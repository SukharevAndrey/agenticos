---
source_sha: "4ad2bb97f109"
---

# Добавьте эндпоинт API { #add-an-api-endpoint }

Этот пример добавляет эндпоинт «Notification» от начала до конца, следуя тому же
разделению на слои, что и каждый домен здесь.

!!! important "Маршруты → Сервисы → Репозитории, и никаких обходных путей"

    Маршрут проверяет, делегирует и возвращает. **Маршрут никогда не импортирует
    репозиторий**, а репозиторий никогда не содержит бизнес-логику. См.
    [Архитектура](../architecture.md).

## По шагам { #step-by-step }

### 1. Создайте схему (`app/schemas/`) { #1-create-the-schema-appschemas }

```python
# app/schemas/notification.py
from datetime import datetime
from uuid import UUID

from pydantic import Field

from app.schemas.base import BaseSchema


class NotificationCreate(BaseSchema):
    title: str = Field(max_length=255)
    body: str
    channel: str = "email"


class NotificationRead(BaseSchema):
    id: UUID
    title: str
    body: str
    channel: str
    is_read: bool
    created_at: datetime


class NotificationList(BaseSchema):
    items: list[NotificationRead]
    total: int
```

Одна схема на операцию — `*Create`, `*Update` (все поля необязательны), `*Read`
(с `id` и отметками времени) и `*List` (`items` плюс `total`). Правило — в разделе
[Схемы и модели](../architecture.md).

### 2. Создайте модель базы данных (`app/db/models/`) { #2-create-the-database-model-appdbmodels }

```python
# app/db/models/notification.py
import uuid

from sqlalchemy import Boolean, String
from sqlalchemy.dialects.postgresql import UUID
from sqlalchemy.orm import Mapped, mapped_column

from app.db.base import Base, TimestampMixin


class Notification(Base, TimestampMixin):
    """One notification sent to somebody, and whether they have read it."""

    __tablename__ = "notifications"

    id: Mapped[uuid.UUID] = mapped_column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    title: Mapped[str] = mapped_column(String(255), nullable=False)
    body: Mapped[str] = mapped_column(String, nullable=False)
    channel: Mapped[str] = mapped_column(String(50), default="email", nullable=False)
    is_read: Mapped[bool] = mapped_column(Boolean, default=False, nullable=False)

    def __repr__(self) -> str:
        return f"<Notification(id={self.id}, title={self.title})>"
```

`TimestampMixin` даёт `created_at` и `updated_at`, поэтому здесь ни то ни другое не
объявляется. `__repr__` не необязателен: у каждой модели в этой кодовой базе он
есть. Импортируйте модель в `app/db/models/__init__.py`, иначе Alembic её не
увидит.

### 3. Создайте репозиторий (`app/repositories/`) { #3-create-the-repository-apprepositories }

```python
# app/repositories/notification.py
from uuid import UUID

from sqlalchemy import func, select
from sqlalchemy.ext.asyncio import AsyncSession

from app.db.models.notification import Notification


async def get_by_id(db: AsyncSession, notification_id: UUID) -> Notification | None:
    return await db.get(Notification, notification_id)


async def create(db: AsyncSession, *, title: str, body: str, channel: str) -> Notification:
    notification = Notification(title=title, body=body, channel=channel)
    db.add(notification)
    await db.flush()
    await db.refresh(notification)
    return notification


async def list_unread(db: AsyncSession, limit: int = 50) -> list[Notification]:
    result = await db.execute(
        select(Notification)
        .where(Notification.is_read.is_(False))
        .order_by(Notification.created_at.desc())
        .limit(limit)
    )
    return list(result.scalars().all())


async def count_unread(db: AsyncSession) -> int:
    result = await db.execute(
        select(func.count()).select_from(Notification).where(Notification.is_read.is_(False))
    )
    return result.scalar_one()
```

!!! warning "Страничному списку нужен настоящий счётчик, а не `len(items)`"

    `total` у `*List` — сколько строк **подходит**, и по нему клиент листает
    страницы. `len(items)` — сколько вернулось; они равны только до заполнения
    первой страницы, а дальше тихо расходятся в сторону, которая прячет строки.

