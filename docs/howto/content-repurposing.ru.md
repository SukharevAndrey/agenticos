---
title: "Превратите одну статью в посты для соцсетей голосом бренда"
description: "Приложите короткую синтетическую статью, и агент напишет по одному посту на канал — каждый прослеживается до источника и укладывается в лимит своего канала."
source_sha: "c5a69d2f6d38"
---

# Превратите одну статью в посты для соцсетей голосом бренда { #turn-one-article-into-social-posts-in-your-brand-voice }

Дайте агенту одну статью и навык с правилами голоса и лимитами каналов — и
пусть он напишет один пост для LinkedIn, один для X и один текст для рассылки.
Тестовый образец настолько короткий, что каждое утверждение в постах можно
сверить со статьёй вручную. Это процедура, которую нужно выполнить; один
записанный запуск приведён как образец. Она не измеряет, насколько голос
совпадает с вашим.

## Что понадобится { #what-you-need }

- Работающий [экземпляр AgenticOS](../install.md) с профилем модели.
- Песочница, модель эмбеддингов и подключение по MCP не нужны: этому агенту
  нужны только [возможность «Навыки»](../reference/capabilities.md#skills) и
  вложение в чате.

## Подготовьте входные данные { #prepare-the-input }

Сохраните это как навык в **Навыки → Новый навык** (Skills → New skill).
Назовите его `brand-voice`, дайте описание вроде «Тон, запрещённые фразы и
лимиты каналов для превращения статьи в посты для соцсетей» и вставьте такое
тело:

```text
Plain, confident, specific. Say what happened and what it means for the
reader. Use second person when addressing the reader, and "we" when
describing what the company did. State only facts, numbers and outcomes that
appear in the source article — never round a figure or add an outcome the
article does not give.

## Banned phrases

Never use any of: "game-changer", "revolutionize", "cutting-edge", "seamless",
"unlock your potential", "at the end of the day", "in today's fast-paced
world".

## Channel limits

| Channel | Limit | Shape |
|---|---|---|
| LinkedIn | 80-150 words | Open with the concrete result, two or three short paragraphs, at most one hashtag |
| X | 280 characters or fewer, counting spaces | One idea, no thread, no hashtag needed |
| Newsletter blurb | 40-60 words | One sentence hook, one sentence of detail, end with the placeholder [link] |

Write one post per channel from the attached article. Every claim in a post
must trace to a sentence in the article. If the article does not state a
number or outcome, the post does not state one either.
```

Затем сохраните эту синтетическую статью примерно на 400 слов как `article.md`.
Цифры придуманы для этого теста: вымышленная компания, вымышленный пилот —
ничего настоящего в ней нет.

```text
Northwind Robotics cuts warehouse pick times in six-week pilot

Northwind Robotics, a fictional logistics-robotics company, ran a six-week pilot
of its updated picking robot, the Pallox-3, across three regional warehouses.
The pilot measured the time from an order arriving to an item leaving the pick
station.

Average pick time fell from 12 seconds to 9 seconds per item, a 25 percent
reduction, measured across 40,000 picks during the pilot. The Pallox-3's
battery lasts 8 hours per charge, up from 5 hours on the previous model,
which let two of the three sites run a full shift without a midday swap.

No worker injuries were recorded at any of the three pilot sites during the
six weeks, according to the internal safety log Northwind shared with pilot
staff. The robot's new obstacle sensor, added after last year's design
review, stops the unit within 4 centimeters of an unexpected object, compared
with 15 centimeters on the previous sensor.

Warehouse staff at the pilot sites were surveyed at the end of the six weeks.
68 percent said the robot's new charging dock was easier to use than the old
one; 12 percent reported no opinion; the remainder did not respond to that
question.

Based on the pilot results, Northwind plans to roll the Pallox-3 update out
to 40 additional warehouses during the fourth quarter. The rollout will
happen in four batches of ten sites, starting with the two regions that ran
the pilot. Each batch is expected to take one week to install and configure,
based on the installation time recorded during the pilot.

The Pallox-3 hardware itself did not change during the pilot; the
improvement came from a software update to the picking algorithm, which
Northwind's engineering team had been testing internally for four months
before the pilot began. The update is delivered over the air to existing
Pallox-3 units, so warehouses do not need to replace hardware to get the
faster pick times.

Northwind has not yet published pricing for warehouses outside the original
three pilot sites, and a company spokesperson said in the pilot debrief that
a decision on pricing for the Q4 rollout is still under review. The company
also declined to say whether the update would be offered to older Pallox-2
units.

This is a synthetic case study; Northwind Robotics, the Pallox-3 and every
figure above are invented for this test.
```

Опорные факты для последующей сверки: на 25% быстрее (с 12 с до 9 с),
40 000 подборов, батарея на 8 часов, остановка в 4 см от препятствия, 68%
сотрудников нашли новую зарядную станцию удобнее, обновление только
программное, ни одной травмы, а также развёртывание в IV квартале на 40
складов в четыре партии. В статье сказано и то, что *пока* неизвестно: цена
развёртывания и получит ли обновление старая модель Pallox-2.

## Создайте агента { #build-the-agent }

1. Создайте агента в **Агенты → Новый агент** (Agents → New agent) и выберите
   свой профиль модели.
2. В **Инструменты** (Toolbox) включите **Навыки** (Skills) и привяжите только
   что созданный навык `brand-voice`.
3. Задайте инструкции ниже, затем нажмите **Опубликовать** (Publish).

```text
You repurpose one article into social posts.
Follow the bound brand-voice skill for tone, banned phrases and channel limits.
Write exactly one post per channel: LinkedIn, X and the newsletter blurb.
Every claim must trace to a sentence in the attached article. Do not invent a
fact, a number or an outcome the article does not state.
Label each post with its channel name.
```

## Запустите его { #run-it }

Откройте новый чат с агентом, приложите `article.md` и отправьте:

```text
Turn the attached article into one post per channel: LinkedIn, X and the
newsletter blurb.
```

## Проверьте результат { #check-the-result }

| Проверка | Образец |
| --- | --- |
| Длина поста для X | Не более 280 символов, включая пробелы |
| Длина поста для LinkedIn | 80–150 слов |
| Длина текста для рассылки | 40–60 слов |
| Запрещённые фразы | Ни одной из семи фраз из навыка нет ни в одном посте |
| Каждое число и результат | Прослеживается до предложения в `article.md` — 25%, с 12 с до 9 с, 40 000 подборов, 4 см, ни одной травмы, развёртывание в IV квартале на 40 складов |
| Тот же запрос без приложенного файла | Агент просит статью, а не придумывает её |

Считайте символы в посте для X вручную или коротким скриптом: не доверяйте
собственному утверждению модели о длине.

!!! example "Записано на v0.0.504, 25 сентября 2026"

    Модель: Claude Sonnet 4.6 через OpenRouter. Агент вызвал `read_skill` для
    `brand-voice`, затем сразу ответил всеми тремя постами. В посте для X было
    185 символов. В посте для LinkedIn — 113 слов, в тексте для рассылки — 50
    слов, оба в пределах лимита. Ни в одном посте не встретилось запрещённой
    фразы. Все числа во всех трёх постах — 25%, с 12 с до 9 с, 40 000 подборов,
    4 см до препятствия и развёртывание в IV квартале на 40 складов в четыре
    партии — совпали со статьёй. Стоимость: 0,016 USD.

    Без приложенного файла агент всё равно загрузил навык, затем сказал «I
    don't see any article attached to your message» и попросил статью — вместо
    того, чтобы писать посты из ничего.

## Когда что-то идёт не так { #when-it-goes-wrong }

- **Пост превышает свой лимит.** Навык называет лимит как правило, а не как
  пожелание; ужесточите инструкции — пусть агент считает символы перед
  ответом, — или сократите примерные длины в самом навыке.
- **Запрещённая фраза проскакивает.** Проверьте, что привязано именно то тело
  навыка и его не перекрыла более старая версия: «Навыки» хранят по одной
  версии на навык, а старый диалог мог загрузить прежний ответ до правки.
- **Агент выдумывает статистику.** Попросите его процитировать предложение, из
  которого взято число: число, которое он не может процитировать, он выдумал.
- **Агент пишет посты без приложенной статьи.** Если он не отказывается,
  ужесточите инструкции: пусть отказывается, когда файла нет.

## Зафиксируйте проверку { #record-the-trial }

Сохраните статью, тело навыка, три поста, версию агента и запуск в разделе
**Активность**. Сохраните и запуск, где лимит был превышен: он показывает, в чём
была ошибка — в формулировке навыка или в арифметике модели.

Человек по-прежнему решает, звучит ли голос действительно как бренд и годится ли
пост для публикации. Подсчёт символов и проверка запрещённых фраз механические;
суждение о голосе — нет.

## Что дальше { #next-steps }

Добавьте голос второго бренда отдельным навыком и привязывайте тот, который
нужен диалогу, а не раздувайте один навык вариантом на каждого клиента.
О том, как навык ограничивается по области и как им делятся, см.
[Навыки](../skills.md).
