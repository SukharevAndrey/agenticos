---
title: "AgenticOS vs Copilot Studio"
seo_title: "AgenticOS vs Copilot Studio: альтернатива на собственной инфраструктуре"
description: "Сравните Microsoft Copilot Studio с AgenticOS: без Copilot Credits, любой провайдер моделей, собственная инфраструктура, бюджеты для каждого агента и открытый аудит."
source_sha: "c3f329d888d0"
---

# AgenticOS vs Copilot Studio { #agenticos-vs-copilot-studio }

Microsoft Copilot Studio создаёт агентов внутри Power Platform. В Copilot Studio есть коннекторы Power Platform, публикация в Teams и Microsoft 365, управление через Purview и Entra — всё в облаке Microsoft и с оплатой в Copilot Credits. AgenticOS создаёт агентов на вашей собственной инфраструктуре, с любым провайдером моделей, и не берёт собственной платы: вы платите провайдеру за модель.

Если ваша компания живёт в Microsoft 365, Copilot Studio — естественный кандидат. AgenticOS — кандидат, когда вы хотите владеть платформой, держать данные там, где выберете, и обойтись без счётчика за каждую функцию.

Страницу ведёт команда AgenticOS. Источники проверены 21 октября 2026 года. Базовая версия AgenticOS: v1.10.0. Что проверено у Copilot Studio: страница цен Microsoft, API розничных цен Azure и Microsoft Learn, а не протестированный клиент.

## Коротко { #at-a-glance }

| Область | Copilot Studio | AgenticOS |
| --- | --- | --- |
| Где работает | Облако Microsoft, в средах Power Platform | Ваша инфраструктура |
| Исходный код | Проприетарный | Apache-2.0 |
| Модели | По умолчанию модели GPT, модели Claude общедоступны, модели Azure Foundry оплачиваются отдельно | 28 провайдеров, включая локальных |
| Оплата | $0.01 за Copilot Credit при оплате по факту или предоплаченные Copilot Credit Commit Units со скидкой до 20% | Без платы за лицензию; использование моделей по тарифам вашего провайдера |
| Как считается использование | Кредиты за функцию: генеративный ответ — 2, действие агента — 5, опора на граф данных клиента — 10 | Токены модели по цене провайдера |
| Контроль расходов | Месячные лимиты для каждого агента; агент отключается при 125% предоплаченного объёма | Бюджет для каждого агента и каждой организации, проверяемый перед каждым запросом к модели |
| Способы запуска | Teams, Microsoft 365, SharePoint, веб, WhatsApp, голос, а также Slack или Telegram через Azure Bot Service | Веб-чат с голосовыми звонками, виджет, размещённая страница, HTTP API, WebSocket, Slack, Telegram, Mattermost |
| Идентификация | Microsoft Entra ID | OIDC SSO, включая Entra, LDAP и Kerberos |
| Управление | Политики данных Power Platform, аудит Purview, Entra Agent ID | Каталог разрешений, выданные доступы, согласования и журнал аудита с защитой от подмены |

## Где AgenticOS идёт дальше { #where-agenticos-goes-further }

### Счёт, который можно предсказать по цене модели { #a-bill-you-can-predict-from-the-model-price }

