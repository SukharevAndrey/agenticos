---
source_sha: "5f2679674e19"
---

# Паттерны кода { #code-patterns }

Формы, которые повторяются в этой кодовой базе. Если изменение, которое вы пишете,
не похоже ни на одну из них, стоит присмотреться к нему ещё раз — прежде чем
заводить новый паттерн.

## Внедрение зависимостей { #dependency-injection }

Всё, что нужно маршруту, приходит как псевдоним `Annotated` из `app/api/deps.py` —
никогда голый `Depends()` в сигнатуре:

```python
from app.api.deps import ConversationSvc, CurrentUser


@router.get("", response_model=ConversationList)
async def list_conversations(service: ConversationSvc, user: CurrentUser) -> Any:
    items, total = await service.list(user_id=user.id)
    return ConversationList(items=items, total=total)
```

!!! important "Маршруты никогда не обращаются к базе данных напрямую"

    Весь доступ к данным идёт через сервис, который, в свою очередь, передаёт
    работу репозиторию. Маршрут проверяет, делегирует и возвращает.

Псевдонимы, которые стоит знать, — все в `app/api/deps.py`:

| Псевдоним | |
|---|---|
| `DBSession` | Сессия запроса. `scope="function"` — именно это фиксирует транзакцию до записи ответа |
| `StreamingDBSession` | Та же сессия с `scope="request"` — для маршрута, который отдаёт тело потоком |
| `CurrentUser` | Аутентифицированный пользователь; без него — 401 |
| `CurrentAppAdmin` | Администратор платформы |
| `Auth` | `AuthContext`: вызывающий, организация, набор разрешений |
| `Redis` | Клиент Redis |
| `<Domain>Svc` | По одному на сервис, строится из `DBSession` |

## Паттерн слоя сервисов { #service-layer-pattern }

Каждая функция использует один и тот же паттерн: класс сервиса получает сессию БД
и предоставляет методы уровня бизнес-логики. Сервисы — **единственный** слой,
который выбрасывает доменные исключения.

```python
from app.repositories import conversation_repo


class ConversationService:
    def __init__(self, db: AsyncSession):
        self.db = db

    async def create(self, data: ConversationCreate, ctx: AuthContext) -> Conversation:
        return await conversation_repo.create_conversation(
            self.db,
            organization_id=ctx.organization_id,
            user_id=ctx.user_id,
            title=data.title,
        )

    async def get_conversation(self, conversation_id: UUID, *, organization_id: UUID) -> Conversation:
        conversation = await conversation_repo.get_conversation_by_id(self.db, conversation_id)
        missing = NotFoundError(
            message="Conversation not found",
            details={"conversation_id": conversation_id},
        )
        if not conversation:
            raise missing
        if conversation.organization_id != organization_id:
            raise missing
        return conversation
```

Проверка принадлежности к организации выбрасывает **тот же отказ**, что и
отсутствующая строка: «вам нельзя это читать» сообщает человеку из другой
организации, что такой идентификатор существует.

Сервис держит сессию и ничего больше: репозитории импортируются как модули, а не
создаются как экземпляры, поэтому нет графа объектов на запрос, который нужно
поддерживать согласованным. Там, где у домена есть собственная инфраструктура
(клиенты, адаптеры, парсеры), сервис становится подпакетом, экспортирующим один
фасад: `services/rag/`, `services/channels/`, `services/email/`.

## Паттерн слоя репозиториев { #repository-layer-pattern }

