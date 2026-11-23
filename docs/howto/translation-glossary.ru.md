---
title: "Переводите документы с вашей терминологией"
description: "Привяжите к агенту глоссарий из десяти терминов и проверьте, что каждый термин применён, термины без перевода и числа сохранены, а неоднозначность помечена, а не разрешена молча."
source_sha: "087a50c95d4c"
---

# Переводите документы с вашей терминологией { #translate-documents-with-your-terminology }

Дайте агенту короткий английский документ и навык с вашим глоссарием, и пусть он
переведёт текст на другой язык, сохранив названия продуктов, названия функций и
другие термины без перевода на английском. В примере есть по-настоящему
неоднозначная дата, поэтому вы можете проверить, помечает ли её агент, вместо того
чтобы молча угадывать. Это процедура, которую нужно выполнить; один записанный
запуск приведён как образец.

## Что понадобится { #what-you-need }

- Работающий [экземпляр AgenticOS](../install.md) с профилем модели.
- Ни песочница, ни модель эмбеддингов не нужны: этому агенту нужны только
  [возможность навыков](../reference/capabilities.md#skills) и вложение в чате.

## Подготовьте входные данные { #prepare-the-input }

Сохраните это как навык в **Навыки → Новый навык** (Skills → New skill). Назовите
его `fenwick-glossary` и дайте описание вроде «Which terms in a Fenwick Ledger
document stay in English, and the preferred Polish translation for the rest.»
Fenwick Ledger — синтетический бухгалтерский продукт, придуманный для этого теста.

```text
Fenwick Ledger is a synthetic accounting product used only for this test.

## Keep in English, never translate

- Fenwick Ledger (product name)
- Quick Close (feature name)
- workspace
- API key
- sandbox

## Translate using these terms

| English | Polish |
|---|---|
| ledger | księga |
| invoice | faktura |
| reconciliation | uzgadnianie |
| dashboard | pulpit |
| audit trail | ślad audytu |

Numbers, dates and currency amounts are copied exactly as they appear in the
source — do not reformat a date or convert a currency. If a sentence in the
source could be read two ways, translate the more likely reading and add one
line after the translation flagging the ambiguity and both readings.
```

Затем сохраните этот синтетический документ как `release-notes.md`. Дата
`03/04/2026` намеренно неоднозначна между чтением «сначала день» и «сначала
месяц» — именно этот случай пример и должен проверять.

```text
Fenwick Ledger 4.2 release notes

This release adds Quick Close, a one-click way to close the monthly ledger
once every invoice is matched. Quick Close runs the reconciliation for the
current period and shows the results on the dashboard.

Every action Quick Close takes is written to the audit trail, so a
controller can see which invoices were matched automatically and which
needed a manual review.

To use Quick Close in a shared workspace, generate an API key from Settings
and add it to your sandbox environment before running your first close.

The reconciliation step handles invoices up to EUR 50,000 automatically;
anything above that amount is queued for manual approval.

Close the March books by 03/04/2026, before the quarterly audit begins.

Fenwick Ledger is a synthetic product created for this test; no real company
or software is described here.
```

## Создайте агента { #build-the-agent }

1. Создайте агента в **Агенты → Новый агент** (Agents → New agent) и выберите
   свой профиль модели.
2. В **Инструменты** (Toolbox) включите **Навыки** (Skills) и привяжите навык
   `fenwick-glossary`.
3. Задайте инструкции ниже, затем нажмите **Опубликовать** (Publish).

```text
You translate documents into Polish.
Follow the bound glossary skill: never translate the terms it lists as
English-only, and use its preferred Polish translation for the rest.
Copy every number, date and currency amount exactly as it appears in the
source.
If a sentence could be read two ways, translate the more likely reading and
add one line after the translation flagging the ambiguity and both readings.
```

## Запустите его { #run-it }

Откройте новый чат с агентом, прикрепите `release-notes.md` и отправьте:

```text
Translate the attached release notes into Polish.
```

## Проверьте результат { #check-the-result }

| Проверка | Что должно быть |
| --- | --- |
| Переведены пять терминов глоссария | ledger→księga, invoice→faktura, reconciliation→uzgadnianie, dashboard→pulpit, audit trail→ślad audytu |
| Сохранены пять терминов без перевода | Fenwick Ledger, Quick Close, workspace, API key и sandbox — все на английском |
| Денежная сумма не изменилась | `EUR 50,000` приводится ровно так, без пересчёта и переформатирования |
| Дата не изменилась | `03/04/2026` приводится ровно так, а не в польском формате даты |
| Неоднозначность помечена | Отдельная заметка называет оба прочтения `03/04/2026` |
| Тот же запрос без прикреплённого файла | Агент просит документ, а не переводит пустоту |

Читайте польский текст, сверяя его с глоссарием, термин за термином; не доверяйте
сводке, которая лишь перечисляет найденные термины.

!!! example "Записано на v0.0.504, 25 сентября 2026"

    Модель: Claude Sonnet 4.6 через OpenRouter. Агент вызвал `read_skill` для
    `fenwick-glossary`, затем перевёл весь документ в одном ответе. Все пять
    терминов глоссария переведены правильно, и все пять терминов без перевода —
    включая `workspace`, `API key` и `sandbox` внутри польских предложений, —
    остались английскими. `EUR 50,000` и `03/04/2026` скопированы без изменений.
    Стоимость: 0,017 USD.

    После перевода агент добавил помеченную заметку: при чтении «сначала день»
    03/04/2026 — это 3 апреля 2026 года (использовано в переводе); при чтении
    «сначала месяц» — 4 марта 2026 года. Он порекомендовал уточнить, что имелось
    в виду. Без прикреплённого файла он загрузил глоссарий, а затем попросил
    документ, вместо того чтобы переводить пустоту.

## Когда что-то идёт не так { #when-it-goes-wrong }

- **Термин без перевода всё равно переведён.** Возможно, модель считает его
  обычной лексикой, а не именем собственным; поставьте список первым в теле
  навыка и повторите указание в собственных инструкциях агента.
- **Меняется число.** Попросите агента цитировать исходное предложение рядом с
  переводом; расхождение видно сразу.
- **Неоднозначность разрешена молча.** Ужесточите инструкции, чтобы они требовали
  отдельной строки с пометкой, а не оставляли выбор формулировке навыка по
  умолчанию.
- **Агент переводит без прикреплённого файла.** Ужесточите инструкции, чтобы они
  требовали отказа, когда документа нет.

## Зафиксируйте проверку { #record-the-trial }

Сохраните исходный документ, тело глоссария, перевод, версию агента и запуск в
разделе «Активность». Человек, читающий целевой язык, всё равно проверяет
беглость перевода и подтверждает, какое прочтение помеченной неоднозначности
имелось в виду на самом деле, — глоссарий и проверки выше ловят терминологию и
сохранённые значения, а не то, читается ли предложение естественно.

## Что дальше { #next-steps }

Для рабочего глоссария держите его в одном навыке на пару языков, а не в одном
навыке со всеми языками вперемешку, чтобы переводчик мог проверить и
отредактировать только свою пару. [Запись DeepL](../mcp.md#automation-storage-productivity-media)
в каталоге MCP — альтернативный движок перевода, который можно подключить вместо
прямого использования модели; эта страница его не использует.
