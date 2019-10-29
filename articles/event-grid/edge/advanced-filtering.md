---
title: Rozšířené filtrování – Azure Event Grid IoT Edge | Microsoft Docs
description: Rozšířené filtrování v Event Grid IoT Edge
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992558"
---
# <a name="advanced-filtering"></a>Rozšířené filtrování
Event Grid umožňuje zadat filtry pro libovolnou vlastnost v datové části JSON. Tyto filtry jsou modelovány jako `AND` podmínky a každá vnější podmínka má volitelné vnitřní `OR` podmínky. Pro každou podmínku `AND` zadejte následující hodnoty:

* `OperatorType` – typ porovnání.
* `Key` – cesta JSON k vlastnosti, na které se má filtr použít.
* `Value` – referenční hodnota, na kterou se filtr spouští (nebo) `Values`-sada referenčních hodnot, na které se filtr spouští.

## <a name="json-syntax"></a>Syntaxe JSON

Syntaxe JSON rozšířeného filtru je následující:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Filtrování hodnot polí

Event Grid nepodporuje filtrování u pole hodnot dnes. Pokud má příchozí událost hodnotu pole pro klíč rozšířeného filtru, operace porovnání se nezdařila. Příchozí událost skončila tím, že se neshoduje s odběrem události.

## <a name="and-or-not-semantics"></a>Sémantika AND – NOT

Všimněte si, že ve výše uvedeném příkladu JSON `AdvancedFilters` je pole. Každý prvek `AdvancedFilter` pole si popřemýšlejte jako podmínku `AND`.

Pro operátory, které podporují více hodnot (například `NumberIn`, `NumberNotIn`, `StringIn`atd.), je každá hodnota považována za `OR`ou podmínku. Takže `StringBeginsWith("a", "b", "c")` bude odpovídat jakékoli hodnotě řetězce, která začíná buď `a` nebo `b` nebo `c`.

> [!CAUTION]
> Operátory NOT `NumberNotIn` a `StringNotIn` se chovají jako podmínky v každé hodnotě zadané v poli `Values`.
>
> Pokud to neprovedete, filtr přijme filtr přijmout – vše a předá účel filtrování.

## <a name="floating-point-rounding-behavior"></a>Chování zaokrouhlení plovoucí desetinné čárky

Event Grid používá typ `decimal` .NET ke zpracování všech číselných hodnot. Číselné hodnoty zadané v JSON pro odběr událostí nepodléhají chování při zaokrouhlování plovoucí desetinné čárky.

## <a name="case-sensitivity-of-string-filters"></a>Rozlišování velkých a malých písmen u filtrů řetězců

U všech porovnávání řetězců se nerozlišují malá a velká písmena. Neexistuje žádný způsob, jak toto chování změnit ještě dnes.

## <a name="allowed-advanced-filter-keys"></a>Povolené klíče rozšířeného filtru

Vlastnost `Key` může být dobře známá vlastnost nejvyšší úrovně, nebo může být cesta JSON s více tečkami, kde každá tečka označuje krokování vnořeného objektu JSON.

Event Grid nemá žádný zvláštní význam pro `$` znak v klíči, na rozdíl od specifikace JSONPath.

### <a name="event-grid-schema"></a>Schéma Event gridu

Pro události ve schématu Event Grid:

* ID
* Téma
* Předmět
* Typ
* dataverze
* Data. Prop1
* Data. Prop * Prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>Vlastní schéma událostí

Neexistuje žádné omezení `Key` ve vlastním schématu událostí, protože Event Grid nevynutilo v datové části žádné schéma obálky.

## <a name="numeric-single-value-filter-examples"></a>Příklady číselného filtru s jednou hodnotou

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Příklady filtru číselného rozsahu a hodnot

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Příklady filtru hodnot rozsahu řetězce

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Příklady logických filtrů s jednou hodnotou

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
