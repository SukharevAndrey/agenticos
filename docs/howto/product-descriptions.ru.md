---
title: "Пишите описания товаров по файлу каталога"
description: "Приложите небольшой вымышленный products.csv, и пусть агент напишет по одному описанию для каталога на каждую строку, а строку без обязательного атрибута пометит, а не выдумает его."
source_sha: "19a5f7f5ada4"
---

# Пишите описания товаров по файлу каталога { #write-product-descriptions-from-a-catalogue-file }

Приложите небольшой файл каталога к обычному чат-агенту, привязанному к навыку для
текстов каталога, и проверьте, что он пишет по одному описанию на товар, не
выдумывая того, чего нет в спецификации. В одной строке намеренно не хватает
обязательного атрибута, чтобы вы могли проверить, что агент отмечает пробел, а не
заполняет его. Это процедура, которую нужно выполнить, с одним записанным запуском
как образцом.

## Что понадобится { #what-you-need }

- Работающий [экземпляр AgenticOS](../install.md) с профилем модели.
- Ни песочница, ни модель эмбеддингов не нужны: достаточно обычного чат-агента с
  [возможностью навыков](../reference/capabilities.md#skills); CSV мал настолько,
  что его можно вставить в промпт текстом.
- Необязательное чтение: в **Навыки → Галерея навыков → e-commerce** (Skills →
  Skill gallery → e-commerce) есть навык `product-description-writer` с теми же
  правилами, что и на этой странице, а шаблон агента `ecommerce/listing-writer` в
  той же галерее показывает более полного агента, собранного вокруг него, с
  добавленными знаниями и подключениями MCP. Навык на этой странице написан по
  содержимому той записи.

## Подготовьте входные данные { #prepare-the-input }

Сохраните это как `products.csv`. Четыре строки полные; в строке `FW-104` столбец
`material` намеренно пуст — это тот пробел, ради которого построен образец.

```csv
sku,name,category,material,size_run,weight_g,fit_note,care,box_contents
FW-101,Harbor Rain Jacket,Outerwear,recycled nylon 100%,S-XXL,410,true to size,machine wash cold,jacket and stuff sack
FW-102,Harbor Wool Beanie,Accessories,merino wool 100%,one size,80,one size fits most,hand wash cold,beanie only
FW-103,Harbor Steel Water Bottle,Drinkware,stainless steel,650 ml,320,not applicable,dishwasher safe lid only,bottle and lid
FW-104,Harbor Canvas Tote,Bags,,38 x 42 x 10 cm,260,not applicable,spot clean,tote bag only
FW-105,Harbor Trail Socks (2-pack),Apparel,merino wool blend 60%,S/M and L/XL,60,true to size,machine wash cold,two pairs of socks
```

Сохраните это как навык в **Навыки → Новый навык** (Skills → New skill) — назовите
его `listing-copy-from-spec`, взяв за основу `ecommerce/product-description-writer`
из галереи:

```text
Copy that describes a product the customer does not receive is the most
expensive sentence in e-commerce.

## Work only from the spec

Every claim traces to the spec sheet, the supplier data or a photograph. If
the material is not stated, the description does not name a material.

## The shape

One line saying what it is and who it is for; three to five bullets of
concrete attributes with numbers; one short paragraph on use; then the full
specification as given.

## Concrete beats enthusiastic

"320 gsm, pre-shrunk, fits true to size" outperforms "premium quality".
Numbers survive translation, reduce returns and answer the question that
would otherwise become a support ticket.

## Always include

Dimensions with units, materials, care, what is in the box, and — for
anything worn — the fit note. Missing fit information is the single largest
driver of apparel returns.

## Never

Claim a certification, a country of origin, a health benefit or a
compatibility that the source does not state.
```

## Создайте агента { #build-the-agent }

1. Создайте агента в **Агенты → Новый агент** (Agents → New agent) и выберите свой
   профиль модели.
2. В **Инструменты** (Toolbox) включите **Навыки** (Skills) и привяжите
   `listing-copy-from-spec`.
3. Задайте инструкции ниже, затем нажмите **Опубликовать** (Publish).

```text
You write product listing descriptions from an attached catalogue file.
Follow the bound listing-copy-from-spec skill for shape and rules.
Write one description per row of the attached CSV.
If a row is missing an attribute the skill says to always include, do not
invent it: name it as missing in that product's description instead.
Return the result as a markdown table: sku, name, then the description.
```

## Запустите его { #run-it }

Откройте новый чат с агентом, приложите `products.csv` и отправьте:

```text
Write listing descriptions for every product in the attached catalogue.
```

## Проверьте результат { #check-the-result }

| Проверка | Образец |
| --- | --- |
| Число строк | Пять описаний, по одному на каждый SKU |
| Числа | Вес, размеры и объёмы точно совпадают с CSV, с единицами измерения |
| Отсутствующий материал у FW-104 | Помечен как отсутствующий в этом описании, материал не назван |
| Заметка о посадке | Есть у FW-101, FW-102 и FW-105 — трёх вещей, которые носят |
| Атрибуты, которые никогда не заявляются | Никаких заявлений о сертификации, стране происхождения, пользе для здоровья или совместимости |
| Тот же запрос без приложенного файла | Агент говорит, что файла нет, и не выдумывает каталог |

Сначала проверьте FW-104: молча заполненный недостающий атрибут — та ошибка, ради
которой этот образец и существует.

!!! example "Записано на v0.0.504, 25 сентября 2026"

    Модель: Claude Sonnet 4.6 через OpenRouter. Агент загрузил привязанный навык,
    затем написал пять описаний в одном ответе, по одному на строку. Каждое
    число — 650 ml, 320 g, 38 x 42 x 10 cm, 60% merino, S/M и L/XL — совпало с
    CSV. Для FW-104 он написал «Состав материала не указан в данных о товаре и не
    приводится в этом описании» вместо того, чтобы назвать ткань. Заметки о
    посадке появились у куртки, шапки и носков; заявлений о сертификации,
    происхождении или пользе для здоровья не появилось нигде. Стоимость: 0,053 USD.

    Без приложенного файла агент всё равно загрузил навык, затем сказал «Я не вижу
    приложенного файла каталога или CSV в вашем сообщении» и попросил его, вместо
    того чтобы писать описания из ничего.

## Когда что-то идёт не так { #when-it-goes-wrong }

- **Для FW-104 выдуманы материал или размер.** Раздел «Never» навыка игнорируется;
  повторите инструкцию в собственном поле инструкций агента, а не только в навыке.
- **Агент вызывает `read_skill` с неверным id, и ход завершается ошибкой.** Это
  случилось при проверке на 0.0.508, когда запись из галереи установилась под
  именем `Product description writer`: модель дважды написала вместо этого имени id
  через дефис,
  и ход завершился `UnexpectedModelBehavior`, а не обычным отказом. С 0.0.509
  навыки из галереи устанавливаются под этим именем через дефис, а имя нового
  навыка должно состоять из строчных букв, цифр и дефисов
  ([#1911](https://github.com/vstorm-co/agenticos/issues/1911)). Навык,
  установленный раньше, сохраняет старое имя: установите его из галереи заново и
  привяжите новый.
- **У носимой вещи нет заметки о посадке.** Спросите, какой атрибут называет
  раздел «Always include» навыка для этой строки; отсутствующая заметка о посадке
  — та причина возвратов, ради которой навык и существует.
- **В ответе пропущена строка.** Спросите число строк, прежде чем доверять таблице:
  пять строк на входе — пять описаний на выходе.

## Зафиксируйте проверку { #record-the-trial }

Сохраните CSV, тело навыка, пять описаний, версию агента и запуск в «Активности».
Сохраните и запуск, где недостающий атрибут был выдуман, если такой случится: это
самое ясное доказательство того, что формулировки навыка нужно ужесточить.

Человек по-прежнему решает, годится ли описание для публикации, и разбирается с
помеченным пробелом до того, как товар окажется в продаже; проверка выше ловит
выдуманный факт, а не скучный.

## Что дальше { #next-steps }

Для живого каталога привяжите [коллекцию знаний](set-up-knowledge-base.md) из
спецификаций поставщиков вместо вставки строк, а из той же категории галереи возьмите
навык `ecommerce/review-response`, чтобы отвечать на вопросы покупателей о товаре с
той же дисциплиной источников.
