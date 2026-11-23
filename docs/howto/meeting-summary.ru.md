---
title: "Превратите транскрипт встречи в решения и задачи"
description: "Вставьте короткий синтетический транскрипт и проверьте, что агент отделяет решения от задач, называет для каждой владельца и срок и отмечает задачу, которую никто не взял."
source_sha: "1d2ade73a284"
---

# Превратите транскрипт встречи в решения и задачи { #summarise-a-meeting-transcript-into-decisions-and-action-items }

Создайте агента, который превращает вставленный транскрипт в три коротких
списка: решения, задачи с владельцем и сроком, а также открытые вопросы. В
транскрипте примера есть задача, которая всплывает, но которую на самом деле
никто не соглашается взять; важнее всего проверить, честно ли агент об этом
сообщает, вместо того чтобы назначить её тому, кто упомянут рядом. Это процедура,
которую нужно выполнить; один записанный запуск приведён как образец.

## Что понадобится { #what-you-need }

- Работающий [экземпляр AgenticOS](../install.md) с профилем модели.
- Никаких возможностей. Этот агент читает то, что есть в сообщении, и ничего
  больше.

## Подготовьте входные данные { #prepare-the-input }

Короткий синтетический транскрипт, выдуманный для этой страницы:

```text
Onboarding revamp sync — 12 March, 10:00–10:35
Jenna: Let's get through this quickly. Marcus, where are we with the signup
API changes?
Marcus: Mostly done. I can have the new field validation live by March 20.
Jenna: Good. Priya, the tooltip designs?
Priya: Almost there. I can deliver the final set by March 18, in time for
Marcus to wire them up.
Jenna: Great. Let's also decide on the survey step. Tomas, you said support
tickets show people dropping off there.
Tomas: Right, about a third of drop-offs happen on the survey screen. My
recommendation is to remove it entirely rather than shorten it.
Jenna: Agreed, let's remove the survey step from onboarding. Marcus, can you
fold that into the same API change?
Marcus: Yes, same PR.
Jenna: Decision made — the survey step is gone. Now, should the new tooltip
flow go to everyone at once, or beta first?
Priya: Beta first. We haven't tested it on mobile yet.
Marcus: Agreed, mobile rendering is still rough.
Jenna: Okay, decision: new tooltip flow ships to beta users first, general
release after that's clean.
Tomas: One more thing — the help center article on "how onboarding works"
is now out of date once the survey step is gone. Somebody should update it
before we ship.
Jenna: Good catch. Let's make sure that happens.
Priya: I can't take that on, I'm full up with the tooltip work through the
20th.
Marcus: Not mine either, that's not engineering's article.
Jenna: Okay, let's flag it and figure out who owns docs later this week.
Tomas: Compiling the onboarding-related support tickets into a report —
I'll do that, but I don't have a firm date yet, depends on how much backlog
I need to dig through.
Jenna: That's fine, just get it to us when it's ready.
Jenna: Last open question — do we sunset the old onboarding flow entirely,
or keep it behind a flag as a fallback for a few weeks?
Marcus: I'd lean toward keeping the flag, in case the new flow breaks
something we didn't catch in beta.
Priya: I don't have a strong opinion either way.
Jenna: Let's leave that open and revisit once beta feedback comes in.
Jenna: Okay, I think that's everything. Thanks all.
```

Образец: два решения (убрать шаг с опросом; сначала выпустить поток подсказок для
бета-пользователей), четыре задачи с владельцем, одна задача — обновление статьи
в справочном центре, — от которой явно отказались и Priya, и Marcus, и один
открытый вопрос, оставленный на потом.

## Создайте агента { #build-the-agent }

1. Создайте агента в **Агенты → Новый агент** (Agents → New agent) и выберите
   свой профиль модели.
2. Оставьте **Инструменты** (Toolbox) пустыми. Здесь никакой инструмент не
   нужен.
3. Задайте бюджет и лимит шагов для проверки.
4. Задайте инструкции ниже, затем нажмите **Опубликовать** (Publish).

