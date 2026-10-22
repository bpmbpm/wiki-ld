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
