---
title: Interakce se sestavami pomocí rozhraní API pro JavaScript | Dokumentace Microsoftu
description: Rozhraní API pro JavaScript Power BI umožňuje snadné vkládání sestav Power BI do vašich aplikací.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: bdd885d3-1b00-4dcf-bdff-531eb1f97bfb
ms.service: power-bi-embedded
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: bfc80318eb43992e292159a0cd99a9b2f4a3c041
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233098"
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interakce se sestavami Power BI pomocí rozhraní API pro JavaScript

Rozhraní API pro JavaScript Power BI umožňuje snadné vkládání sestav Power BI do vašich aplikací. Prostřednictvím tohoto rozhraní API mohou vaše aplikace programově interagovat s různými elementy sestav, jako jsou stránky a filtry. Díky této interaktivitě se sestavy Power BI stanou integrálnější součástí vaší aplikace.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Ke vložení sestavy Power BI do své aplikace použijete element iframe, který je hostovaný jako součást aplikace. Element iframe slouží jako hranice mezi vaší aplikací a sestavou, jak je vidět na následujícím obrázku:

![Element iframe s kolekcí Pracovních prostorů Power BI bez použití rozhraní API pro JavaScript](media/interact-with-reports/iframe-without-javacript.png)

Element iframe sice proces vkládání dost usnadňuje, ale bez rozhraní API pro JavaScript API spolu sestava a aplikace nemohou interagovat. Tento nedostatek interakce potom může mít za následek pocit, že sestava vlastně není součástí příslušné aplikace. Sestava a aplikace skutečně potřebují vzájemně komunikovat, jak ukazuje následující obrázek:

![Element iframe s kolekcí Pracovních prostorů Power BI s použitím rozhraní API pro JavaScript](media/interact-with-reports/iframe-with-javascript.png)

Rozhraní API pro JavaScript Power BI umožňuje psát kód, který bezpečně prochází hranicí elementu iframe. Díky tomu aplikace může programově provádět v sestavě různé akce a naslouchat událostem z akcí, které uživatelé v této sestavě provedou.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>K čemu je možné využít rozhraní API pro JavaScript Power BI?

Pomocí rozhraní API pro JavaScript API můžete spravovat sestavy, přejít na stránky v sestavě, filtrovat sestavu a zpracovávat události vkládání. Strukturu rozhraní API ukazuje následující diagram.

![Diagram rozhraní API pro JavaScript Power BI](media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>Správa sestav
Rozhraní API pro Javascript umožňuje spravovat chování na úrovni sestav a stránek:

* Zabezpečené vložení konkrétní sestavy Power BI do vaší aplikace – vyzkoušejte si [ukázkovou aplikaci s vložením](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * Nastavení přístupového tokenu
* Konfigurace sestavy
  * Povolení a zákaz podokna filtru a podokna navigace stránkami – vyzkoušejte si [ukázkovou aplikaci s aktualizací nastavení](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * Nastavení výchozích hodnot pro stránky a filtry – vyzkoušejte si [ukázku nastavení výchozích hodnot](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* Nastavení a ukončení režimu zobrazení na celé obrazovce

[Další informace o vkládání sestav](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>Přechod na stránky v sestavě
Rozhraní API pro JavaScript umožňuje prohlédnout všechny stránky sestavy a nastavit aktuální stránku. Vyzkoušejte si [ukázkovou aplikaci s navigací](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Další informace o navigaci stránkami](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrování sestavy
Rozhraní API pro JavaScript poskytuje základní a rozšířené možnosti filtrování pro vložené sestavy a stránky sestav. Vyzkoušejte si [ukázkovou aplikaci filtrování](http://azure-samples.github.io/powerbi-angular-client/#/scenario4) a prohlédněte si základní kód.

#### <a name="basic-filters"></a>Základní filtry
Základní filtr je postavený na úrovni hierarchie nebo sloupce a obsahuje seznam hodnot, které se mají zahrnout nebo vyloučit.

```typescript
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>Rozšířené filtry
Rozšířené filtry využívají logické operátory AND a OR a přijímají jednu nebo dvě podmínky. Každá z těchto podmínek má vlastní operátor a hodnotu. Podporované podmínky:

* Žádný
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contains
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```typescript
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[Další informace o filtrování](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>Zpracování událostí

Kromě odesílání informací do elementu iframe může aplikace také přijímat informace o následujících událostech, jejichž původcem je iframe:

* Embed
  * loaded
  * error
* Reports
  * pageChanged
  * dataSelected (už brzy)

[Další informace o zpracování událostí](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>Další postup

Další informace o rozhraní API pro JavaScript Power BI najdete pod následujícími odkazy:

* [Wikiweb rozhraní API pro JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Referenční informace k objektovému modelu](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* [Živá ukázka](https://microsoft.github.io/PowerBI-JavaScript/demo/)