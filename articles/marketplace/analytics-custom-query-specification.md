---
title: Specifikace vlastního dotazu
description: Naučte se, jak pomocí vlastních dotazů programově extrahovat data z analytických tabulek pro vaše nabídky na komerčním webu Microsoft Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583751"
---
# <a name="custom-query-specification"></a>Specifikace vlastního dotazu

Partneři můžou pomocí této specifikace dotazu snadno formulovat vlastní dotazy na extrakci dat z analytických tabulek. Dotazy lze použít k výběru pouze požadovaných sloupců a metrik, které odpovídají určitému kritériu. Na srdce od specifikace jazyka je definice datové sady, na které se dá zapisovat vlastní dotaz.

## <a name="datasets"></a>Datové sady

Stejným způsobem, že se některé dotazy spouštějí v databázi, která obsahuje tabulky a sloupce, vlastní dotaz funguje na datových sadách, které mají sloupce a metriky. Úplný seznam dostupných datových sad pro formulaci dotazu lze získat pomocí rozhraní API datových sad.

Tady je příklad datové sady zobrazené jako JSON.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Části datové sady

- Název datové sady je jako název databázové tabulky. Například ISVUsage. Datová sada obsahuje seznam sloupců, které lze vybrat, například MarketplaceSubscriptionId.
- Datová sada obsahuje také metriky, které jsou jako agregační funkce v databázi. Například NormalizedUsage.
- Existují pevná časová rozpětí, nad kterými lze exportovat data.

### <a name="formulating-a-query-on-a-dataset"></a>Formulování dotazu na datovou sadu

Jedná se o některé ukázkové dotazy, které ukazují, jak extrahovat různé typy dat.

| Dotaz | Description |
| ------------ | ------------- |
| **Vybrat** MarketplaceSubscriptionId, CustomerId **z** ISVUsage **TIMESPAN LAST_MONTH** | Tento dotaz zobrazí všechny jedinečné `MarketplaceSubscriptionId` a odpovídající `CustomerId` za poslední 1 měsíc. |
| **Vybrat** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **z** **pořadí ISVUsage podle** EstimatedExtendedChargeCC **limitu** 10 | Tento dotaz získá prvních 10 předplatných v sestupném pořadí podle počtu licencí prodaných v rámci každého předplatného. |
| **Vybrat** CustomerId, NormalizedUsage, RawUsage **z** ISVUSAGE **objednávky podle** NormalizedUsage **, kde** NormalizedUsage > 100000 **ORDER by** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | Tento dotaz poskytne NormalizedUsage a RawUsage všem zákazníkům, kteří mají NormalizedUsage větší než 100 000. |
| **Vybrat** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **z** ISVUsage, **kde** CustomerID ("2a31c234-1f4e-4c60-909e-76d234f93161", "80780748-3f9a-11eb-b378-0242ac130002") | Tento dotaz získá `MarketplaceSubscriptionId` a vygeneruje výnosy za každý měsíc pomocí dvou `CustomerId` hodnot: `2a31c234-1f4e-4c60-909e-76d234f93161` a `80780748-3f9a-11eb-b378-0242ac130002` . |
|||

## <a name="query-specification"></a>Specifikace dotazu

Tato část popisuje definici a strukturu dotazu.

### <a name="grammar-reference"></a>Gramatika – referenční informace

V této tabulce jsou popsány symboly používané v dotazech.

| Symbol | Význam |
| ------------ | ------------- |
| ? | Volitelné |
| * | Nula nebo více |
| + | Jeden nebo více |
| &#124; | Nebo jeden ze seznamu |
| | |

### <a name="query-definition"></a>Definice dotazu

Příkaz dotazu má následující klauzule: SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause? a TimeSpan?.

- **SelectClause**: **Vyberte** ColumOrMetricName (, ColumOrMetricName) *
    - **ColumOrMetricName**: sloupce a metriky definované v rámci datové sady
- **FromClause**: **od** třídy DataSet
    - Název **sady**: datová sada definovaná v rámci datové sady
- **WhereClause**: **WHERE** FilterCondition (**a** FilterCondition) *
    - **FilterCondition**: hodnota operátoru ColumOrMetricName
        - **Operátor**: = | > | < | >= | <= |! = | NAPŘÍKLAD | NELÍBÍ SE VÁM | V | NENÍ V
        - **Hodnota**: Number | StringLiteral | MultiNumberList | MultiStringList 
            - **Číslo**:-? [0-9] + (. [0-9] [0-9] *)?
            - **StringLiteral**: ' [a-za-z0-9_] * '
            - **MultiNumberList**: (číslo (, číslo) *)
            - **MultiStringList**: (StringLiteral (; StringLiteral) *)
