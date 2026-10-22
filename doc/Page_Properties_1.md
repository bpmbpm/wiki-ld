## 1

В Confluence «свойства страницы» (page properties) — это способ добавить к странице метаданные (пары «ключ-значение»), чтобы потом легко собирать из них сводки. Я подскажу, как это работает и как этим пользоваться. [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```7```](https://www.gocapable.com/blog/using-page-properties-to-summarize-information-in-confluence-pages)[```5```](https://actonic.de/en/how-to-use-page-properties-macros-in-confluence/)


[![Page Properties Macro Confluence Data Center 10.2 Atlassian Documentation](https://confluence.atlassian.com/doc/files/184550024/1005333634/1/1589961817627/page-properties-editor-vertical.png)](https://confluence.atlassian.com/doc/page-properties-macro-184550024.html?utm_source=blog&utm_medium=unpaid-social&utm_campaign=P:marketplace*O:ecosystem*H:fy22q1*I:guestblog_stiltsoft*)

## Как это устроено

Для работы с такими свойствами используют два макроса вместе:
*   **`Page Properties`** — вставляет на страницу таблицу из двух колонок. В левой — названия свойств (они станут заголовками в сводной таблице), в правой — их значения. [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```6```](https://confluence.atlassian.com/conf713/page-properties-macro-1077913741.html)
*   **`Page Properties Report`** — позволяет собрать все данные из макросов `Page Properties` на других страницах в одну сводную таблицу (например, дашборд). [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```6```](https://confluence.atlassian.com/conf713/page-properties-macro-1077913741.html)[```7```](https://www.gocapable.com/blog/using-page-properties-to-summarize-information-in-confluence-pages)

**Важное уточнение по названиям:** в Confluence Cloud эти макросы называются `Content Properties` и `Content Properties Report` — название сменили, потому что макросы работают не только на классических страницах, но и на Live Docs, в блогах и при работе с вложениями. В Data Center пока сохраняется старое название. [```5```](https://actonic.de/en/how-to-use-page-properties-macros-in-confluence/)

## Какие свойства можно задать

Вы можете фиксировать разные метаданные. Вот примеры:
*   Статус (In Progress, Completed, Under Review);
*   Ответственный (Owner или Team);
*   Срок выполнения (Deadline);
*   Приоритет (High, Medium, Low);
*   Теги (Labels) для категоризации.

 [```4```](https://www.kolekti.com/resources/blog/pape-reports-with-confluence-page-properties)

Для более сложных сценариев можно использовать сторонние приложения (например, Metadata for Confluence), чтобы добавлять собственные пользовательские поля и фильтровать по ним. [```9```](https://drivtu.com/page-properties-macro/)

## Как настроить

**Чтобы добавить `Page Properties` на страницу:**
1.  В редакторе нажмите «Insert» → «Other Macros». 
2.  Выберите `Page Properties` из категории Confluence content. 
3.  В теле макроса создайте двухколоночную таблицу. **Критически важно:** для левой колонки примените стиль «Heading column» — это сигнал для макроса `Page Properties Report`, что здесь заголовки. [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```6```](https://confluence.atlassian.com/conf713/page-properties-macro-1077913741.html)[```2```](https://confluence.atlassian.com/doc/page-properties-macro-184550024.html)
4.  В правой колонке укажите значения. 
5.  Сохраните страницу. 
6.  **Добавьте метку (label) к странице** — позже вы укажете эту метку в макросе отчёта. [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```6```](https://confluence.atlassian.com/conf713/page-properties-macro-1077913741.html)[```2```](https://confluence.atlassian.com/doc/page-properties-macro-184550024.html)

**Дополнительные опции в `Page Properties`:**
*   **`ID`** — опциональный идентификатор. Это полезно, если на одной странице несколько наборов свойств: так вы сможете отдельно ссылаться на каждый в отчёте.
*   **`Hidden`** — если включить, содержимое макроса не будет отображаться на самой странице (но это не повлияет на его отображение в сводной таблице отчёта).

 [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```2```](https://confluence.atlassian.com/doc/page-properties-macro-184550024.html)

**На странице со сводной таблицей (`Page Properties Report`):**
*   В параметре **Labels** укажите метку страницы, чтобы макрос собрал данные. [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```2```](https://confluence.atlassian.com/doc/page-properties-macro-184550024.html)
*   Можно добавить дополнительные фильтры (по типу контента, владельцу, дате последнего изменения и т.п.). [```4```](https://www.kolekti.com/resources/blog/pape-reports-with-confluence-page-properties)
*   Если нужно показать не все, а только определённые наборы свойств, укажите и метку, и ID в макросе отчёта. [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```2```](https://confluence.atlassian.com/doc/page-properties-macro-184550024.html)

## Когда это полезно

Такой подход отлично подходит для:
*   создания сводных дашбордов (например, по статусу задач в проекте); 
*   ведения реестров решений; 
*   документирования политик и процедур; 
*   быстрого обзора информации, разбросанной по разным страницам. [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```6```](https://confluence.atlassian.com/conf713/page-properties-macro-1077913741.html)[```10```](https://confluence.atlassian.com/conf720/page-properties-macro-1168861495.html)

## Несколько советов

*   Не используйте в левой колонке таблицы внутри `Page Properties` другие макросы — это нарушит логику сбора данных. [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```2```](https://confluence.atlassian.com/doc/page-properties-macro-184550024.html)
*   Если вы добавляете несколько наборов свойств на одну страницу, обязательно присваивайте им разные ID, если планируете фильтровать их в отчёте. [```1```](https://confluence.atlassian.com/conf712/page-properties-macro-1056671754.html)[```4```](https://www.kolekti.com/resources/blog/pape-reports-with-confluence-page-properties)
*   Помните: макросы работают в рамках одного инстанса Confluence. [```9```](https://drivtu.com/page-properties-macro/)

Попробуйте начать с простого примера: задайте свойства на паре страниц и соберите из них сводку на третьей. Если на каком-то шаге возникнут сложности (например, отчёт не подхватывает данные) — пишите, разберусь!

## 2

Вопрос интересный! Сразу оговорюсь: в Confluence **нет макросов, которые изначально созданы для работы с RDF** (Resource Description Framework) — это формат семантических данных из веба. Но с помощью пары встроенных инструментов и хитрых приёмов действительно можно эмулировать простую RDF-логику: хранить факты о странице и потом собирать их в сводки. Я подобрала варианты и показала, как это связать.

## Какие макросы можно использовать

Помимо `Page Properties` и `Page Properties Report`, есть ещё пара инструментов, которые в связке дают нужный эффект:
*   **`Label`** (Метки). Это не макрос для вставки контента напрямую, а система тегов. Вы можете навесить метки на страницы. Это полезно: потом в отчётах или фильтрах можно отбирать страницы по меткам, что работает как фильтр по «свойству» объекта. [```9```](https://tr-page.yandex.ru/translate?lang=en-ru&url=https%3A%2F%2Fstackoverflow.com%2Fquestions%2F26405847%2Fcustomizing-the-content-report-table-macro)
*   **`Include Page`** или **`Multiexcerpt Include`**. Они позволяют встраивать содержимое одной страницы в другую. Если на «базовой» странице есть `Page Properties`, вы можете включить её в другую и через `Page Properties Report` собрать данные из нескольких источников в одну таблицу. Так вы моделируете связи между сущностями — как трипы в RDF.

## Как эмулировать RDF

RDF обычно строится на тройках: *субъект — предикат — объект*. В Confluence это можно сымитировать так:

1.  **Субъект** — это страница.
2.  **Предикат** — ключ из макроса `Page Properties` (например, `dc:title`, `ex:author`, `os:created`).
3.  **Объект** — значение, которое вы вписываете в правую колонку таблицы внутри `Page Properties`.

**Пример:**
На странице «Документ А» вы добавляете макрос `Page Properties` и заполняете таблицу:
*   Ключ: `dc:title`, Значение: «Мой документ»
*   Ключ: `ex:author`, Значение: «Анна Смирнова»
*   Ключ: `os:created`, Значение: `2026-10-22`

Теперь, если на дашборде или сводной странице вставить макрос `Page Properties Report` и указать метку (или отобрать страницы по другому признаку), он соберёт все такие тройки в одну таблицу. Вы увидите, у кого какой документ, когда создан — прямо как в RDF-графе, только в табличном виде. [```1```](https://habr.com/ru/companies/tbank/articles/769694/)[```9```](https://tr-page.yandex.ru/translate?lang=en-ru&url=https%3A%2F%2Fstackoverflow.com%2Fquestions%2F26405847%2Fcustomizing-the-content-report-table-macro)

**Дополнительный приём с метками:** навесьте на страницы с документами метку `doc:technical`. Затем в настройках `Page Properties Report` задайте фильтр по этой метке — и в отчёт попадут только технические документы. Это эмуляция фильтрации по предикату в RDF-запросе.

## Важные ограничения

Помните: это именно эмуляция. Confluence не хранит эти данные в семантическом графе с формальной онтологией. Нет встроенного механизма для сложных логических выводов (например, «если у страницы есть свойство `is-a` = «Документ», то она должна иметь свойство `has-extension`»). Всё сводится к сбору и отображению фактов. Если вам понадобятся настоящие онтологии, сложные связи или SPARQL-запросы — придётся выходить за рамки встроенных макросов и подключать внешние инструменты или плагины из Atlassian Marketplace. [```5```](https://selectel.ru/blog/kak-ispolzovat-makrosy-v-confluence/)

## Итог

Схема такая: `Page Properties` (записываем факты) + `Page Properties Report` + `Label` (для фильтрации) + `Include Page` (для объединения данных из разных мест) = простая семантическая модель внутри Confluence.

Хотите разобрать конкретный кейс? Например, как смоделировать связь «заказ — товар — клиент» или собрать каталог продуктов? Напишите детали — подскажу, как разбить на свойства и настроить отчёт!

