---
source_sha: "12c6776e073b"
---

# Каждый экран консоли { #every-screen-in-the-console }

Актуальные снимки экрана из демонстрационной организации Northwind Labs, сделанные 2 ноября 2026 года в AgenticOS 1.35.3. Данные компании, показатели продаж, поставки и оборудование вымышлены. Подготовленные визуализации помечены; никакой связи с реальными CRM, ERP или станками они не подразумевают. Снимки сохраняют весь экран приложения целиком.

## Куда вы попадаете { #where-you-land }

### Обзор { #dashboard }

Своё представление, настраиваемое представление организации, представление одной группы и — для администратора экземпляра — всего экземпляра; выбор вверху. Запуски, расходы, состояние сервисов и качество ответов; каждая карточка закрыта разрешением, которое нужно её собственным данным, поэтому карточку, основной запрос которой вам недоступен, вам и не предлагают.

![Обзор](assets/screens/light/dashboard.webp?v=20261102-industrial#only-light)
![Обзор](assets/screens/dark/dashboard.webp?v=20261102-industrial#only-dark)

### Чат { #chat }

Подготовленный обзор продаж с Revenue Pulse, отрисованным возможностью «Визуализации». Выручка, планы и вклад команды меняются вместе с фильтрами региона и периода. Диалог и набор данных — заранее подготовленные примеры, а не доказательство того, что эту панель построила модель.

![Чат](assets/screens/light/chat.webp?v=20261102-industrial#only-light)
![Чат](assets/screens/dark/chat.webp?v=20261102-industrial#only-dark)

## Создание агента { #building-an-agent }

### Агенты { #agents }

Каталог. У каждого агента указаны действующая версия, кому он доступен и ждёт ли его черновик. Агент — это конфигурация, а не код, поэтому этот список может править тот, кто знает ответ.

![Агенты](assets/screens/light/agents.webp?v=20261102-industrial#only-light)
![Агенты](assets/screens/dark/agents.webp?v=20261102-industrial#only-dark)

### Шаблоны агентов { #agent-templates }

Шаблоны по отраслям, поверх каталога. Установка создаёт черновик, который вы дорабатываете и публикуете; пока вы этого не сделаете, ничего не запускается.

![Шаблоны агентов](assets/screens/light/agent-templates.webp?v=20261102-industrial#only-light)
![Шаблоны агентов](assets/screens/dark/agent-templates.webp?v=20261102-industrial#only-dark)

### Навыки { #skills }

Практические знания, записанные один раз и общие для каждого привязанного агента: как обрабатываются возвраты, каков фирменный стиль. Отредактируйте навык здесь, и каждый привязанный к нему агент получит актуальную версию при следующем запуске.

![Навыки](assets/screens/light/skills.webp?v=20261102-industrial#only-light)
![Навыки](assets/screens/dark/skills.webp?v=20261102-industrial#only-dark)

### Галерея навыков { #skill-gallery }

Навыки по отраслям. Установка копирует навык в вашу организацию, где его можно редактировать, — это копия, поэтому оригинал не может изменить то, что говорят ваши агенты.

![Галерея навыков](assets/screens/light/skill-gallery.webp?v=20261102-industrial#only-light)
![Галерея навыков](assets/screens/dark/skill-gallery.webp?v=20261102-industrial#only-dark)

### Один навык { #one-skill }

Открыт для редактирования, вместе с категорией. Имя, по которому обращается модель, задаётся при создании и не может измениться; всё остальное здесь — может.

![Один навык](assets/screens/light/skill-detail.webp?v=20261102-industrial#only-light)
![Один навык](assets/screens/dark/skill-detail.webp?v=20261102-industrial#only-dark)

### Контекст { #context }

Постоянный контекст, к которому может обратиться любой агент: глоссарий, политика, фирменный голос. Вставляется в промпт или читается по запросу и становится актуальным сразу после правки.

![Контекст](assets/screens/light/context.webp?v=20261102-industrial#only-light)
![Контекст](assets/screens/dark/context.webp?v=20261102-industrial#only-dark)

## Внутри агента { #inside-one-agent }

Конструктор, вкладка за вкладкой.

### Конструктор { #build }

Инструкции, модель и эндпоинт. Поведение живёт здесь, а не в коде, — в Markdown, который модель читает ради структуры; а в шапке рядом с `Draft differs from agent v40` стоит `published`, и в этом весь смысл: правка ничего не публикует.

![Конструктор](assets/screens/light/agent-builder.webp?v=20261102-industrial#only-light)
![Конструктор](assets/screens/dark/agent-builder.webp?v=20261102-industrial#only-dark)

### Инструменты { #toolbox }

Каждая возможность — переключатель: поиск по знаниям, браузер, Python в песочнице, графики, делегирование; рядом с каждым — согласование для отдельного инструмента. Конфигурация достаёт только до того, что зарегистрировал код.

![Инструменты](assets/screens/light/agent-toolbox.webp?v=20261102-industrial#only-light)
![Инструменты](assets/screens/dark/agent-toolbox.webp?v=20261102-industrial#only-dark)

### MCP-серверы { #mcp-servers }

Какие подключения доступны этому агенту и какие их инструменты. Список организации по-прежнему задаёт границы; агент может сузить их внутри, но не выйти за них.

![MCP-серверы](assets/screens/light/agent-mcp.webp?v=20261102-industrial#only-light)
![MCP-серверы](assets/screens/dark/agent-mcp.webp?v=20261102-industrial#only-dark)

### Лимиты { #limits }

Один месячный лимит бюджета и потолок шагов. Лимит проверяется перед каждым запросом к модели, а ограничение шагов ловит второй вид перерасхода — цикл инструментов, который дёшев за вызов и никогда не заканчивается.

![Лимиты](assets/screens/light/agent-limits.webp?v=20261102-industrial#only-light)
![Лимиты](assets/screens/dark/agent-limits.webp?v=20261102-industrial#only-dark)

### Доступность { #availability }

Где этот агент отвечает: в «Обзоре» и через API всегда, плюс любой чат-бот, привязанный здесь. Упомянуть агента через `@handle` можно только на тех ботах, к которым он привязан.

![Доступность](assets/screens/light/agent-availability.webp?v=20261102-industrial#only-light)
![Доступность](assets/screens/dark/agent-availability.webp?v=20261102-industrial#only-dark)

### Автоматизации в агенте { #routines-on-the-agent }

Что он делает, когда никто не печатает, — на этой же вкладке: расписание, которое можно поставить на паузу, или триггер по событию.

![Автоматизации в агенте](assets/screens/light/agent-routines.webp?v=20261102-industrial#only-light)
![Автоматизации в агенте](assets/screens/dark/agent-routines.webp?v=20261102-industrial#only-dark)

### История { #history }

Каждая версия, которая была у этого агента. Версию, действовавшую в марте, по-прежнему можно прочитать — поэтому откат здесь выбор, а не археологические раскопки.

![История](assets/screens/light/agent-history.webp?v=20261102-industrial#only-light)
![История](assets/screens/dark/agent-history.webp?v=20261102-industrial#only-dark)

### Карта { #visual-map }

Тот же агент в виде графа: что к нему ведёт и к чему обращается он сам. Пунктирная рамка — то, к чему ничего не привязано: бюджет без собственного потолка читается как пробел, а не как значение по умолчанию.

![Карта](assets/screens/light/agent-map.webp?v=20261102-industrial#only-light)
![Карта](assets/screens/dark/agent-map.webp?v=20261102-industrial#only-dark)

## Сценарии и таблицы { #workflows-and-tables }

### Сценарии { #workflows }

Каталог сценариев, по карточке на каждый: статус, кому доступен, есть ли действующая версия и когда его последний раз редактировали; запуски, редактор и копия — в одном клике.

![Сценарии](assets/screens/light/workflows.webp?v=20261102-industrial#only-light)
![Сценарии](assets/screens/dark/workflows.webp?v=20261102-industrial#only-dark)

### Редактор сценария { #the-workflow-editor }

Холст занимает всю ширину: граф в виде карточек, которые говорят, что должен делать каждый шаг или к какой группе он относится; настройки шага — в диалоге поверх; а любой шаг можно добавить одним кликом — или одним правым кликом. Цикл показывает, сколько шагов в его теле.

![Редактор сценария](assets/screens/light/workflow-editor.webp?v=20261102-industrial#only-light)
![Редактор сценария](assets/screens/dark/workflow-editor.webp?v=20261102-industrial#only-dark)

### Тело цикла { #a-loops-body }

Внутри цикла: текущая компания, сопоставление, которое формирует её запись, и шаг, собирающий результат. Этот локальный пример обрабатывает три вымышленных названия компаний без внешних учётных данных.

![Тело цикла сценария](assets/screens/light/workflow-loop-body.webp?v=20261102-industrial#only-light)
![Тело цикла сценария](assets/screens/dark/workflow-loop-body.webp?v=20261102-industrial#only-dark)

### Запуски { #runs }

Каждый запуск одного сценария, сначала новые: запускался черновик или опубликованная версия, что его начало, когда, сколько он длился и во что обошёлся. **Запустить** (Start a run) проверяет черновик на ваших входных данных.

![Запуски сценария](assets/screens/light/workflow-runs.webp?v=20261102-industrial#only-light)
![Запуски сценария](assets/screens/dark/workflow-runs.webp?v=20261102-industrial#only-dark)

### Запуск { #a-run }

Один запуск, как он прошёл: исполненный граф, где каждый шаг помечен тем, что сделал, выход, которым он ответил, и каждый пройденный шаг, итерация за итерацией.

![Запуск сценария](assets/screens/light/workflow-run.webp?v=20261102-industrial#only-light)
![Запуск сценария](assets/screens/dark/workflow-run.webp?v=20261102-industrial#only-dark)

### Таблицы { #tables }

Вымышленный поток сделок в канбане: одна сделка на карточку, сгруппированы по этапам. Агенты и сценарии могут работать с одними и теми же типизированными записями; главный снимок здесь — доска.

![Таблицы](assets/screens/light/table-kanban.webp?v=20261102-industrial#only-light)
![Таблицы](assets/screens/dark/table-kanban.webp?v=20261102-industrial#only-dark)

## Знания { #knowledge }

### Базы знаний { #knowledge-bases }

Коллекции. Соберите связанные документы в одну, а затем в чате выберите, по каким коллекциям агент может искать.

![Базы знаний](assets/screens/light/knowledge-bases.webp?v=20261102-industrial#only-light)
![Базы знаний](assets/screens/dark/knowledge-bases.webp?v=20261102-industrial#only-dark)

### Коллекция { #a-collection }

Её документы, число фрагментов в них и всё, что не удалось проиндексировать, с причиной. Поиск сопоставляет именно с границами фрагментов, поэтому документ, загруженный заново после смены настроек, разбивается на фрагменты заново.

![Коллекция](assets/screens/light/knowledge-collection.webp?v=20261102-industrial#only-light)
![Коллекция](assets/screens/dark/knowledge-collection.webp?v=20261102-industrial#only-dark)

### Разбор для каждой загрузки { #parsing-per-upload }

Выбор, которого не даёт никто другой: **PyMuPDF**, **LiteParse** или **LlamaParse**, стратегия разбиения на фрагменты, размер фрагмента и перекрытие, OCR и его язык. Задаётся на коллекции и переопределяется для следующего добавляемого файла — потому что сканированному прайсу и Markdown-инструкции нужен не один и тот же разборщик, а неверный — это разница между ответом и отказом.

![Разбор для каждой загрузки](assets/screens/light/kb-parse-options.webp?v=20261102-industrial#only-light)
![Разбор для каждой загрузки](assets/screens/dark/kb-parse-options.webp?v=20261102-industrial#only-dark)

## Тестирование и улучшение агента { #testing-and-improving-an-agent }

Эти экраны — из второй демонстрационной организации, Demo, и её агента Refund desk: вопросы магазина Acme о возвратах и доставке, оценённые проверками и судьёй. Вопросы выдуманы; ответы, оценки и сеансы улучшения — настоящие запуски.

### Наборы данных { #datasets }

Тестовые вопросы, по которым проверяют ваших агентов. Любое изменение кейсов набора данных создаёт новую версию, поэтому два запуска оценки сравниваются на одних и тех же вопросах — или не сравниваются вовсе.

![Наборы данных](assets/screens/light/datasets.webp#only-light)
![Наборы данных](assets/screens/dark/datasets.webp#only-dark)

### Кейсы набора данных { #a-datasets-cases }

Каждый вопрос: откуда он взялся, сколько проверок несёт и использует ли его «Улучшение» для написания изменений или откладывает, чтобы проверить результат.

![Кейсы набора данных](assets/screens/light/dataset-cases.webp#only-light)
![Кейсы набора данных](assets/screens/dark/dataset-cases.webp#only-dark)

### Оценивание { #grading }

Что позволяет ответу пройти: проверки, которые получает каждый кейс, какой судья его оценивает, по каким критериям и начиная с какого балла. Проверки ничего не стоят; каждая оценка судьи — запуск на модели.

![Оценивание](assets/screens/light/dataset-grading.webp#only-light)
![Оценивание](assets/screens/dark/dataset-grading.webp#only-dark)

### Оценки набора данных { #a-datasets-evals }

Все запуски по этому набору данных в виде линии доли прохождений для каждого агента; каждая точка окрашена по тому, какие кейсы версия теперь проходит, а какие перестала проходить; под графиком перечислены запуски.

![Оценки набора данных](assets/screens/light/dataset-evals.webp#only-light)
![Оценки набора данных](assets/screens/dark/dataset-evals.webp#only-dark)

### Запуск оценки { #one-eval-run }

Сначала вердикт, одним предложением, затем доля прохождений до и после, во что он обошёлся против сметы и изменившиеся кейсы. Ответ, который при повторном вопросе вышел другим, не считается изменением.

![Запуск оценки](assets/screens/light/eval-run.webp#only-light)
![Запуск оценки](assets/screens/dark/eval-run.webp#only-dark)

### Оценки агента { #an-agents-evals }

Та же динамика со стороны агента: каждый набор данных, по которому он запускался, отдельной линией, а рядом — **Запустить оценки** (Run evals).

![Оценки агента](assets/screens/light/agent-evals.webp#only-light)
![Оценки агента](assets/screens/dark/agent-evals.webp#only-dark)

### Улучшение { #improve }

С чего начинается улучшение: ответы, которые люди оценили низко, тестовые ответы, не прошедшие проверку, то, как тестовые вопросы делятся между написанием изменений и их проверкой, и сколько стоит первый шаг. Цена каждого следующего шага показывается до нажатия.

![Улучшение](assets/screens/light/improve-start.webp#only-light)
![Улучшение](assets/screens/dark/improve-start.webp#only-dark)

### Улучшение · Понимание { #improve-understand }

Что пошло не так, разложенное на то, что агент может исправить, и то, чего не хватает базе знаний; по карточке на каждый вопрос, с оценками словами. Сеанс останавливается здесь, чтобы вы посмотрели.

![Улучшение · Понимание](assets/screens/light/improve-understand.webp#only-light)
![Улучшение · Понимание](assets/screens/dark/improve-understand.webp#only-dark)

### Улучшение · Изменения { #improve-changes }

Изменения, которые написал помощник, в виде диффа по тексту самого агента, с пояснением, почему и на чём основано каждое. Формулировку можно переписать до проверки; проверка измеряет вашу формулировку.

![Улучшение · Изменения](assets/screens/light/improve-changes.webp#only-light)
![Улучшение · Изменения](assets/screens/dark/improve-changes.webp#only-dark)

### Улучшение · Результат { #improve-result }

К чему пришёл сеанс: каждая попытка — сколько вопросов из отложенных она теперь проходит и сколько перестала проходить, во что обошлась и какую попытку оставить. Ничего не становится действующим, пока человек это не примет.

![Улучшение · Результат](assets/screens/light/improve-result.webp#only-light)
![Улучшение · Результат](assets/screens/dark/improve-result.webp#only-dark)

### Пробелы в знаниях { #knowledge-gaps }

Вопросы, по которым агент искал в своих базах знаний и не нашёл ничего подходящего, сгруппированные по темам. В базу знаний ничего не добавляется само; документ пишет и добавляет человек.

![Пробелы в знаниях](assets/screens/light/knowledge-gaps.webp#only-light)
![Пробелы в знаниях](assets/screens/dark/knowledge-gaps.webp#only-dark)


## Что произошло и что ждёт { #what-happened-and-what-is-waiting }

### Запуски { #runs_1 }

Каждый запуск этой организации: статус, способ запуска, модель, человек и стоимость. Запуск — это процесс: он начинается, его можно остановить, и он оставляет запись.

![Запуски](assets/screens/light/activity.webp?v=20261102-industrial#only-light)
![Запуски](assets/screens/dark/activity.webp?v=20261102-industrial#only-dark)

### Запуск, открытый на просмотр { #one-run-opened }

Токены на входе и выходе, стоимость с четырьмя знаками после запятой, длительность и хронология каждого хода и вызова инструмента. Чат, в котором это было, — в одном клике.

![Запуск, открытый на просмотр](assets/screens/light/run-detail.webp?v=20261102-industrial#only-light)
![Запуск, открытый на просмотр](assets/screens/dark/run-detail.webp?v=20261102-industrial#only-dark)

### Согласования { #approvals }

Всё, что ждёт человека, вместе с тем, что агент намерен сделать. Согласование решается ровно один раз: повторное решение по уже закрытому отклоняется, и именно эта деталь делает согласование стоящим.

![Согласования](assets/screens/light/approvals.webp?v=20261102-industrial#only-light)
![Согласования](assets/screens/dark/approvals.webp?v=20261102-industrial#only-dark)

### Расходы { #spend }

Что фактически израсходовано, по периодам. Бюджет проверяется *до* запроса к модели, а не подсчитывается после, поэтому запуск, вышедший за него, останавливается посреди ответа и всё равно записывает свою стоимость.

![Расходы](assets/screens/light/spend.webp?v=20261102-industrial#only-light)
![Расходы](assets/screens/dark/spend.webp?v=20261102-industrial#only-dark)

### Автоматизации { #routines }

Что делают агенты, когда никто не печатает: по расписанию или когда приходит событие. Такие запуски так же подчиняются бюджету, согласованию и аудиту, как и любые другие.

![Автоматизации](assets/screens/light/routines.webp?v=20261102-industrial#only-light)
![Автоматизации](assets/screens/dark/routines.webp?v=20261102-industrial#only-dark)

### Новый триггер по событию { #a-new-event-trigger }

Имя события, с которого начинается запуск, задаётся поверх списка автоматизаций.

![Новый триггер по событию](assets/screens/light/routine-event-trigger.webp?v=20261102-industrial#only-light)
![Новый триггер по событию](assets/screens/dark/routine-event-trigger.webp?v=20261102-industrial#only-dark)

### Приложения { #apps }

Страницы, опубликованные агентами: отчёты, небольшие панели, сводки. Ссылка сохраняется, когда агент публикует приложение снова, а прежние версии остаются доступны для чтения.

![Приложения](assets/screens/light/apps.webp?v=20261102-industrial#only-light)
![Приложения](assets/screens/dark/apps.webp?v=20261102-industrial#only-dark)

### Одно приложение { #one-app }

Northline объединяет карту европейских поставок, исключения по доставке и окна сборки. Смоделируйте задержку, отфильтруйте входящие отгрузки и изучите зависимость. Это подготовленное локальное приложение для планирования, без подключений к ERP или системам перевозчиков.

![Northline](assets/screens/light/app-manufacturing.webp?v=20261102-industrial#only-light)
![Northline](assets/screens/dark/app-manufacturing.webp?v=20261102-industrial#only-dark)

### Motor Atlas { #motor }

Концептуальная сборка двигателя в разнесённом виде: выбираемые компоненты, управление разнесением, вымышленный перечень материалов и демонстрационная телеметрия. Это интерактивное объяснение, а не чертёж CAD, не диагностика и не инструкция по обслуживанию.

![Motor Atlas](assets/screens/light/app-motor.webp?v=20261102-industrial#only-light)
![Motor Atlas](assets/screens/dark/app-motor.webp?v=20261102-industrial#only-dark)

### Revenue Pulse { #revenue }

Интерактивный обзор продаж: выручка против плана, поток сделок, вклад команды и предстоящие сделки. Все итоги рассчитаны по вымышленным демонстрационным строкам; элементы управления регионом и периодом работают локально.

![Revenue Pulse](assets/screens/light/app-revenue.webp?v=20261102-industrial#only-light)
![Revenue Pulse](assets/screens/dark/app-revenue.webp?v=20261102-industrial#only-dark)

### Визуализации { #visuals-in-the-conversation }

Подготовленная визуализация Revenue Pulse в настоящем рендерере чата. Её можно сохранить как приложение; снимок не означает нового выполнения модели или доступа к реальной CRM.

![Визуализации](assets/screens/light/chat-hero.webp?v=20261102-industrial#only-light)
![Визуализации](assets/screens/dark/chat-hero.webp?v=20261102-industrial#only-dark)

## Организация { #the-organization }

### Организации { #organizations }

Переключайтесь между ними, управляйте участниками, создавайте новые. Полномочия внутри организации — это строка членства плюс каталог разрешений; в пользователе нет столбца роли.

![Организации](assets/screens/light/organizations.webp?v=20261102-industrial#only-light)
![Организации](assets/screens/dark/organizations.webp?v=20261102-industrial#only-dark)

### Участники { #members }

Люди одной организации и их роли, а также месячный потолок, общий для всех агентов в ней, и то, может ли диалог получить освобождение от согласований.

![Участники](assets/screens/light/members.webp?v=20261102-industrial#only-light)
![Участники](assets/screens/dark/members.webp?v=20261102-industrial#only-dark)

### Роли { #roles }

Что может каждая роль, в виде матрицы, которую задаёт код. Открытие доступа к отдельному ресурсу может расширить доступ сверх роли; сузить его — никогда.

![Роли](assets/screens/light/roles.webp?v=20261102-industrial#only-light)
![Роли](assets/screens/dark/roles.webp?v=20261102-industrial#only-dark)

### Группы { #groups }

Отделы и команды. Откройте группе доступ к агенту, базе знаний, навыку или файлу контекста, и пользоваться сможет каждый в ней — а люди вне её нет.

![Группы](assets/screens/light/groups.webp?v=20261102-industrial#only-light)
![Группы](assets/screens/dark/groups.webp?v=20261102-industrial#only-dark)

### Секреты { #vault }

Каждый ключ, сохранённый этой организацией, зашифрован отдельно для неё. Его можно заменить, но больше нельзя прочитать; а ротация ключа невидима для опубликованного агента, который ссылается на секрет, а не на его значение.

![Секреты](assets/screens/light/vault.webp?v=20261102-industrial#only-light)
![Секреты](assets/screens/dark/vault.webp?v=20261102-industrial#only-dark)

### MCP-серверы { #mcp-servers_1 }

Подключите любой MCP-сервер по URL, и его инструменты станут переключателями в Конструкторе. Подключите его для организации — им сможет пользоваться каждый агент; подключите для себя — он останется в вашем чате.

![MCP-серверы](assets/screens/light/mcp-servers.webp?v=20261102-industrial#only-light)
![MCP-серверы](assets/screens/dark/mcp-servers.webp?v=20261102-industrial#only-dark)

### Каналы { #channels }

Чат-платформы, в которых отвечает эта организация: Slack, Teams, Telegram, Mattermost. Бот обслуживает каждого привязанного к нему агента, а привязка делается на вкладке «Доступность» этого агента.

![Каналы](assets/screens/light/channels.webp?v=20261102-industrial#only-light)
![Каналы](assets/screens/dark/channels.webp?v=20261102-industrial#only-dark)

### Песочницы { #sandboxes }

Где агенты этой организации выполняют команды оболочки и хранят файлы. Агент называет подключение по id, поэтому переезд на другой хост — одна правка здесь, а не повторная публикация каждого агента.

![Песочницы](assets/screens/light/sandboxes.webp?v=20261102-industrial#only-light)
![Песочницы](assets/screens/dark/sandboxes.webp?v=20261102-industrial#only-dark)

### Документы { #documents }

Файлы, которые агенты хранят для вас. Рабочее пространство — это черновая область: оно удаляется вместе с диалогом, к которому относится, и не годится для долговременного хранения.

![Документы](assets/screens/light/documents.webp?v=20261102-industrial#only-light)
![Документы](assets/screens/dark/documents.webp?v=20261102-industrial#only-dark)

### Ваши настройки { #your-settings }

Ваш профиль и учётная запись, слеш-команды, память, входы, которые агент-браузер хранит для вас, уведомления, API-ключи, MCP-клиенты и Архитектор — по вкладке на каждое.

![Ваши настройки](assets/screens/light/settings.webp?v=20261102-industrial#only-light)
![Ваши настройки](assets/screens/dark/settings.webp?v=20261102-industrial#only-dark)

## Администрирование экземпляра { #deployment-administration }

### Пользователи { #users }

Все, кто может войти в этот экземпляр, и флаг администратора платформы, не зависящий от роли в организации.

![Пользователи](assets/screens/light/admin-users.webp?v=20261102-industrial#only-light)
![Пользователи](assets/screens/dark/admin-users.webp?v=20261102-industrial#only-dark)

### Все организации { #all-organizations }

Каждая организация в этом экземпляре: владелец, участники и агенты.

![Все организации](assets/screens/light/admin-organizations.webp?v=20261102-industrial#only-light)
![Все организации](assets/screens/dark/admin-organizations.webp?v=20261102-industrial#only-dark)

### Система { #system }

База данных, Redis, векторное хранилище, доступ к моделям и запланированные задачи — те же проверки, что выполняет `agenticos cmd doctor`, на странице.

![Система](assets/screens/light/admin-system.webp?v=20261102-industrial#only-light)
![Система](assets/screens/dark/admin-system.webp?v=20261102-industrial#only-dark)

### Платформа { #deployment }

Собственная идентичность и политика этого экземпляра: регистрация, приглашения, объявления и то, что видит новый посетитель.

![Платформа](assets/screens/light/admin-deployment.webp?v=20261102-industrial#only-light)
![Платформа](assets/screens/dark/admin-deployment.webp?v=20261102-industrial#only-dark)

## Чего здесь ещё нет { #what-is-not-here-yet }

Вход, знакомство при первом запуске и экскурсия по продукту ещё не сняты.

## Итог { #recap }

Каждый экран показан в обеих темах и взят из одной демонстрационной организации. `docs/assets/screens/capture.mjs` снимает их все заново на любом запущенном экземпляре, поэтому изменившийся экран — это повторный запуск, а не снимок, сделанный вручную.