!!! danger "`flush()` + `refresh()`, никогда `commit()`"

    Сеанс запроса фиксирует изменения один раз, после возврата маршрута и *до*
    записи ответа — и именно поэтому ответ 2xx означает, что запись уже читается
    ([#353](https://github.com/vstorm-co/agenticos/issues/353)). Репозиторий,
    который фиксирует сам, отнимает этот порядок у единственного места, которому
    он принадлежит.

Репозиторий — это **модуль функций без состояния**, а не класс: `db` идёт первым,
все остальные аргументы — только именованные, а возвращается сущность, а не
идентификатор или словарь. Реэкспортируйте его из `app/repositories/__init__.py`, как любой другой, —
`from app.repositories import notification as notification_repo`, — чтобы
вызывающие импортировали псевдоним, а не путь модуля.

### 4. Создайте сервис (`app/services/`) { #4-create-the-service-appservices }

```python
# app/services/notification.py
from uuid import UUID

from sqlalchemy.ext.asyncio import AsyncSession

from app.core.exceptions import NotFoundError
from app.db.models.notification import Notification
from app.repositories import notification_repo
from app.schemas.notification import NotificationCreate


class NotificationService:
    def __init__(self, db: AsyncSession):
        self.db = db

    async def create(self, data: NotificationCreate) -> Notification:
        return await notification_repo.create(
            self.db, title=data.title, body=data.body, channel=data.channel
        )

    async def get_or_raise(self, notification_id: UUID) -> Notification:
        notification = await notification_repo.get_by_id(self.db, notification_id)
        if not notification:
            raise NotFoundError(
                message="Notification not found",
                details={"notification_id": notification_id},
            )
        return notification

    async def list_unread(self) -> tuple[list[Notification], int]:
        items = await notification_repo.list_unread(self.db)
        return items, await notification_repo.count_unread(self.db)
```

Сервис держит сеанс и больше ничего; репозитории импортируются как модули. Он же
**единственный** слой, который выбрасывает доменное исключение, и `details` несёт
само значение, а не его строку: обработчик кодирует через `jsonable_encoder`.

### 5. Зарегистрируйте зависимость (`app/api/deps.py`) { #5-register-the-dependency-appapidepspy }

```python
from app.services.notification import NotificationService


def get_notification_service(db: DBSession) -> NotificationService:
    return NotificationService(db)


NotificationSvc = Annotated[NotificationService, Depends(get_notification_service)]
```

### 6. Создайте маршрут (`app/api/routes/v1/`) { #6-create-the-route-appapiroutesv1 }

```python
# app/api/routes/v1/notifications.py
from typing import Any

from fastapi import APIRouter, status

from app.api.deps import CurrentUser, NotificationSvc
from app.schemas.notification import NotificationCreate, NotificationList, NotificationRead

router = APIRouter()


@router.post("", response_model=NotificationRead, status_code=status.HTTP_201_CREATED)
async def create_notification(
    data: NotificationCreate, service: NotificationSvc, user: CurrentUser
) -> Any:
    return await service.create(data)


@router.get("", response_model=NotificationList)
async def list_unread(service: NotificationSvc, user: CurrentUser) -> Any:
    items, total = await service.list_unread()
    return NotificationList(items=items, total=total)
```

!!! note "`-> Any` — так здесь принято"

    `response_model` сериализует и проверяет ответ, а каждый маршрут в этой
    кодовой базе оставляет аннотацию `Any`, чтобы на вопрос «где объявлена форма
    ответа» был один ответ, а не два, которые могут разойтись. Следуйте этому
    ради единообразия с окружающим кодом, а не потому, что аннотация стоила бы
    второго прохода проверки: она не стоит.

Всё, что относится к организации, берёт разрешение из каталога на **маршруте
коллекции** — `dependencies=[Depends(require(Perm.X))]`, — а маршрут одного
ресурса передаёт решение сервису, вызывающему `resolve_access`. См.
[Разрешения](../permissions.md).

### 7. Зарегистрируйте маршрутизатор { #7-register-the-router }

В `app/api/routes/v1/__init__.py`:

```python
from app.api.routes.v1 import notifications

v1_router.include_router(
    notifications.router, prefix="/notifications", tags=["notifications"]
)
```

### 8. Создайте и примените миграцию { #8-create-and-apply-the-migration }

```bash
make db-migrate    # message: "Add notifications table"
make db-upgrade
make db-check      # a model change with no migration fails here, and in `make check`
```

### 9. Протестируйте { #9-test-it }

Маршрут выходит вместе с тестами, а не получает их потом: один тест на то, что
проверка доступа подключена (`tests/api/`), по одному на каждую ветку сервиса,
включая отказ, и интеграционный тест, если вы на самом деле меняли ограничение или
каскад. Затем `http://localhost:8000/docs`, чтобы попробовать вручную.