```text
You turn a pasted meeting transcript into three sections: Decisions,
Action items, and Open questions.

For each action item, name the owner and the due date exactly as stated. If
a task is mentioned but nobody agreed to own it, list it under Action items
as unassigned and say so - never guess an owner, and never assign it to
someone who explicitly declined it in the transcript.

List a topic under Open questions only if the transcript does not record a
decision on it. Do not invent a decision, an owner, or a date the transcript
does not state.
```

## Запустите его { #run-it }

Вставьте транскрипт прямо в новый диалог после короткой инструкции:

```text
Summarise this meeting transcript into decisions, action items and open questions.

[paste the transcript]
```

Прикрепление его как текстового файла работает так же: агенту без рабочего
пространства текст загруженного файла вставляется в промпт так же, как при
вставке. См. [обработку файлов](../file-processing.md#chat-file-uploads).

## Проверьте результат { #check-the-result }

| Проверка | Образец |
| --- | --- |
| Решения | Убрать шаг с опросом; сначала выпустить поток подсказок для бета-пользователей |
| Задачи Marcus | Проверка полей и удаление шага с опросом, срок 20 марта |
| Задача Priya | Финальные макеты подсказок, срок 18 марта |
| Задача Tomas | Сбор отчёта по обращениям в поддержку, без выдуманного срока |
| Статья в справочном центре | Указана как нераспределённая, а не отдана Priya или Marcus |
| Открытые вопросы | Только вопрос «свернуть или оставить запасной вариант», а не решение, пересказанное как вопрос |

Сначала проверьте нераспределённую задачу. Агент, который тихо отдаёт её тому
имени, что оказалось ближе всего в транскрипте, не прошёл ту единственную
проверку, ради которой существует эта страница, даже если каждая другая строка
верна.

!!! example "Записано на v0.0.504, 25 сентября 2026"

    Модель: Claude Sonnet 4.6 через OpenRouter. Вызовов инструментов не было —
    весь ответ пришёл из одного запроса к модели, за 0,0063 USD. Он перечислил
    оба решения, дал Marcus две строки (проверка полей и удаление шага с
    опросом, обе к 20 марта), макеты подсказок Priya — к 18 марта, а отчёт
    Tomas — с пометкой «точного срока нет — будет передан, когда будет готов».
    Статья в справочном центре была указана как **«Не назначено (Priya и Marcus
    оба отказались; владельца определим позже на этой неделе)»**, а не отдана
    кому-то из них. Единственным открытым вопросом было решение «свернуть или
    оставить запасной вариант», помеченное как отложенное.

## Когда что-то идёт не так { #when-it-goes-wrong }

- **Никем не взятую задачу всё равно кому-то назначают.** Это тот сбой, за
  которым нужно следить. Ужесточайте инструкции дальше: указание точной фразы
  «отказался» или «не назначено» иногда помогает меньше, чем второй транскрипт,
  где та же картина повторяется, — так видно, был ли первый результат везением.
- **Появляется срок, которого никто не называл.** Модель заполнила пробел, потому
  что задача без срока читается как неполная. Проверьте, что последняя строка
  инструкций делает своё дело, и испытайте на транскрипте с более чем одной
  задачей без срока.
- **Открытый вопрос пересказывает уже решённое.** Модель сочла решение, принятое
  под давлением в конце встречи, всё ещё открытым. Укажите ей точную строку, где
  решение было принято.
- **Три раздела сливаются.** На более длинном и сумбурном транскрипте просите
  разделы в фиксированном порядке и проверяйте, что в каждом есть только то, что
  к нему относится.

## Зафиксируйте проверку { #record-the-trial }

Сохраните транскрипт, точный промпт, версию агента, модель и ответ. Человек
по-прежнему сверяет нераспределённую задачу напрямую с транскриптом: это ровно та
мелкая, легко упускаемая деталь, которую пропускает быстрое чтение длинной
сводки.

## Что дальше { #next-steps }

Превращение каждой задачи в настоящую отслеживаемую задачу с уведомлением
владельца — отдельный шаг, описанный в разделе [превращение заметок о встрече в
задачи](meeting-to-tasks.md). Эта страница заканчивается на сводке, которую
человек читает и проверяет.