Copilot Studio считает каждую функцию в кредитах, добавляет повышенный тариф для моделей рассуждения и берёт плату за новую среду агента GitHub Copilot с момента, когда вы начинаете строить. AgenticOS записывает собственную стоимость модели при [каждом запуске](../governance.md#what-run-history-shows) по снимку цен, входящему в поставку. Модель, появившаяся после снимка, записывается как [частично оценённая](../models.md#what-a-run-costs), а самостоятельно размещённый провайдер без ключа не записывает расходов. [Бюджет](../governance.md#budgets) агента проверяется [перед каждым запросом к модели](../governance.md#enforcement-is-before-the-request), а не отключает агента, когда запас исчерпан.

### Любое облако — или никакого { #any-cloud-or-none }

Copilot Studio работает в облаке Microsoft. Microsoft отмечает, что модели Anthropic находятся за пределами Границы данных ЕС (EU Data Boundary) и по умолчанию отключены в ЕС, ЕАСТ и Великобритании. AgenticOS работает там, где вы его развернёте. Вы можете выбрать провайдера в нужном регионе или [запустить модель у себя](../models.md#self-hosted), чтобы промпты не покидали вашу сеть.

### Каждый провайдер моделей — полноправный { #every-model-provider-first-class }

Стандартная среда агента Copilot Studio предлагает модели GPT и Claude, а Azure Foundry — для собственных моделей, с отдельной оплатой. AgenticOS относится к [28 провайдерам](../models.md#providers) одинаково. У них один формат [профиля модели](../models.md#a-model-profile), одинаковые [резервные модели](../models.md#fallbacks) и одинаковая запись стоимости.

### Открыто там, где это важно аудитору { #open-where-it-matters-to-an-auditor }

Управление в Copilot Studio надёжно внутри стека Microsoft. AgenticOS позволяет прочитать сами механизмы контроля: [каталог разрешений](../permissions.md), [хранилище секретов](../secrets.md#envelope-encryption), [цепочку хешей аудита](../governance.md#audit) и [тесты отказов](../security.md#the-refusals-as-a-set) в CI. Спецификация [экспортируется в YAML](../features.md#exportable-into-your-own-repository), поэтому уход означает, что ваши агенты остаются с вами, а не что их придётся собирать заново.

### Идентификация Microsoft без хостинга Microsoft { #microsoft-identity-without-microsoft-hosting }

AgenticOS выполняет вход через Entra ID по [OIDC](../configuration.md#single-sign-on-generic-oidc), сопоставляет [группы каталога](../directory.md#the-groups-claim-over-oidc) с ролями и читает файлы из [SharePoint и OneDrive](../howto/configure-sync-sources.md#sharepoint-and-onedrive-setup). Агенты также могут работать в Microsoft 365 через [каталог MCP](../mcp.md#communication-support-knowledge): самостоятельно размещённый сервер Microsoft 365 для почты и календаря Outlook и размещённый сервер Work IQ от Microsoft. Microsoft остаётся источником идентификации и документов, а агентов вы запускаете сами.

## Когда Copilot Studio подходит лучше { #when-copilot-studio-is-the-better-fit }

- Ваши агенты должны жить в Teams и Microsoft 365 Copilot, а у пользователей уже есть лицензии Microsoft 365 Copilot.
- Вам нужны коннекторы Power Platform, потоки агентов, телефонный голос или WhatsApp. У AgenticOS нет канала Teams, телефонии или WhatsApp; его [голосовые звонки](../voice.md) идут в браузере.
- Purview, Sentinel и политики данных Power Platform — то, чем ваша организация управляет всем остальным.

## Попробуйте на одной задаче { #try-it-on-one-task }

Соберите [общего агента по документам](../howto/first-document-agent.md) в обоих продуктах на сопоставимых моделях. Задайте сто вопросов и сравните счёт: кредиты с одной стороны, стоимость модели с другой. Выберите модель, которую покрывает снимок цен, чтобы цифра в AgenticOS была полной. Затем проверьте, что происходит при достижении лимита. Запишите результат по [методике сравнения](comparison.md#a-shared-trial).

## Частые вопросы { #frequently-asked-questions }

### AgenticOS — альтернатива Microsoft Copilot Studio? { #is-agenticos-an-alternative-to-microsoft-copilot-studio }

Да, если вы хотите владеть платформой. AgenticOS работает на вашей инфраструктуре с любым провайдером моделей и без счётчика кредитов. Copilot Studio подходит лучше, когда агенты живут в Teams и Microsoft 365.

### AgenticOS работает с Microsoft Entra ID и SharePoint? { #does-agenticos-work-with-microsoft-entra-id-and-sharepoint }

Да. Люди входят через Entra ID по OIDC, группы каталога сопоставляются с ролями, а коллекции синхронизируют файлы из SharePoint и OneDrive.

### Сколько стоит Copilot Studio по сравнению с AgenticOS? { #how-much-does-copilot-studio-cost-compared-with-agenticos }

Copilot Studio берёт $0.01 за Copilot Credit при оплате по факту или продаёт предоплаченные Copilot Credit Commit Units со скидкой до 20%. У AgenticOS нет собственной платы; вы платите своему провайдеру моделей за токены, которые использует агент.

### Может ли AgenticOS публиковать агентов в Microsoft Teams? { #can-agenticos-publish-agents-to-microsoft-teams }

Пока нет. Он публикует в веб-чат, виджет, размещённую страницу, HTTP API, WebSocket, Slack, Telegram и Mattermost.

## Другие сравнения { #related-comparisons }

[AgenticOS vs Gemini Enterprise](gemini-enterprise.md) · [AgenticOS vs ChatGPT](chatgpt.md) · [AgenticOS vs n8n](n8n.md) · [Все сравнения](comparison.md)

## Источники { #sources }

- [Цены Copilot Studio](https://www.microsoft.com/en-us/microsoft-365-copilot/pricing/copilot-studio): план предоплаты и оплата по факту.
- [Розничные цены Azure](https://prices.azure.com/api/retail/prices?$filter=contains(productName,'Copilot%20Studio')): $0.01 за кредит.
- [Тарифы и управление оплатой](https://learn.microsoft.com/en-us/microsoft-copilot-studio/requirements-messages-management): кредиты за функцию, лимиты для каждого агента и отключение при 125%.
- [Среды агента](https://learn.microsoft.com/en-us/microsoft-copilot-studio/harnesses-overview) и [оплата сред агента](https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/billing-credit-overview): плата с момента сборки.
- [Выбор модели](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-select-agent-model): доступные модели.
- [Anthropic как субпроцессор](https://learn.microsoft.com/en-us/microsoft-365/copilot/connect-to-ai-subprocessor): исключение из Границы данных ЕС.
- [Каналы публикации](https://learn.microsoft.com/en-us/microsoft-copilot-studio/publication-fundamentals-publish-channels): способы запуска и аутентификация.