- **OrderClause**: **ORDER by** OrderCondition (, OrderCondition) *
    - **OrderCondition**: ColumOrMetricName (**ASC**  |  **DESC**) *
    - **LimitClause**: **limit** [0-9] +
    - **TimeSpan**: **TimeSpan** (Today | VČERA | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | PLATNÉ

### <a name="query-structure"></a>Struktura dotazu

Dotaz sestavy je tvořen více částmi:

- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
- TIMESPAN

Každá část je popsána níže.

#### <a name="select"></a>SELECT

Tato část dotazu určuje sloupce, které budou exportovány. Sloupce, které lze vybrat, jsou pole uvedená v `selectableColumns` částech a v `availableMetrics` rámci datové sady. Konečné exportované řádky budou vždy obsahovat jedinečné hodnoty ve vybraných sloupcích. V exportovaném souboru například nebudou žádné duplicitní řádky. Metriky se vypočtou pro každou jedinečnou kombinaci vybraných sloupců.

**Příklad**:
- **Vyberte** `OfferName` , `NormalizedUsage`

#### <a name="from"></a>FROM

Tato část dotazu určuje datovou sadu, ze které je nutné exportovat data. Název datové sady, který je zde uveden, musí být platným názvem datové sady vráceným rozhraním API datových sad.

**Příklad**:
- Výsledkem `ISVUsage`
- Výsledkem `ISVOrder`

#### <a name="where"></a>WHERE

Tato část dotazu slouží k zadání podmínek filtru pro datovou sadu. V konečném exportovaném souboru budou k dispozici pouze řádky, které odpovídají všem podmínkám uvedeným v této klauzuli. Podmínka filtru může být na všech sloupcích uvedených v `selectableColumns` a `availableMetrics` . Hodnoty zadané v podmínce filtru mohou být seznam čísel nebo seznam řetězců pouze v případě, že je operátor `IN` nebo `NOT IN` . Hodnoty lze vždy předávat jako literální řetězec a budou převedeny na nativní typy sloupců. Operace s více podmínkami filtru musí být odděleny `AND` operací.

**Příklad**:

- MarketplaceSubscriptionId = ' 868368da-957d-4959-8992-3c12dc7e6260 '
- Zákazník, **jako** je% Contosso%
- ID zákazníka **není v** (1000, 1001, 1002).
- OrderQuantity = 100
- OrderQuantity = 100
    - MarketplaceSubscriptionId = ' 7b487ac0-CE12-b732-dcd6-91a1e4e74a50 ' a CustomerId = ' 0f8b7fa0-eb83-a183-1225-ca153ef807aa '

#### <a name="order-by"></a>ORDER BY

Tato část dotazu určuje kritéria řazení pro exportované řádky. Sloupce, ve kterých lze definovat řazení, musí být z `selectableColumns` a `availableMetrics` datové sady. Pokud není zadán směr řazení, bude nastaven na výchozí hodnotu `DESC` sloupce. Řazení lze definovat na více sloupcích oddělením kritérií na čárku.

**Příklad**:

- **Řadit podle** NormalizedUsage **ASC**, ESTIMATEDEXTENDEDCHARGE (CC) **DESC**
- **Řadit podle** Customer **ASC**, NormalizedUsage

#### <a name="limit"></a>LIMIT

Tato část dotazu určuje počet řádků, které budou exportovány. Počet, který zadáte, musí být kladné nenulové celé číslo.

#### <a name="timespan"></a>TIMESPAN

Tato část dotazu určuje dobu, po kterou je nutné exportovat data. Možné hodnoty by měly být z `availableDateRanges` pole v definici datové sady.

### <a name="case-sensitivity-in-query-specification"></a>Rozlišování velkých a malých písmen v dotazu Specification

Specifikace je zcela bez rozlišení velkých a malých písmen. Předdefinovaná klíčová slova, názvy sloupců a hodnoty lze zadat pomocí velkých nebo malých písmen.

## <a name="see-also"></a>Viz také

- [Seznam systémových dotazů](analytics-system-queries.md)
