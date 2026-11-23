---
title: "Проверьте изменение в репозитории"
description: "Поручите агенту собрать крошечный git-репозиторий в своей песочнице, затем проверить один diff по встроенному навыку code-review и убедиться, что он находит оба подложенных дефекта на нужных строках."
source_sha: "63ed58260c3c"
---

# Проверьте изменение в репозитории { #review-a-change-in-a-repository }

Дайте агенту [песочницу](../sandbox.md) и поставляемый навык `code-review`,
поручите ему настроить небольшой git-репозиторий и проверить по нему diff одного
коммита. В diff два подложенных дефекта — ошибка на единицу и необработанный
`None` — в местах вида файл:строка, которые можно проверить вручную. Это
процедура, которую нужно выполнить, с одним записанным запуском для сверки.

## Что понадобится { #what-you-need }

- [Работающий экземпляр](../install.md) с профилем модели и зарегистрированным
  [подключением песочницы](../sandbox.md), среда выполнения по умолчанию
  которого — `workbench`: в нём есть `git`.
- Навык `code-review`, который поставляется в каждой организации; см.
  [Навыки](../skills.md#getting-skills-into-an-organization).
- Больше ничего привязывать не нужно: репозиторий создаёт внутри песочницы сам
  агент, из точного содержимого файлов в промпте ниже.

## Подготовьте входные данные { #prepare-the-input }

Здесь нечего прикреплять: «репозиторий» — это два небольших файла Python,
которые агент пишет сам по вашей инструкции, поэтому вы точно управляете тем,
что содержит diff. Версия 1 — простой калькулятор покупок:

```python
# utils.py (version 1)
def compute_total(prices):
    total = 0
    for p in prices:
        total += p
    return total


def find_discount_tier(count):
    tiers = [(10, 0.05), (20, 0.10), (50, 0.15)]
    for threshold, rate in tiers:
        if count >= threshold:
            return rate
    return 0.0
```

```python
# main.py (version 1)
from utils import compute_total, find_discount_tier


def checkout(cart):
    prices = [item["price"] for item in cart]
    total = compute_total(prices)
    rate = find_discount_tier(len(cart))
    return total * (1 - rate)
```

Версия 2 добавляет скидку участника и помощник `apply_discount` с двумя
подложенными дефектами: новый параметр `member_rate` в `main.py` по умолчанию
равен `None` и используется без проверки, а `apply_discount` в `utils.py` даёт
скидку на каждый товар, включая самый дорогой, а затем добавляет тот же товар
ещё раз, считая его дважды.

```python
# utils.py (version 2, adds apply_discount)
def apply_discount(prices, rate):
    """Discount every item except the single most expensive one."""
    sorted_prices = sorted(prices)
    n = len(sorted_prices)
    discounted = [sorted_prices[i] * (1 - rate) for i in range(n)]
    discounted.append(sorted_prices[-1])
    return discounted
```

```python
# main.py (version 2)
from utils import compute_total, find_discount_tier, apply_discount


def checkout(cart, member_rate=None):
    prices = [item["price"] for item in cart]
    tier_rate = find_discount_tier(len(cart))
    discounted_prices = apply_discount(prices, tier_rate)
    total = compute_total(discounted_prices)
    return total * (1 - member_rate)
```

Для сверки: в diff между двумя версиями ровно два настоящих дефекта —
`return total * (1 - member_rate)` в `main.py` возбуждает `TypeError` всякий
раз, когда `member_rate` остаётся со значением по умолчанию, а `apply_discount`
в `utils.py` возвращает на один товар больше, потому что его цикл уже включает
последний индекс, прежде чем тот же товар добавляется снова. Ни `compute_total`,
ни `find_discount_tier` между версиями не меняются, поэтому правильная проверка
о них ничего не говорит.

## Создайте агента { #build-the-agent }

1. Создайте агента в **Агенты → Новый агент** (Agents → New agent) и выберите
   свой профиль модели.
2. На вкладке **Инструменты** (Toolbox) включите **Песочница** (Sandbox). Выберите
   вариант **Контейнер** (Container), укажите своё подключение песочницы и среду
   выполнения `workbench` и оставьте область диалога.
3. Включите **Навыки** (Skills) и привяжите `code-review`.
4. Задайте бюджет для пробы: в записанном запуске было около 40 шагов
   и стоимость около 0,18 USD, в основном из-за повторных согласований
   оболочки.
5. Задайте инструкции ниже, затем нажмите **Опубликовать** (Publish).

```text
You review a change in a git repository using the bound code-review skill.
Follow that skill: read the whole change before commenting, say what is wrong
and why it matters, and separate what blocks from what does not.
Only report issues that are actually in the diff. Do not report a problem in a
line the diff did not touch.
Cite every finding as file:line and give a concrete fix.
```

## Запустите его { #run-it }

Откройте новый чат и вставьте две версии выше с инструкциями настроить
репозиторий и проверить изменение:

```text
Set up a tiny git repository in your workspace and review one change in it.

1. Create a directory `grocery_calc`, write utils.py and main.py exactly as
   given above (version 1), then git init, configure a throwaway user, and
   commit them as the initial version.
2. Replace both files with version 2 exactly as given above, and commit that
   as a second commit.
3. Run `git diff HEAD~1 HEAD` to get the exact change, then review that diff
   using the bound code-review skill. Report every real defect the diff
   introduces, each as file:line with a concrete fix. Do not report anything
   about a line the diff did not change.
```

Каждый вызов `execute`, который делает агент, — `git init`, каждый коммит,
diff — показывает в чате **Требуется согласование инструмента**
(Tool approval required). Прочитайте команду и **Одобрите** (Approve) каждую;
запуск продолжается с того места, где остановился. Чтобы пропустить это для
доверенного тестового агента, измените настройку согласования для `execute`
в Конструкторе. См. [согласования](../governance.md#approvals).

## Проверьте результат { #check-the-result }

| Проверка | Образец |
| --- | --- |
| Необработанный `None` найден | `main.py:9`, `return total * (1 - member_rate)` падает, когда `member_rate` остаётся со значением по умолчанию `None` |
| Ошибка на единицу найдена | `utils.py:20-21`: списковое включение уже охватывает каждый индекс, включая последний, а затем этот товар добавляется второй раз |
| Оба указаны как файл:строка | Не просто «в apply_discount есть ошибка» |
| Исправление конкретное | Исправленный фрагмент, а не только описание проблемы |
| Неизменённый код | Ни одного замечания о `compute_total` или `find_discount_tier`, которых diff не касался |
| Блокирующее и нет | Оба дефекта помечены как блокирующие; всё стилистическое снабжено префиксом «nit» |

Прочитайте diff сами, прежде чем доверять проверке: `git diff HEAD~1 HEAD`
на панели файлов рабочего пространства показывает ровно то, что проверял агент.

!!! example "Записано на v0.0.504, 25 сентября 2026"

    Модель: Claude Sonnet 4.6 через OpenRouter. Агент загрузил навык
    `code-review`, затем `read_skill_resource` для `checklist.md`
    и `review-comment.md` навыка, потом написал оба файла, сделал два коммита
    и выполнил diff — три вызова `execute`, каждый вставал на согласование.
    После одобрения он ответил тремя блокирующими замечаниями и одним nit.

    Он указал `main.py:9` для `TypeError` при отсутствующем `member_rate`,
    с исправлением в две строки, и `utils.py:20-21` для дважды посчитанного
    товара с исправленным `apply_discount`, точно совпав с обоими подложенными
    дефектами. Ещё он отметил, что diff не проверен тестами, следуя правилу
    из контрольного списка навыка: изменение поведения требует теста.
    О `compute_total` и `find_discount_tier` он не сказал ничего. Стоимость:
    0,18 USD за 40 шагов, большая часть — три круга согласования.

## Когда что-то идёт не так { #when-it-goes-wrong }

- **Агент говорит, что у него нет оболочки.** Возможность использует
  **Файлы** (Files), а не **Контейнер** (Container) — переключитесь на Контейнер
  и выберите среду выполнения `workbench`.
- **Запуск останавливается после `git init` или коммита.** Он ждёт согласования
  `execute`. Откройте чат или вкладку **Согласования** (Approvals)
  в разделе **Активность** (Activity).
- **Проверка сообщает что-то о неизменённом коде.** Уточните инструкции: он
  должен проверять только то, что показывает `git diff`, а не весь файл.
- **У замечания нет файла:строки.** Попросите его перечитать собственные шаблоны
  комментариев из контрольного списка — в них всегда указано место.
- **Первый ход медленный.** Образ `workbench` собирается; последующие сеансы его
  переиспользуют. См. [песочницу](../sandbox.md#when-a-build-is-paid-for).

## Зафиксируйте проверку { #record-the-trial }

Сохраните точные две версии, diff, проверку, версию агента и каждую одобренную
команду в «Активности». Правильны ли исправления и должно ли «нет теста на это»
действительно блокировать слияние, всё равно решает человек: навык формулирует
правило, но применять его к настоящему пул-реквесту — дело человека, как
и [локальный стандарт проверки](../code-review.md), которого этот проект
придерживается в своих пул-реквестах.

## Что дальше { #next-steps }

Направьте того же агента на песочницу с доступом в сеть и поручите ему
проверить diff из склонированного публичного репозитория, а не тот, что диктуете
вы: в среде выполнения `workbench` есть `git` и сеть, поэтому `git clone`
работает так же, как здесь работал `git diff`.
