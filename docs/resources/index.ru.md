---
source_sha: "322ecdc03ee2"
---

# Ресурсы { #resources }

Всё вокруг продукта, а не внутри него: как получить помощь, как внести вклад и как
расширить платформу.

## Помогите AgenticOS { #help-agenticos }

Проект молод, и быстрее всего помочь ему — пользоваться им и говорить, что сломалось.

<div class="grid cards" markdown>

- :material-bug:{ .lg .middle } **[Сообщить об ошибке](https://github.com/vstorm-co/agenticos/issues/new)**

    Что ломается, последовательность, которая это вызывает, и как вы поймёте, что это
    исправлено. Именно третьего чаще всего не хватает в задачах.

- :material-lightbulb-on:{ .lg .middle } **[Предложить идею](https://github.com/vstorm-co/agenticos/issues)**

    Сначала загляните в [карту задач](https://github.com/vstorm-co/agenticos/issues/168) —
    она говорит, что уже запланировано, а что ещё вне рамок.

- :material-star:{ .lg .middle } **[Поставить звезду репозиторию](https://github.com/vstorm-co/agenticos)**

    Это самый дешёвый сигнал, что продолжать стоит.

</div>

## Разработка — как внести вклад { #development-contributing }

Прочитайте страницу, которая соответствует тому, что вы трогаете. Это собственные
инженерные заметки репозитория: они публикуются, а не переписываются, поэтому вы читаете
то же, что и участники.

| Над чем работаете | Что читать |
|---|---|
| Что угодно, сначала | [Архитектура](../architecture.md) · [Код консоли](../frontend.md) — маршруты → сервисы → репозитории и транзакция запроса |
| Знакомый образец | [Паттерны](../patterns.md) |
| Функция целиком | [Добавление функций](../adding_features.md) |
| Тест или красный порог покрытия | [Тестирование](../testing.md) |
| Пул-реквест | [Проверка кода](../code-review.md) и [Ветвление](../branching.md) |

!!! warning "Три вещи, которые вас укусят"

    Репозиторий фиксирует изменения через `db.flush()` и никогда через `db.commit()`. Фоновая
    работа, которая читает строку, только что записанную запросом, передаётся через
    `spawn_after_commit`, а не `spawn`. А платформенный слой держится на 100 % покрытия,
    и это проверяется в CI. Все три правила есть в [Архитектуре](../architecture.md) и
    [Тестировании](../testing.md), и все три здесь уже хотя бы раз нарушали.

## Расширение платформы { #extending-the-platform }

Добавление того, что умеет сам AgenticOS, на Python. Это рецепты для участников — вам
понадобится склонированный репозиторий.

- [Добавить возможность](../howto/add-capability.md) — новый инструмент, который модель может вызвать
- [Добавить сервер в каталог MCP](../howto/add-mcp-server.md)
- [Добавить эндпоинт API](../howto/add-api-endpoint.md)
- [Добавить фоновую задачу](../howto/add-background-task.md)
- [Добавить коннектор синхронизации](../howto/add-sync-connector.md)

!!! tip "Прежде чем писать возможность"

    Спросите себя, не является ли она на самом деле [подключением MCP](../mcp.md).
    Возможность, которая была бы API-клиентом для одного SaaS-продукта, — это сервер,
    который кто-то уже написал, и принять его в этот репозиторий значит вечно
    поддерживать его под API этого продукта.

## На чём построено { #built-on }

AgenticOS сгенерирован из
[Full-Stack AI Agent Template](https://github.com/vstorm-co/full-stack-ai-agent-template)
и опирается на:

- [Pydantic AI](https://ai.pydantic.dev) — среда выполнения агентов
- [FastAPI](https://fastapi.tiangolo.com) и [Pydantic](https://docs.pydantic.dev) — бэкенд
- [pgvector](https://github.com/pgvector/pgvector) — поиск по знаниям
- [Next.js](https://nextjs.org) — консоль
- [Model Context Protocol](https://modelcontextprotocol.io) — каждая интеграция

## Лицензия { #licence }

Apache 2.0. См.
[`LICENSE`](https://github.com/vstorm-co/agenticos/blob/main/LICENSE).
