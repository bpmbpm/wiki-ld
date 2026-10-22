## technological ontology
начало в start1.md

### 1
"Технологический контур wiki" - сформулируй для него онтологию. метаонтология wiki-страницы  
Сформируй короткий пример, который бы пояснял использование такой онтологии "Технологический контур wiki" - например, как механизм в confluence и аналог утверждения в RDF (эквивалентное утверждение, факт или аксиома). Сделай это в разрезе TBox, RBox, Abox.

### answer 1

Да. Для «Технологического контура wiki» нужна не онтология банка, а **метаонтология wiki-страницы**: она описывает, что такое страница, из каких механизмов Confluence извлекаются ее свойства и как эти свойства превращаются в утверждения вида «субъект — предикат — объект». В OWL утверждения онтологии называются аксиомами; индивиды, классы и связи обозначаются как individuals, classes и properties. [w3](https://www.w3.org/TR/2009/REC-owl2-primer-20091027/)

## Онтология «Технологический контур wiki»

### Ключевая идея

Страница Confluence — это не только текст. В терминах онтологии это **индивид класса `wiki:Page`**, у которого свойства распределены по нескольким физическим механизмам:

| Механизм Confluence | Онтологическая роль | Примеры |
|---|---|---|
| Заголовок страницы | Идентификатор / имя индивида | `Бизнес-процесс — Выдача кредита` |
| Родительская страница | Свойство `wiki:hasParentPage` | Родитель: `Кредитование` |
| Space | Контекст / пространство имен | `BANK-KNOWLEDGE` |
| Page Properties | Явные типизированные свойства и предикаты | `usesSystem`, `hasOwner`, `status` |
| Метки | Фасетные классификационные утверждения | `ont-process`, `domain-lending` |
| Тело страницы | Неформальное описание, документация | Текст, таблицы, макросы |
| Ссылки в тексте | Неформальные упоминания | «см. также АБС» |
| Page Properties Report | Проекция / запрос, а не свойство страницы | Реестр всех процессов |

Важно: **Page Properties Report не является свойством объекта**. Это представление, которое собирает свойства многих страниц по метке. 

## TBox: классы и таксономия

TBox — терминологическая часть: классы, их иерархия и ограничения. В OWL это аксиомы включения классов, например `C ⊑ D`. [iccl.inf.tu-dresden](https://iccl.inf.tu-dresden.de/w/images/7/79/FSWT-L13-OWL2.pdf)

```text
wiki:WikiArtifact
├── wiki:Page
├── wiki:Template
├── wiki:Label
├── wiki:Property
├── wiki:Predicate
├── wiki:Class
├── wiki:Report
└── wiki:Space

wiki:Page
├── wiki:BusinessObjectPage
├── wiki:MetaModelPage
└── wiki:NavigationPage

wiki:BusinessObjectPage
├── wiki:ProcessPage
├── wiki:ProductPage
├── wiki:SystemPage
├── wiki:DataObjectPage
├── wiki:RequirementPage
└── wiki:RiskPage
```

Примеры аксиом TBox:

```text
wiki:ProcessPage ⊑ wiki:BusinessObjectPage
wiki:BusinessObjectPage ⊑ wiki:Page
wiki:Predicate ⊑ wiki:Property
wiki:Report ⊑ wiki:WikiArtifact

wiki:ProcessPage ⊓ wiki:SystemPage ⊑ ⊥
```

Последняя аксиома означает: страница не может одновременно быть канонической страницей процесса и канонической страницей системы. Она может ссылаться на систему, но не быть ею.

## RBox: предикаты и правила связывания

RBox описывает свойства связей: иерархию предикатов, обратные связи, домены и диапазоны. В терминах дескрипционных логик RBox содержит аксиомы включения ролей, транзитивности и другие ограничения на свойства. [iccl.inf.tu-dresden](https://iccl.inf.tu-dresden.de/w/images/7/79/FSWT-L13-OWL2.pdf)

Базовые предикаты:

```text
wiki:hasParentPage
wiki:hasClass
wiki:hasLabel
wiki:hasPageProperty
wiki:hasPredicate
wiki:hasSubject
wiki:hasObject
wiki:usesSystem
wiki:performedByRole
wiki:supportsProduct
wiki:governedByRequirement
```

Примеры аксиом RBox:

```text
wiki:usesSystem ⊑ wiki:hasPredicate
wiki:performedByRole ⊑ wiki:hasPredicate
wiki:supportsProduct ⊑ wiki:hasPredicate

wiki:hasParentPage⁻ ≡ wiki:isParentPageOf
wiki:usesSystem⁻ ≡ wiki:isUsedByProcess

wiki:usesSystem domain wiki:ProcessPage
wiki:usesSystem range wiki:SystemPage

wiki:hasParentPage transitive
```

Практический смысл:

- `wiki:usesSystem` можно указывать только на странице процесса и только со ссылкой на страницу системы.
- Если процесс использует систему, то система используется процессом — это обратное отношение.
- Родительство страниц транзитивно: если `A` — родитель `B`, а `B` — родитель `C`, то `A` — предок `C`.

## ABox: конкретная страница как набор фактов

ABox — утверждения о конкретных индивидах: принадлежность классу и связи между индивидами. [iccl.inf.tu-dresden](https://iccl.inf.tu-dresden.de/w/images/7/79/FSWT-L13-OWL2.pdf)

Пусть есть страница:

```text
Бизнес-процесс — Выдача кредита
```

Ее ABox может выглядеть так:

```text
wiki:Page_BPLoanIssuance rdf:type wiki:ProcessPage .

wiki:Page_BPLoanIssuance wiki:hasParentPage wiki:Page_LendingProcesses .

wiki:Page_BPLoanIssuance wiki:hasLabel wiki:Label_ont-process .
wiki:Page_BPLoanIssuance wiki:hasLabel wiki:Label_domain-lending .
wiki:Page_BPLoanIssuance wiki:hasLabel wiki:Label_status-approved .

wiki:Page_BPLoanIssuance wiki:usesSystem wiki:Page_ABS .
wiki:Page_BPLoanIssuance wiki:performedByRole wiki:Page_Role_LoanManager .
wiki:Page_BPLoanIssuance wiki:supportsProduct wiki:Page_Product_CorporateLoan .
```

## Где физически лежат утверждения

Одно и то же логическое утверждение может быть реализовано в разных местах Confluence.

| RDF-утверждение | Где реализовано в Confluence |
|---|---|
| `wiki:Page_BPLoanIssuance rdf:type wiki:ProcessPage` | Метка `ont-process` и/или поле `Тип` в Page Properties |
| `wiki:Page_BPLoanIssuance wiki:hasParentPage wiki:Page_LendingProcesses` | Штатная родительская страница в дереве Confluence |
| `wiki:Page_BPLoanIssuance wiki:hasLabel wiki:Label_domain-lending` | Метка `domain-lending` |
| `wiki:Page_BPLoanIssuance wiki:usesSystem wiki:Page_ABS` | Строка `Использует систему` в Page Properties |
| `wiki:Page_BPLoanIssuance wiki:hasOwner wiki:Page_DepartmentLending` | Строка `Владелец` в Page Properties |
| `wiki:Page_BPLoanIssuance wiki:hasStatus "Approved"` | Строка `Статус` в Page Properties или метка `status-approved` |

Отсюда важное правило: **метка и Page Properties не должны дублировать одно и то же без контроля**. Например, класс процесса лучше задать одним авторитетным способом — либо свойством `Тип`, либо меткой `ont-process`, а второе сделать производным и проверяемым.

## Что значит «субъект по умолчанию»

Фраза означала следующее.

Когда вы на странице `Бизнес-процесс — Выдача кредита` создаете Page Properties:

| Свойство | Значение |
|---|---|
| `Использует систему` | `[ИТ-система — АБС]` |

то логическое утверждение неявно такое:

```text
Бизнес-процесс — Выдача кредита
    использует систему
        ИТ-система — АБС
```

Субъектом является **сама страница, на которой расположен блок Page Properties**. Вы не пишете ее в таблице, потому что она уже задана контекстом страницы.

В RDF это:

```text
wiki:Page_BPLoanIssuance
    wiki:usesSystem
    wiki:Page_ABS .
```

## Как задать субъект не по умолчанию

Иногда нужно выразить связь, **субъектом которой является другая страница**. Например, на странице системы `ИТ-система — АБС` нужно сказать:

```text
АБС используется процессом «Выдача кредита»
```

Есть три корректных варианта.

### Вариант 1: обратный предикат — рекомендуемый

На странице `ИТ-система — АБС` в Page Properties пишете:

| Свойство | Значение |
|---|---|
| `Используется процессом` | `[Бизнес-процесс — Выдача кредита]` |

RDF:

```text
wiki:Page_ABS
    wiki:isUsedByProcess
    wiki:Page_BPLoanIssuance .
```

А в RBox задана обратность:

```text
wiki:isUsedByProcess ≡ wiki:usesSystem⁻
```

Тогда это не дублирование факта, а **выводимое обратное утверждение**. Фактически факт хранится один раз — на странице процесса, а на странице системы его можно либо вычислять, либо поддерживать согласованным вручную.

### Вариант 2: отдельная страница связи — reification

Если связь сама имеет свойства — автора, дату, статус, источник, период действия, — создайте отдельную страницу:

```text
Связь — Выдача кредита использует АБС
```

В ее Page Properties:

| Свойство | Значение |
|---|---|
| `Тип связи` | `usesSystem` |
| `Субъект` | `[Бизнес-процесс — Выдача кредита]` |
| `Объект` | `[ИТ-система — АБС]` |
| `Утверждено` | `2026-10-22` |
| `Источник` | `Архитектурный комитет` |
| `Статус` | `Действует` |

RDF-эквивалент — реификация утверждения:

```text
wiki:Assertion_001
    rdf:type wiki:PredicateAssertion ;
    wiki:hasSubject wiki:Page_BPLoanIssuance ;
    wiki:hasPredicate wiki:usesSystem ;
    wiki:hasObject wiki:Page_ABS ;
    wiki:hasStatus "Действует" .
```

Это нужно, когда связь — самостоятельный управляемый объект: например, интеграция, договор, требование, контроль или временное отношение.

### Вариант 3: явное поле `Субъект` в Page Properties

Можно добавить в шаблон строку:

| Свойство | Значение |
|---|---|
| `Субъект` | `[Бизнес-процесс — Выдача кредита]` |
| `Предикат` | `использует систему` |
| `Объект` | `[ИТ-система — АБС]` |

Но это хуже первого и второго вариантов: вы дублируете заголовок страницы и создаете риск рассинхронизации. Используйте это только для служебных страниц-связей.

## Короткий пример: TBox, RBox, ABox

### TBox — схема

```text
Класс:
  wiki:ProcessPage

Аксиома:
  wiki:ProcessPage ⊑ wiki:BusinessObjectPage

Ограничение:
  wiki:usesSystem domain wiki:ProcessPage
  wiki:usesSystem range wiki:SystemPage
```

Смысл: страница процесса может ссылаться на систему, но не наоборот в качестве прямого предиката.

### RBox — правила связей

```text
wiki:usesSystem⁻ ≡ wiki:isUsedByProcess

wiki:usesSystem ⊑ wiki:hasPredicate
wiki:hasPredicate domain wiki:Page
wiki:hasPredicate range wiki:Page
```

Смысл: если процесс использует систему, то система используется процессом; обе стороны связи должны быть страницами.

### ABox — факты

```text
wiki:Page_BPLoanIssuance rdf:type wiki:ProcessPage .

wiki:Page_BPLoanIssuance
    wiki:usesSystem
    wiki:Page_ABS .

wiki:Page_ABS
    wiki:isUsedByProcess
    wiki:Page_BPLoanIssuance .
```

Второе утверждение либо явно хранится на странице АБС, либо выводится из первого по аксиоме обратности.

## Как это выглядит в Confluence

### На странице процесса

```text
Бизнес-процесс — Выдача кредита

Page Properties:
  Тип: Бизнес-процесс
  Использует систему: ИТ-система — АБС
  Выполняется ролью: Роль — Кредитный менеджер
  Поддерживает продукт: Продукт — Корпоративный кредит

Метки:
  ont-process
  domain-lending
  status-approved
```

Это ABox:

```text
wiki:Page_BPLoanIssuance rdf:type wiki:ProcessPage .
wiki:Page_BPLoanIssuance wiki:usesSystem wiki:Page_ABS .
wiki:Page_BPLoanIssuance wiki:performedByRole wiki:Page_Role_LoanManager .
wiki:Page_BPLoanIssuance wiki:supportsProduct wiki:Page_Product_CorporateLoan .
```

### На странице системы

```text
ИТ-система — АБС

Page Properties:
  Тип: ИТ-система
  Используется процессом: Бизнес-процесс — Выдача кредита

Метки:
  ont-system
  domain-it
  status-approved
```

Это либо дублирующий ABox, либо — при настроенной обратности — производное утверждение:

```text
wiki:Page_ABS wiki:isUsedByProcess wiki:Page_BPLoanIssuance .
```

## Практические правила

1. **Канонический факт храните один раз.** Для `процесс использует систему` источник истины — страница процесса.
2. **Обратную связь делайте производной.** На странице системы используйте `используется процессом` как обратный предикат.
3. **Не смешивайте метки и предикаты.** Метка `ont-process` — классификация страницы; `использует систему` — связь с другим объектом.
4. **Не храните в теле страницы то, что должно участвовать в отчетах.** Связи для реестров и запросов помещайте в Page Properties.
5. **Создавайте отдельную страницу связи**, только если у связи есть собственные атрибуты: срок, владелец, статус, источник, версия, критичность.
6. **Фиксируйте в B-контуре**, какой механизм является авторитетным для каждого типа утверждения.

Таким образом, «Технологический контур wiki» — это метаонтология, в которой Confluence-страница является индивидом, ее штатное положение в дереве и метки дают часть фактов, Page Properties дают типизированные факты, а Page Properties Report является не фактом, а запросом/проекцией над этими фактами. Разделение TBox, RBox и ABox позволяет четко отделить схему классов, правила связей и конкретные утверждения wiki. [w3](https://www.w3.org/TR/2009/REC-owl2-primer-20091027/)

### 2
Нужно сформулировать правила.  
Например, в Page Properties - субъект всегда название (или id?) страницы.  
Свойство - всегда предикат, а Значение - всегда RDF-объект (или литерал).  
Нужно указать перечень обязательных свойств страницы wiki, но которые не попадают в Page Properties, например, материнская страница, адрес страницы и т.п.

### answer 2

Да. Нужно разделить **логическую модель утверждения**, **физические механизмы Confluence** и **правила выбора механизма**. Ключевой принцип: страница — это идентификатор субъекта; Page Properties — основной способ задать типизированные предикаты; метки — индексные фасеты для поиска и выборок; штатные атрибуты страницы — системные свойства, которые не следует дублировать в Page Properties. Confluence идентифицирует страницу по `id`, хранит `title`, `spaceId`, `parentId`, а метки и properties доступны как отдельные сущности API. [developer.atlassian](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-page/)

## Базовая модель утверждения

Каждое формальное знание в wiki должно быть выразимо в виде:

```text
<Субъект> <Предикат> <Объект | Литерал>
```

| Компонент | В Confluence | Правило |
|---|---|---|
| Субъект | Страница, однозначно идентифицируемая `pageId` | Всегда страница, на которой расположено утверждение, либо явно указанная страница в странице-связи |
| Предикат | Имя строки в Page Properties либо системный атрибут | Должен принадлежать утвержденному словарю предикатов |
| Объект | Гиперссылка на другую страницу | Используется, когда объект — индивид онтологии |
| Литерал | Текст, число, дата, статус | Используется, когда объект — значение, а не страница |

Пример:

```text
wiki:Page_12345  wiki:usesSystem  wiki:Page_67890 .
wiki:Page_12345  wiki:hasStatus    "Approved" .
wiki:Page_12345  wiki:hasParentPage wiki:Page_11111 .
```

Здесь `wiki:Page_12345` — субъект, `wiki:usesSystem` — предикат, `wiki:Page_67890` — объект-страница, а `"Approved"` — литерал.

## Правило идентификации субъекта

**Субъект — это `pageId`, а не заголовок страницы.**

Заголовок удобен человеку, но не является устойчивым идентификатором: его можно переименовать. Confluence идентифицирует страницу по `id`; заголовок уникален в пределах пространства, но может меняться. [developer.atlassian](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-page/)

Правила:

1. Каждая каноническая страница объекта должна иметь внутренний код: `entityId`, например `BP-CR-003`.
2. `entityId` размещается в Page Properties как обязательное свойство.
3. `pageId` — технический идентификатор Confluence; он не обязательно показывать пользователю, но должен использоваться в интеграциях, экспорте и автоматических проверках.
4. Заголовок — человекочитаемая метка, а не первичный ключ.
5. При переименовании страницы `pageId` сохраняется; ссылки и отчеты продолжают работать.

Рекомендуемая схема идентификации:

```text
pageId       = технический ID Confluence
entityId     = бизнес-код объекта, например BP-CR-003
title        = человекочитаемое имя, например
               «Бизнес-процесс — Выдача кредита»
```

## Правила Page Properties

В Page Properties действует следующая грамматика:

```text
Страница (субъект)
└── Page Properties
    ├── Левая колонка  = предикат
    └── Правая колонка = RDF-объект или литерал
```

Правила:

1. **Субъект по умолчанию** — страница, содержащая блок Page Properties.
2. **Не указывайте субъекта в обычном Page Properties.** Он уже задан самой страницей.
3. **Левая колонка — только предикат из словаря.** Нельзя писать свободные формулировки вроде «важная информация».
4. **Правая колонка — либо ссылка на страницу, либо литерал.** Смешивать их в одном свойстве нельзя.
5. **Для объектных свойств используйте гиперссылку на каноническую страницу объекта.** Не пишите название системы текстом без ссылки.
6. **Для литералов используйте контролируемые значения**, если они есть: `Draft`, `Approved`, `Retired`; `High`, `Medium`, `Low`.
7. **Один предикат может повторяться**, если связь многозначная: процесс может использовать несколько систем.
8. **Не дублируйте системные свойства Confluence** — родителя, адрес, автора, дату изменения — в Page Properties.
9. **Если субъект не совпадает со страницей**, создавайте отдельную страницу-связь или используйте обратный предикат.

Пример корректного Page Properties:

| Предикат | Объект / литерал |
|---|---|
| `entityId` | `BP-CR-003` |
| `hasClass` | `[Класс — Бизнес-процесс]` |
| `hasOwner` | `[Организация — Департамент кредитования]` |
| `usesSystem` | `[ИТ-система — АБС]` |
| `usesSystem` | `[ИТ-система — Кредитный конвейер]` |
| `supportsProduct` | `[Продукт — Корпоративный кредит]` |
| `hasStatus` | `Approved` |

## Обязательные свойства вне Page Properties

Некоторые свойства уже являются **системными атрибутами страницы Confluence**. Их не нужно повторять в Page Properties: они должны считываться из API или использоваться напрямую в CQL/отчетах.

| Свойство | Где хранится в Confluence | RDF-предикат | Правило |
|---|---|---|---|
| Идентификатор страницы | `pageId` | `wiki:hasConfluenceId` | Технический первичный ключ |
| Заголовок | Title | `rdfs:label` | Человекочитаемое имя |
| Пространство | Space | `wiki:inSpace` | Контекст / пространство имен |
| Родительская страница | Parent page | `wiki:hasParentPage` | Навигационная иерархия |
| Путь / адрес | URL или `pageId` | `wiki:hasLocator` | Технический адрес ресурса |
| Автор | Author | `dcterms:creator` | Кто создал страницу |
| Последний редактор | Last modifier | `dcterms:modified` / `wiki:lastModifiedBy` | Техническая версия |
| Дата создания | Created date | `dcterms:created` | Техническая версия |
| Дата изменения | Last modified | `dcterms:modified` | Техническая версия |
| Версия | Page version | `wiki:hasVersion` | История изменений |
| Статус контента | Current / draft / archived | `wiki:contentStatus` | Жизненный цикл самой страницы |
| Ограничения доступа | Restrictions | `wiki:hasAccessRestriction` | Безопасность, не бизнес-онтология |
| Вложения | Attachments | `wiki:hasAttachment` | Отдельные ресурсы |
| Комментарии | Comments | `wiki:hasComment` | Обсуждение, не формальный факт |

Confluence REST API возвращает `id`, `title`, `spaceId`, `parentId`, автора, статус и другие атрибуты страницы; метки и properties также являются отдельными сущностями. [developer.atlassian](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-page/)

**Правило:** эти свойства можно отображать в отчетах и экспортировать в RDF, но **не копировать в Page Properties**. Иначе возникнут две версии истины.

## Модель «страница объекта»

Страница wiki — это **представление объекта**, а не сам объект в строгом смысле. Поэтому модель должна содержать две разные вещи:

```text
Объект онтологии           Страница wiki
Бизнес-процесс     ←──→    Страница «Бизнес-процесс — Выдача кредита»
```

Формально:

```text
wiki:Page_12345
    wiki:representsEntity wiki:Entity_BP-CR-003 .

wiki:Entity_BP-CR-003
    rdf:type ont:BusinessProcess .
```

Но в практической упрощенной модели Confluence допустимо считать:

```text
wiki:Page_12345 rdf:type wiki:ProcessPage .
```

при условии, что страница является единственным каноническим представлением объекта.

**Рекомендация:** используйте двухслойную модель, если планируется интеграция с внешними системами или RDF/OWL. Используйте однослойную модель «страница = объект», если wiki остается единственным источником истины.

### Как из типа объекта получить обязательные предикаты

Тип объекта задается свойством `hasClass` или меткой `ont-*`. Далее шаблон и валидатор определяют набор предикатов.

Пример:

```text
Класс: Бизнес-процесс

Обязательные предикаты:
  entityId
  hasOwner
  hasStatus
  hasParentProcess
  performedByRole
  usesSystem
  processesData

Опциональные предикаты:
  supportsProduct
  governedByRequirement
  mitigatesRisk
  hasKPI
  replaces
```

Это задается в модели классов:

| Класс | Обязательный предикат | Кардинальность | Тип значения |
|---|---|---:|---|
| Бизнес-процесс | `entityId` | 1 | Литерал |
| Бизнес-процесс | `hasOwner` | 1 | Страница |
| Бизнес-процесс | `hasStatus` | 1 | Литерал из справочника |
| Бизнес-процесс | `usesSystem` | 0..* | Страница |
| Бизнес-процесс | `processesData` | 0..* | Страница |
| Бизнес-процесс | `supportsProduct` | 0..* | Страница |

## Полный справочник свойств страницы

Создайте в `B1. Модель классов` страницу **«Реестр свойств страниц»** со следующей структурой.

| Свойство | RDF-предикат | Тип | Где хранится | Обязательность | Назначение |
|---|---|---|---|---|---|
| `pageId` | `wiki:hasConfluenceId` | Идентификатор | Системное свойство Confluence | Автоматически | Технический ключ |
| `title` | `rdfs:label` | Строка | Заголовок страницы | Обязательно | Имя для человека |
| `space` | `wiki:inSpace` | Ссылка | Space | Автоматически | Контекст |
| `parentPage` | `wiki:hasParentPage` | Страница | Дерево страниц | Обязательно | Навигация |
| `pageUrl` | `wiki:hasLocator` | URI | Системный атрибут | Автоматически | Адрес ресурса |
| `author` | `dcterms:creator` | Пользователь | Системный атрибут | Автоматически | Аудит |
| `lastModified` | `dcterms:modified` | Дата/время | Системный атрибут | Автоматически | Аудит |
| `version` | `wiki:hasVersion` | Число | Системный атрибут | Автоматически | История |
| `contentStatus` | `wiki:contentStatus` | Литерал | Системный атрибут | Автоматически | Жизненный цикл страницы |
| `entityId` | `wiki:hasEntityId` | Литерал | Page Properties | Обязательно | Бизнес-ключ объекта |
| `hasClass` | `wiki:hasClass` | Страница класса | Page Properties | Обязательно | Тип объекта |
| `hasOwner` | `wiki:hasOwner` | Страница | Page Properties | Зависит от класса | Владелец |
| `hasStatus` | `wiki:hasStatus` | Литерал | Page Properties | Обязательно | Жизненный цикл объекта |
| `usesSystem` | `wiki:usesSystem` | Страница | Page Properties | Для процессов | Объектная связь |
| `processesData` | `wiki:processesData` | Страница | Page Properties | Для процессов | Объектная связь |
| `supportsProduct` | `wiki:supportsProduct` | Страница | Page Properties | Для процессов | Объектная связь |
| `governedByRequirement` | `wiki:governedByRequirement` | Страница | Page Properties | Опционально | Комплаенс |
| `hasLabel` | `wiki:hasLabel` | Метка | Labels | Управляемо | Поиск и фасеты |
| `hasAccessRestriction` | `wiki:hasAccessRestriction` | Ограничение | Restrictions | По необходимости | Доступ |

## Зачем нужны метки, если есть Page Properties

Метки нужны не вместо Page Properties, а для **глобального индексирования и выборок**.

Page Properties сами по себе — это структурированные данные на конкретной странице. Но чтобы собрать все процессы, все системы или все требования в один отчет, Confluence должен знать, **какие страницы включить в выборку**. Для этого Page Properties Report использует метку страницы. [confluence.atlassian](https://confluence.atlassian.com/conf718/page-properties-report-macro-1129689038.html)

| Задача | Page Properties | Метки |
|---|---|---|
| Указать, что процесс использует АБС | Да | Нет |
| Указать владельца процесса | Да | Нет |
| Указать статус `Approved` | Да | Возможно, дублирование |
| Собрать все процессы в реестр | Нет, сама по себе | Да, через `ont-process` |
| Отфильтровать объекты домена «Кредитование» | Нет | Да, через `domain-lending` |
| Найти все критичные ИТ-системы | Нет | Да, через `ont-system` + `crit-high` |
| Использовать CQL-поиск по меткам | Нет | Да |
| Ограничить отчет ветвью дерева | Да, фильтром `With ancestor` | Да, фильтром `Label` |

Page Properties Report требует метку для выбора страниц, а CQL поддерживает фильтрацию по меткам, предку, прямому родителю и заголовку. [confluence.atlassian](https://confluence.atlassian.com/conf718/page-properties-report-macro-1129689038.html)

## Правило разделения механизмов

Используйте такой принцип:

| Тип утверждения | Механизм | Пример |
|---|---|---|
| Техническая идентификация | Системные атрибуты | `pageId`, `title`, `space`, `parentPage` |
| Класс объекта | Page Properties `hasClass` + метка `ont-*` | `hasClass = Бизнес-процесс`, `ont-process` |
| Бизнес-связь | Page Properties | `usesSystem`, `hasOwner`, `supportsProduct` |
| Значение | Page Properties | `hasStatus = Approved` |
| Фасет поиска | Метка | `domain-lending`, `crit-high` |
| Неформальный контекст | Текст страницы | Описание, инструкции, примеры |
| Сводная выборка | Page Properties Report | Реестр процессов |

## Практическое правило по меткам

Метки должны кодировать только **фасеты, по которым нужен быстрый поиск или автоматическая выборка**.

Рекомендуемый минимальный набор:

```text
ont-process
ont-product
ont-system
ont-data
ont-requirement
ont-risk

domain-lending
domain-it
domain-risk
domain-hr

status-draft
status-review
status-approved
status-retired

crit-high
crit-medium
crit-low
```

При этом:

- `ont-process` должен соответствовать `hasClass = Бизнес-процесс`;
- `status-approved` должен соответствовать `hasStatus = Approved`;
- `domain-lending` может быть отдельным свойством `belongsToDomain`, но метка нужна, чтобы использовать его в CQL и Page Properties Report;
- произвольные метки запрещены;
- каждая метка должна иметь владельца и описание в словаре меток.

## Итоговая формула

```text
Страница Confluence = субъект

Заголовок, pageId, space, parentPage
    = системные свойства страницы

Page Properties:
    левая колонка  = предикат
    правая колонка = объект-страница или литерал

Метки:
    фасетные утверждения для поиска,
    фильтрации и сборки отчетов

Модель классов:
    определяет обязательные и опциональные предикаты

Словарь предикатов:
    определяет допустимые субъекты, объекты,
    кардинальность и направление связи

Page Properties Report:
    не хранит знание, а строит проекцию
    по метке и другим фильтрам
```

Таким образом, метки не заменяют Page Properties: **Page Properties хранят семантику, метки обеспечивают индексацию и выборки**. Системные свойства Confluence не дублируются, а отображаются или экспортируются как уже существующие факты о странице. [developer.atlassian](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-page/)


### 3

Если "Page Properties хранят семантику" но мы не можем использовать поиск для поиска типа найди все страницы, где Свойство = А, а Значение = B, т.е. поиск по "Ключ - Значение", то смысл в Page Properties - непонятен. 

Вы правы: в **штатном Confluence нельзя полноценно искать по произвольной паре «ключ Page Properties = значение»**. Штатный Page Properties Report выбирает страницы прежде всего по метке и CQL-фильтрам, а значения из Page Properties в основном отображает колонками; полноценный поиск по произвольным key-value свойствам требует content properties/search extensions или стороннего приложения. [confluence.atlassian](https://confluence.atlassian.com/conf719/page-properties-report-macro-1157466853.html)

Поэтому уточню прежнюю формулу: **Page Properties хранят семантику, но не обязательно обеспечивают ее поиск**. Если поиск по семантике нужен, свойства надо либо индексировать через Content Properties/Search Extensions, либо использовать приложение, либо выгружать их во внешний граф знаний.

## Что умеет штатный Confluence

| Возможность | Штатный Confluence | Комментарий |
|---|---|---|
| Хранить key-value на странице | Да | Page Properties |
| Показать значения в таблице | Да | Page Properties Report |
| Выбрать страницы по метке | Да | `Label = ont-process` |
| Выбрать страницы по CQL-полям | Да | пространство, предок, заголовок, тип контента и др. |
| Найти все страницы, где `usesSystem = АБС` | **Нет, нативно** | Значение Page Properties не является индексируемым CQL-полем |
| Найти все страницы, где `hasStatus = Approved` | **Нет, нативно** | Нужны content properties, приложение или экспорт |
| Построить произвольный графовый запрос | Нет | Нужен внешний граф или специализированное приложение |

Штатный макрос Page Properties Report поддерживает CQL-фильтры и операторы `AND`, `OR`, `NOT`, но фильтр `Label` является основным способом отбора страниц; произвольные пары «свойство Page Properties — значение» в этот механизм напрямую не попадают. [confluence.atlassian](https://confluence.atlassian.com/conf719/page-properties-report-macro-1157466853.html)

## Как сделать поиск по «ключ — значение»

Есть три рабочих варианта.

### Вариант 1: Content Properties + Search Extensions

Это наиболее «нативный» путь для Confluence Cloud.

Идея: помимо видимого макроса Page Properties, сохранять те же семантические факты как **content properties** — JSON key-value пары, привязанные к странице. Если свойство объявлено индексируемым, по нему можно строить CQL-запросы и фильтры в макросах. [developer.atlassian](https://developer.atlassian.com/cloud/confluence/search-extensions/)

Пример content property для страницы процесса:

```json
{
  "key": "ontology",
  "value": {
    "class": "BusinessProcess",
    "entityId": "BP-CR-003",
    "status": "Approved",
    "usesSystem": ["ABS", "LoanWorkflow"],
    "domain": ["lending", "risk"]
  }
}
```

Тогда возможен запрос вида:

```text
Найти страницы, где:
  ontology.class = BusinessProcess
  AND ontology.status = Approved
  AND ontology.usesSystem = ABS
```

Confluence официально описывает Search Extensions именно так: content properties — key-value пары на странице или записи блога; после объявления свойства индексируемым можно использовать его в CQL и фильтрах макросов. [developer.atlassian](https://developer.atlassian.com/cloud/confluence/search-extensions/)

**Плюсы:**

- поиск по семантическим свойствам;
- нет дублирования в метках;
- свойства остаются машиночитаемыми;
- можно строить отчеты и фильтры.

**Минусы:**

- требуется интеграция через REST API;
- Page Properties и content properties нужно синхронизировать;
- настройка нетривиальна для обычных пользователей.

### Вариант 2: сторонние приложения

Если Confluence Data Center/Server и доступна установка приложений, можно использовать расширения отчетности.

Примеры:

- **Table Filter, Charts & Spreadsheets / Page Properties Report от StiltSoft** — позволяет строить отчеты по Page Properties и использовать SQL-подобные запросы к собранным данным. [docs.stiltsoft](https://docs.stiltsoft.com/spaces/TFACDRAFTS/pages/162269579/Page+Properties+Report)
- **EPS Page Properties Report / EDM macros** — индексирует значения внутри Page Properties и позволяет фильтровать их через CQL и Page Properties Report. [doc.eps](https://doc.eps.ch/pedms/latest/filtering-with-the-page-property-report-macro)

С такими инструментами запрос «найди все процессы, где `Использует систему = АБС` и `Статус = Действует`» становится возможным непосредственно в Confluence.

**Плюсы:**

- привычный интерфейс Confluence;
- Page Properties остаются источником семантики;
- можно строить интерактивные реестры.

**Минусы:**

- зависимость от приложения;
- различия между Cloud и Data Center;
- требуется лицензирование и администрирование.

### Вариант 3: экспорт Page Properties во внешний граф

Это правильный вариант, если нужна настоящая семантическая модель, транситивные запросы, reasoning, версии онтологии и интеграция с другими системами.

Схема:

```text
Confluence
├── Page Properties
├── Метки
├── Дерево страниц
└── REST API
        ↓ ETL / синхронизация
Промежуточная модель JSON / CSV
        ↓ преобразование
Граф знаний
├── Neo4j / RDF Store / Oxigraph / GraphDB
        ↓
SPARQL / Cypher / API
        ↓
Confluence-страницы, отчеты, дашборды
```

Confluence Cloud REST API возвращает `id`, `title`, `spaceId`, `parentId`, статус и другие атрибуты страницы; это позволяет построить стабильный экспорт структуры и свойств. 

## Зачем тогда метки

Метки остаются полезными, но их роль сужается.

| Роль | Метки | Page Properties | Content Properties |
|---|---|---|---|
| Быстрая ручная классификация | Да | Нет | Нет |
| Поиск в штатном интерфейсе | Да | Ограниченно | Да, если индексированы |
| Вход в Page Properties Report | Да | Нет | Зависит от реализации |
| Типизированная связь объект-объект | Нет | Да | Да |
| Значение с типом и контролируемым словарем | Ограниченно | Да | Да |
| Машинная интеграция | Ограниченно | Через API | Да |
| Граф знаний | Как вспомогательный фасет | Да | Да |

Метки нужны, если:

- вы не можете использовать content properties или приложения;
- нужен простой поиск и навигация без интеграций;
- метка служит «точкой входа» для Page Properties Report;
- нужны фасеты, понятные пользователю: `domain-lending`, `status-approved`, `crit-high`.

Метки можно исключить или свести к минимуму, если:

- все семантические свойства хранятся в content properties;
- эти свойства индексируются;
- отчеты и поиск строятся по CQL;
- Page Properties Report настраивается через content properties, а не через label.

Практически разумный компромисс: **метки — только для класса объекта и жизненного цикла**, а все предметные связи — в Page Properties/content properties.

```text
Метки:
  ont-process
  status-approved

Page Properties:
  entityId = BP-CR-003
  hasOwner = Департамент кредитования
  usesSystem = АБС
  supportsProduct = Корпоративный кредит

Content Properties:
  class = BusinessProcess
  status = Approved
  usesSystem = [ABS]
  domain = [lending]
```

## Как построить граф знаний в Confluence

### Шаг 1. Определить графовую модель

Зафиксируйте три типа узлов и два типа ребер.

```text
Узлы:
  wiki:Page
  wiki:Entity
  wiki:Label

Ребра:
  wiki:Page      --represents-->  wiki:Entity
  wiki:Page      --hasLabel-->    wiki:Label
  wiki:Page      --hasParent-->   wiki:Page
  wiki:Entity    --usesSystem-->  wiki:Entity
  wiki:Entity    --hasOwner-->    wiki:Entity
```

Более строгая модель:

```text
wiki:Page_12345
    wiki:representsEntity wiki:Entity_BP-CR-003 .

wiki:Entity_BP-CR-003
    rdf:type ont:BusinessProcess ;
    ont:usesSystem ont:Entity_ABS ;
    ont:hasOwner ont:Entity_DepLending .
```

### Шаг 2. Извлечь данные из Confluence

Соберите по каждой странице:

- `pageId`;
- `title`;
- `spaceId`;
- `parentId`;
- labels;
- Page Properties;
- content properties, если используются.

Источник — REST API Confluence. 

### Шаг 3. Преобразовать в триплеты

Правила преобразования:

| Источник в Confluence | Триплет |
|---|---|
| `pageId` | `wiki:Page_12345 wiki:hasConfluenceId "12345"` |
| Заголовок | `wiki:Page_12345 rdfs:label "Бизнес-процесс — Выдача кредита"` |
| Родитель | `wiki:Page_12345 wiki:hasParentPage wiki:Page_11111` |
| Метка | `wiki:Page_12345 wiki:hasLabel wiki:Label_ont-process` |
| Page Properties `Тип` | `wiki:Page_12345 wiki:hasClass wiki:Class_Process` |
| Page Properties `Использует систему` | `wiki:Page_12345 wiki:usesSystem wiki:Page_ABS` |
| Content Properties `status` | `wiki:Page_12345 wiki:hasStatus "Approved"` |

### Шаг 4. Загрузить в граф

Для RDF:

```turtle
@prefix wiki: <https://example.org/wiki/> .
@prefix ont:  <https://example.org/ontology/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .

wiki:Page_12345
    a ont:BusinessProcessPage ;
    rdfs:label "Бизнес-процесс — Выдача кредита" ;
    wiki:hasParentPage wiki:Page_11111 ;
    wiki:hasLabel wiki:Label_ont-process ;
    wiki:usesSystem wiki:Page_ABS ;
    wiki:supportsProduct wiki:Page_Product_CorporateLoan .
```

Для Neo4j:

```cypher
MERGE (p:Page {id: "12345"})
SET p.title = "Бизнес-процесс — Выдача кредита"

MERGE (s:System {id: "ABS"})
SET s.title = "АБС"

MERGE (p)-[:USES_SYSTEM]->(s)
```

### Шаг 5. Выполнять графовые запросы

Примеры вопросов, которые нельзя удобно решить штатным поиском Confluence:

```text
Найти все процессы, использующие АБС.
```

SPARQL:

```sparql
SELECT ?process ?title
WHERE {
  ?process a ont:BusinessProcess ;
           ont:usesSystem ont:Entity_ABS ;
           rdfs:label ?title .
}
```

Cypher:

```cypher
MATCH (p:Page)-[:USES_SYSTEM]->(s:System {id: "ABS"})
RETURN p.title
```

Более сложный запрос:

```text
Найти все ИТ-системы, которые затрагиваются процессами,
подчиненными требованию 115-ФЗ.
```

Это уже цепочка:

```text
Требование ← подчиняется ← Процесс → использует → Система
```

В Confluence такой обход связей штатными средствами практически недоступен; во внешнем графе это обычный path query.

## Рекомендуемая архитектура

Для вашего случая предлагаю такую схему:

```text
Confluence
│
├── Шаблоны и Page Properties
│   └── источник семантики для человека
│
├── Метки
│   └── фасеты для штатного поиска и Page Properties Report
│
├── Content Properties / Search Extensions
│   └── индексируемые key-value для CQL-поиска
│
└── REST API
    └── экспорт в граф знаний
        ├── RDF Store + SPARQL
        └── Neo4j + Cypher
```

## Практический вывод

- **Если нужен только Confluence без плагинов:** Page Properties полезны для структурирования и отчетов в пределах страницы/ветки, а метки — для поиска и Page Properties Report. Поиск по произвольному `ключ = значение` будет ограниченным.
- **Если нужен поиск по свойствам:** используйте Content Properties + Search Extensions или сторонний reporting-плагин. [developer.atlassian](https://developer.atlassian.com/cloud/confluence/search-extensions/)
- **Если нужен настоящий граф знаний:** Confluence — редактор и источник фактов, а граф хранится во внешней системе; Page Properties экспортируются в RDF или property graph.
- **Метки не исчезают полностью:** они остаются дешевым штатным индексом, но не должны дублировать все свойства. Оптимально оставить в метках только класс, статус и, при необходимости, домен.

CQL-поле (Confluence Query Language field) — это элемент языка CQL, который обозначает индексируемое свойство контента в Confluence. Проще говоря, это «параметр», по которому вы фильтруете результаты поиска: например, ищете страницы с определённой меткой (label), созданные автором (creator) или обновлённые после конкретной даты. 

## 4

Какие сторонние плагины решают эту проблему

Да, есть несколько категорий плагинов. Важно сразу разделить: одни позволяют **фильтровать уже собранную таблицу Page Properties**, другие — **индексируют свойства и делают их доступными для CQL-поиска**, третьи — создают **структурированные объекты и связи**, пригодные для графа знаний.

## 1. StiltSoft: Table Filter, Charts & Spreadsheets

**Что решает:** фильтрацию, сортировку, агрегацию и визуализацию данных из Page Properties Report.

Приложение работает поверх штатного Page Properties Report: сначала штатный макрос собирает страницы по метке и выводит их свойства в таблицу, затем Table Filter добавляет интерактивные фильтры по колонкам, pivot-таблицы, диаграммы и SQL-подобные преобразования. [marketplace.atlassian](https://marketplace.atlassian.com/apps/27447/table-filter-charts-spreadsheets-for-confluence)

**Как это работает:**

```text
Page Properties на страницах процессов
        ↓
Page Properties Report по метке ont-process
        ↓
Table Filter / Pivot Table / Chart from Table
        ↓
Интерактивный реестр: фильтр по системе, владельцу, статусу
```

**Можно получить:**

- все процессы, где в колонке `Использует систему` указана АБС;
- все системы с `Критичность = High`;
- сводную таблицу «Процесс — Система — Продукт»;
- pivot по доменам и статусам;
- диаграммы распределения объектов.

**Ограничение:** фильтры применяются к строкам, уже отображенным Page Properties Report; приложение не превращает само по себе Page Properties в индексируемые CQL-поля и не заменяет полноценный графовый запрос. [stiltsoft](https://stiltsoft.com/blog/table-filter-and-charts-with-page-properties-report/)

**Когда выбирать:** если нужен удобный корпоративный каталог и интерактивные реестры внутри Confluence, без внешнего графа.

Ссылка: [Table Filter, Charts & Spreadsheets for Confluence](https://marketplace.atlassian.com/apps/27447/table-filter-charts-spreadsheets-for-confluence). [marketplace.atlassian](https://marketplace.atlassian.com/apps/27447/table-filter-charts-spreadsheets-for-confluence)

## 2. Junovi: Properties

**Что решает:** наиболее близко к вашей задаче — поиск и фильтрацию по **значениям свойств**, а не только по меткам.

Приложение использует пару макросов:

- `Property Group` — задает структурированные свойства на странице;
- `Property Group Report` — собирает страницы и позволяет фильтровать по значениям свойств, меткам, пространствам и ветке дерева. [seibert](https://seibert.group/blog/en/keep-track-goals-objectives-confluence/)

**Ключевая возможность:** в отчете есть фильтр **Property Value(s)**, который возвращает страницы с заданными значениями свойств; несколько фильтров объединяются через `AND`, а несколько значений внутри одного фильтра — через `OR`. [junovi.atlassian](https://junovi.atlassian.net/wiki/spaces/PROP/pages/2145255476)

Пример:

```text
Property Group: Бизнес-процесс

Свойства:
  Владелец
  Использует систему
  Поддерживает продукт
  Статус
  Критичность
```

Запрос в Property Group Report:

```text
Свойство: Использует систему = АБС
AND
Свойство: Статус = Действует
AND
Метка: domain-lending
```

Результат — все процессы кредитования, использующие АБС и находящиеся в статусе «Действует».

**Плюсы:**

- именно поиск по `Свойство = Значение`;
- отчеты в виде таблицы или карточек;
- комбинация свойств, меток, пространств и предков;
- меньше необходимости дублировать семантику метками.

**Ограничение:** это собственная модель свойств приложения, а не штатный Page Properties. Если у вас уже много данных в Page Properties, потребуется миграция или параллельное ведение.

Ссылка: [Properties — Confluence](https://junovi.atlassian.net/wiki/spaces/PROP/pages/2145255476). [junovi.atlassian](https://junovi.atlassian.net/wiki/spaces/PROP/pages/2145255476)

## 3. EPS: Page Properties Report / EDM Macros

**Что решает:** индексацию значений Page Properties и фильтрацию через CQL.

У EPS есть механизм, при котором специальные макросы размещаются внутри Page Properties; значения автоматически индексируются и становятся доступны для CQL-фильтрации и Page Properties Report. 

**Как это работает:**

```text
Page Properties
└── EDM macro / индексируемое поле
        ↓
Индекс Confluence
        ↓
CQL-фильтр / Page Properties Report
```

**Пример запроса:**

```text
Найти страницы, где:
  status = Approved
  AND system = ABS
```

**Плюсы:**

- сохраняется работа со штатным Page Properties;
- значения становятся доступными для поиска;
- можно строить CQL-запросы без выгрузки во внешний граф.

**Ограничение:**

- требуется конкретное приложение EPS;
- нужно проверить совместимость с вашей версией Confluence — Cloud или Data Center;
- модель индексируемых полей нужно проектировать заранее.

Ссылка: [Filtering with the Page Property Report Macro — EPS](https://doc.eps.ch/pedms/latest/filtering-with-the-page-property-report-macro). 

## 4. Atlassian: Content Properties + Search Extensions

Это не сторонний плагин, а **нативный механизм Confluence Cloud**, но его стоит рассматривать как альтернативу плагинам.

Content Properties — это key-value данные, привязанные к странице. Search Extensions позволяют объявить такие свойства индексируемыми и использовать их в CQL-поиске и фильтрах. 

**Как это работает:**

```text
Страница Confluence
├── Page Properties — видимое представление для пользователя
└── Content Properties — машиночитаемое key-value хранилище
        ↓
Индекс Confluence
        ↓
CQL: найти по свойству и значению
```

**Пример:**

```json
{
  "key": "ontology",
  "value": {
    "class": "BusinessProcess",
    "status": "Approved",
    "usesSystem": "ABS"
  }
}
```

**Плюсы:**

- нет сторонней зависимости;
- свойства доступны для CQL;
- можно синхронизировать с Page Properties через REST API;
- лучше подходит для последующего экспорта в граф знаний.

**Минусы:**

- требуется разработка/интеграция;
- пользовательский интерфейс менее удобен, чем у специализированных приложений;
- в Data Center реализация и возможности могут отличаться.

Ссылка: [Confluence Search Extensions](https://developer.atlassian.com/cloud/confluence/search-extensions/). 

## 5. Собственный плагин: CQL field + extractor

Для Data Center/Server можно разработать собственное приложение, которое добавляет произвольное свойство страницы в CQL-индекс.

Atlassian описывает схему: listener создает/обновляет свойство, extractor помещает значение в поисковый индекс, CQL field handler обрабатывает выражение вида `status = "Approved"`, а REST-ресурс обеспечивает автодополнение в расширенном поиске. [developer.atlassian](https://developer.atlassian.com/server/confluence/adding-a-field-to-cql/)

**Когда оправдано:**

- есть команда разработки Atlassian-приложений;
- нужен строгий контроль индексации;
- требуется интеграция с корпоративным поиском;
- нельзя использовать внешнюю графовую БД или marketplace-приложения.

**Когда не оправдано:** если задача решается стандартным Content Properties, Properties от Junovi или StiltSoft.

## Сравнение

| Решение | Поиск `Свойство = Значение` | Работа с Page Properties | Граф знаний | Сложность |
|---|---|---|---|---|
| Штатный Page Properties Report | Нет / очень ограниченно | Да | Нет | Низкая |
| **StiltSoft Table Filter** | Фильтрация собранной таблицы | Да, через Report | Нет | Низкая |
| **Junovi Properties** | **Да, по значениям свойств** | Нет, своя модель Property Group | Частично | Средняя |
| **EPS EDM Macros** | **Да, через CQL** | Да | Частично | Средняя |
| **Content Properties + Search Extensions** | **Да, через CQL** | Требует синхронизации | Хорошая основа | Средняя/высокая |
| Собственный CQL-плагин | Да | Да | Хорошая основа | Высокая |
| Внешний RDF/Neo4j граф | Да, включая транзитивные запросы | Через экспорт | **Да** | Высокая |

## Что выбрать

| Сценарий | Рекомендация |
|---|---|
| Нужны удобные реестры и фильтры в Confluence | **StiltSoft Table Filter, Charts & Spreadsheets**  [marketplace.atlassian](https://marketplace.atlassian.com/apps/27447/table-filter-charts-spreadsheets-for-confluence) |
| Нужен поиск именно по `Свойство = Значение` без внешней системы | **Junovi Properties**  [seibert](https://seibert.group/blog/en/keep-track-goals-objectives-confluence/) |
| Нужен CQL-поиск при сохранении Page Properties | **EPS EDM Macros** или Content Properties + Search Extensions  |
| Нужны транзитивные запросы, reasoning, RDF/OWL | **Confluence + REST API + внешний RDF Store или Neo4j** |
| Нужен корпоративный семантический каталог с минимальными изменениями UX | **Junovi Properties + StiltSoft** |
| Нужна долгосрочная онтологическая платформа | **Confluence как редактор + внешний граф знаний** |

## Практическая рекомендация

Для корпоративной wiki банка я бы выбрал двухуровневую архитектуру:

```text
Уровень 1. Confluence
├── Page Properties — семантика для пользователей
├── Метки — класс, статус, домен
└── Junovi Properties или EPS
    └── поиск по значениям свойств

Уровень 2. Граф знаний
└── Экспорт Page Properties / Content Properties через REST API
    └── RDF Store или Neo4j
        └── SPARQL / Cypher, транситивные запросы, валидация онтологии
```

Если можно установить только одно приложение, начните с **Junovi Properties**: оно наиболее прямо решает проблему «найти все страницы, где свойство A равно значению B». Если же критичны интерактивные реестры, pivot-анализ и визуализация, добавьте **StiltSoft Table Filter, Charts & Spreadsheets**. [marketplace.atlassian](https://marketplace.atlassian.com/apps/27447/table-filter-charts-spreadsheets-for-confluence)

