---
title: "AgenticOS vs Microsoft Agent 365"
seo_title: "AgenticOS vs Microsoft Agent 365: управляйте агентами сами"
description: "Agent 365 — плоскость управления агентами в Microsoft 365 от Microsoft. Сравните её с AgenticOS: собирайте, запускайте агентов и управляйте ими на своих серверах."
source_sha: "c7529954399e"
---

# AgenticOS vs Microsoft Agent 365 { #agenticos-vs-microsoft-agent-365 }

Microsoft Agent 365 — плоскость управления агентами компании: собственными агентами Microsoft, собранными в Copilot Studio, и некоторыми агентами других вендоров, — с политиками, безопасностью и управлением жизненным циклом в Entra и Purview. AgenticOS — это место, где агентов и собирают, и контролируют: один слой на собственной инфраструктуре, где вместе лежат конструктор, среда выполнения, разрешения, бюджеты и журнал аудита.

Если ваша компания работает на Microsoft 365 и хочет учесть и проконтролировать агентов, которые у неё уже есть, Agent 365 подходит для такого парка агентов. Если вам нужны сами агенты и правила над ними на инфраструктуре, которой вы управляете, и с любой моделью, AgenticOS делает и то и другое в одном месте.

Страницу ведёт команда AgenticOS. Источники проверены 21 октября 2026 года. Базовая версия AgenticOS: v1.10.0. Что проверено у Agent 365: общая доступность у Microsoft около 1 мая 2026 года по сообщениям прессы, а не протестированный клиент. О сборке агентов в стеке Microsoft см. [AgenticOS vs Copilot Studio](copilot-studio.md).

## Коротко { #at-a-glance }

| Область | Microsoft Agent 365 | AgenticOS |
| --- | --- | --- |
| Что вы получаете | Плоскость управления агентами, собранными в других местах | Конструктор, среду выполнения и управление в одном слое |
| Где работает | Облако Microsoft | Ваша инфраструктура, с Docker Compose |
| Исходный код | Проприетарный | Apache-2.0 |
| Идентификация | Microsoft Entra ID | Единый вход OIDC, LDAP и Kerberos, включая Entra ID |
| Политики и аудит | Purview, предотвращение утечки данных, жизненный цикл | Роли и выданные доступы на отдельные ресурсы, согласования, бюджеты перед каждым запросом к модели, журнал аудита с защитой от подмены |
| Модели | Через агентов, которыми управляет | 28 провайдеров, включая локальные модели |
| Цена | Лицензирование Microsoft 365 | Без платы за лицензию; вы платите провайдерам моделей и за инфраструктуру |

## Где AgenticOS идёт дальше { #where-agenticos-goes-further }

### Управление там, где работает агент { #governance-where-the-agent-runs }

Плоскость управления наблюдает за агентами, которые работают в другом месте. AgenticOS [проверяет бюджет](../governance.md#enforcement-is-before-the-request) и [согласование](../governance.md#approvals) в том же процессе, который вызывает модель и инструмент, поэтому правило применяется в момент, когда оно важно, а не сообщается задним числом.

### Вне облака одного вендора { #outside-one-vendors-cloud }

Agent 365 живёт в облаке Microsoft и в его лицензировании. AgenticOS работает [там, где вы его развернёте](../deploy.md), выполняет вход через ваш [каталог](../directory.md#signing-in-with-a-directory-account) — включая Entra ID — и работает с [любым из 28 провайдеров моделей](../models.md#providers).

### Открыто для чтения и расширения { #open-to-read-and-extend }

AgenticOS распространяется по Apache-2.0. Аудитор может прочитать, как применяется правило, а инженер — [добавить возможность](../howto/add-capability.md) на типизированном Python.

## Когда Agent 365 достаточно { #when-agent-365-is-enough }

- Ваша компания сделала Microsoft 365 стандартом, а агенты собраны в Copilot Studio или продуктах Microsoft.
- Вам нужен единый перечень агентов по инструментам Microsoft с политиками Purview.
- Microsoft Teams — ваш основной канал сегодня; у AgenticOS пока нет канала Teams.

## Частые вопросы { #frequently-asked-questions }

### AgenticOS — альтернатива Microsoft Agent 365? { #is-agenticos-an-alternative-to-microsoft-agent-365 }

Для компании, которая хочет собирать, запускать агентов и управлять ими в одном месте, которое она контролирует, — да. Agent 365 управляет агентами по всему парку агентов Microsoft; AgenticOS — слой, который их собирает и запускает, и правила находятся там же.

### Может ли AgenticOS выполнять вход через Microsoft Entra ID? { #can-agenticos-sign-in-with-microsoft-entra-id }

Да. AgenticOS выполняет вход по OIDC, а Entra ID — провайдер OIDC; группы каталога сопоставляются с ролями.

## Другие сравнения { #related-comparisons }

[AgenticOS vs Copilot Studio](copilot-studio.md) · [AgenticOS vs OpenAI Frontier](openai-frontier.md) · [AgenticOS vs Gemini Enterprise](gemini-enterprise.md) · [Все сравнения](comparison.md)

## Источники { #sources }

- [Help Net Security: обновления безопасности и управления Copilot Studio](https://www.helpnetsecurity.com/2026/05/14/copilot-studio-security-governance-updates/): Agent 365 как плоскость управления, политики, безопасность и жизненный цикл.