Репозитории занимаются только доступом к данным. В них **нет** бизнес-логики, и
они всегда используют `flush()` вместо `commit()`, потому что транзакцией владеет
сессия запроса и фиксирует её один раз — после возврата маршрута и *до* записи
ответа; именно поэтому 2xx означает, что запись уже читается. См.
[транзакцию запроса](architecture.md#the-requests-transaction).

Репозиторий — это **модуль функций без состояния**, а не класс: `db` первым
аргументом, всё после него — только по имени:

```python
# app/repositories/conversation.py

async def create_conversation(
    db: AsyncSession,
    *,
    organization_id: UUID,
    user_id: UUID | None = None,
    title: str | None = None,
) -> Conversation:
    conversation = Conversation(
        user_id=user_id, organization_id=organization_id, title=title
    )
    db.add(conversation)
    await db.flush()
    await db.refresh(conversation)
    return conversation


async def get_conversations_by_user(
    db: AsyncSession,
    user_id: UUID | None = None,
    *,
    organization_id: UUID,
    skip: int = 0,
    limit: int = 50,
) -> list[Conversation]:
    query = select(Conversation).where(Conversation.organization_id == organization_id)
    if user_id:
        query = query.where(Conversation.user_id == user_id)
    result = await db.execute(query.offset(skip).limit(limit))
    return list(result.scalars().all())
```

В этой сигнатуре важны две вещи. У `organization_id` намеренно нет значения по
умолчанию нигде в этом модуле: каждый диалог принадлежит организации, а у
вызывающего, который не может её назвать, ошибка, а не значение по умолчанию. И
сужающий аргумент, который принят, должен быть **применён** — запрос, который
принимает `user_id`, а фильтрует только по организации, отвечает диалогами всех
участников. (Настоящий модуль расширяет предикат пользователя до подтверждённых
участников каналов через `_reachable_by`, по идентификаторам, которые вызывающий
уже сверил с платформой; здесь важно то, что аргумент вообще доходит до
предложения `WHERE`.)

!!! danger "`flush()`, а не `commit()`"

    Сессия запроса фиксирует транзакцию один раз. Единственное разрешённое
    исключение — путь запуска агента, который фиксирует перед вызовом модели и ещё
    раз в завершающем `finally`.

## Обработка исключений { #exception-handling }

Используйте доменные исключения в сервисах:

```python
from app.core.exceptions import NotFoundError, AlreadyExistsError, ValidationError

# In service
if not conversation:
    raise NotFoundError(
        message="Conversation not found",
        details={"id": id}
    )

if await user_repo.get_by_email(self.db, email):
    raise AlreadyExistsError(
        message="User with this email already exists"
    )
```

Обработчики исключений автоматически преобразуют их в HTTP-ответы, а `details`
кодируется через `jsonable_encoder` — тем же кодировщиком, который использует
`response_model`, — поэтому тот, кто выбрасывает исключение, передаёт значение,
которое у него есть, а не его строку. `UUID` приходит в строковой форме,
`datetime` — в ISO 8601, `Enum` — своим значением. Деньги — исключение, о котором
стоит знать: `Decimal` кодируется в число с плавающей точкой, поэтому код,
выбрасывающий исключение, сам приводит стоимость или лимит к строке.

!!! warning "Отказ описывает отказ, а не сервер"

    Всё в `details` читает тот, кому отказали, поэтому там называется поле,
    идентификатор или ресурс, с которым он может что-то сделать, — никогда путь в
    файловой системе, текст исключения вышестоящего клиента или настройка,
    описывающая экземпляр. Диагностика не удаляется: она переходит в строку
    журнала рядом с местом, где исключение выброшено.

`max_mb` и `seats_limit` — ровно то, с чем вызывающий может что-то сделать; а где
контейнер держит свои шаблоны — нет. Путь, который искал загрузчик, и сообщение
SDK вендора идут в строку журнала рядом с местом выброса: там их читает оператор,
а вызывающий — нет.

```python
except Exception as exc:
    logger.exception("Knowledge base search failed")   # the upstream text stays here
    raise ExternalServiceError(
        message="Knowledge base search failed",
        details={"collections": names, "operation": "retrieve"},
    ) from exc
```

К `message` предъявляется та же планка: его несёт конверт, и обработчик пишет его
в журнал в той же строке, поэтому фраза, в которой назван эндпоинт, раскрывает
именно то, что поле не должно было содержать. URL, *из-за которого* отказано,
называется своим полем:
`refused_field("base_url", ...)`, а не адресом, в котором всё ещё стоит пароль.
Этот помощник лежит в `app/core/field_errors.py` — единственном месте, где
строится `details["fields"]`, по которому форма помечает поле ввода; о трёх точках
входа и о том, какие отказы намеренно не называют поле вовсе, см.
[Архитектуру](architecture.md#a-refusal-that-names-a-field).

То же относится к записи аудита, которая есть `details` с более долгой жизнью:
записывайте, *какие* поля изменил администратор, а не значения, которые он
отправил.

## Паттерны схем { #schema-patterns }

Отдельные схемы для разных операций:

```python
# Base with shared fields
class UserBase(BaseModel):
    email: str
    full_name: str | None = None

# For creation (input)
class UserCreate(UserBase):
    password: str

# For updates (all optional)
class UserUpdate(BaseModel):
    full_name: str | None = None
    email: str | None = None

# For responses (with DB fields)
class UserResponse(UserBase):
    id: UUID
    created_at: datetime
    updated_at: datetime | None

    model_config = ConfigDict(from_attributes=True)
```

### Обновление записывается через `writable`, а не выгрузкой { #an-update-is-written-through-writable-never-dumped }

В `*Update` каждое поле имеет тип `X | None`, потому что `None` означает *не
передано*, — а `model_dump(exclude_unset=True)` сохраняет поле, которое **явно
установили в `None`**, ведь `exclude_unset` спрашивает именно о том,
устанавливали ли поле. Поэтому клиент, отправивший `{"name": null}`, проводит свой
`None` мимо выгрузки, в `setattr` и дальше в столбец `NOT NULL`: 500 с именем
ограничения базы данных — на запрос, который, по собственным типам API, допустим.

```python
from app.db.updates import writable

changes = writable(data, over=AgentEmbed)      # not data.model_dump(exclude_unset=True)
```

**Решает столбец.** `writable` читает допустимость `NULL` из модели, поэтому
схема, получившая необязательное поле, покрыта в тот же день, когда его получила,
— а список имён полей, который ведут руками в каждом сервисе, — это новое падение
в следующий раз, когда кто-то добавит поле. До #637 таких пар было двадцать четыре
в одиннадцати схемах.

`null`, который столбец *допускает*, сохраняется — этим это и отличается от
`exclude_none`: очистить столбец, допускающий `NULL`, — законный запрос, а
отбросить все `null` значило бы, что «удалить описание» молча ничего не делает.
Там, где у поля есть значение по умолчанию, к которому стоит вернуться, сервис
подставляет его перед вызовом: `EmbedUpdate.config` восстанавливает значения по
умолчанию для этого вида, а не отбрасывает ключ.

`tests/test_update_nulls.py` следит за тем, чтобы это оставалось правдой: каждая
схема `*Update` объявлена против строки, в которую она пишет, и ни один сервис не
может выгрузить её сам.

## Передача работы в фон { #handing-work-to-the-background }

Два примитива в `app/core/background.py`, и выбор между ними зависит от того, что
читает работа, а не от того, сколько она длится:

```python
from app.core.background import spawn, spawn_after_commit

# Owns everything it needs - a rendered email, an id it will not look up.
spawn(deliver(key, to, context), name=f"email:{key}:{to}")

# Reads a row this unit of work wrote. Starts when the session commits.
spawn_after_commit(self.db, ingest_document_flow(rag_document_id=str(doc.id)), name=...)
```

Оба держат сильную ссылку на задачу и пишут в журнал всё, что она выбросит, —
голый `asyncio.create_task` не делает ни того, ни другого. `spawn_after_commit`
вдобавок ставит корутину в очередь сессии, поэтому ничего не начнётся, пока не
зафиксирована транзакция, от которой зависит работа: иначе задача, читающая
собственную строку по идентификатору, работала бы с базой данных, в которой её ещё
нет ([#417](https://github.com/vstorm-co/agenticos/issues/417)). Ни один из них не
переживает перезапуск; работа, которая обязана его пережить, — это задача,
поставленная в очередь через `app.services.background_jobs.enqueue` в той же
транзакции; см. [Добавьте фоновую задачу](howto/add-background-task.md).

## Паттерн коннекторов (синхронизация RAG) { #connector-pattern-rag-sync }

Удалённые источники документов (Google Drive, S3 и т. п.) используют паттерн
подключаемых коннекторов, определённый в `app/services/rag/connectors/`. Каждый
коннектор наследует `BaseSyncConnector` и регистрируется в словаре
`CONNECTOR_REGISTRY`.

### Добавление нового коннектора { #adding-a-new-connector }

1. Создайте файл в `app/services/rag/connectors/` (например, `confluence.py`).
2. Унаследуйте `BaseSyncConnector` и реализуйте обязательные методы.
3. Зарегистрируйте коннектор в `CONNECTOR_REGISTRY`.

```python
from pathlib import Path

from pydantic import BaseModel, Field

from app.core.secret_kinds import SecretKind, StorableSecret
from app.services.rag.connectors import (
    CONNECTOR_REGISTRY,
    BaseSyncConnector,
    ConnectorConfig,
    RemoteFile,
    RemoteListing,
)

class ConfluenceConfig(BaseModel):
    # No default, so the one required field; the wizard draws it from the
    # model's JSON Schema and a refusal names its title.
    space_key: str = Field(title="Space key")

class ConfluenceConnector(BaseSyncConnector):
    CONNECTOR_TYPE = "confluence"
    DISPLAY_NAME = "Confluence"
    # What authenticates it. The credential is a vault secret the source names,
    # unsealed by the caller - never a field of CONFIG_MODEL.
    SECRET_KIND = SecretKind.API_KEY
    CONFIG_MODEL = ConfluenceConfig

    async def list_files(
        self, config: ConnectorConfig, credential: StorableSecret | None
    ) -> RemoteListing:
        # Metadata for the available files, and complete=False if the listing
        # stopped short - the sync removes nothing against a partial one
        ...

    async def _fetch(
        self,
        file: RemoteFile,
        dest_path: Path,
        config: ConnectorConfig,
        credential: StorableSecret | None,
    ) -> None:
        # Write the bytes to dest_path. The base class chose it and confirmed
        # it is inside the sync directory - never build a path from file.name.
        ...

# Register so the sync service can discover it
CONNECTOR_REGISTRY["confluence"] = ConfluenceConnector
```

`RagSyncService` по `CONNECTOR_REGISTRY` находит нужный коннектор по типу,
проверяет его конфигурацию, получает список удалённых файлов, скачивает их и
передаёт в конвейер индексации. После получения полного списка он удаляет документы,
которые источник принёс раньше и больше не перечисляет.

## Паттерны фронтенда { #frontend-patterns }

### Аутентификация (HTTP-only cookies) { #authentication-http-only-cookies }

```typescript
import { useAuth } from '@/hooks/use-auth';

function Component() {
    const { user, isAuthenticated, login, logout } = useAuth();
}
```

### Управление состоянием (Zustand) { #state-management-zustand }

```typescript
import { useAuthStore } from '@/stores/auth-store';

const { user, setUser, logout } = useAuthStore();
```

### Чат по WebSocket { #websocket-chat }

```typescript
import { useChat } from '@/hooks/use-chat';

function ChatPage() {
    const { messages, sendMessage, isStreaming } = useChat();
}
```
