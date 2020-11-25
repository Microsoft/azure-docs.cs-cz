---
title: Rozšířené filtrování – Azure Event Grid IoT Edge | Microsoft Docs
description: Rozšířené filtrování v Event Grid IoT Edge
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001044"
---
# <a name="advanced-filtering"></a>Rozšířené filtrování
Event Grid umožňuje zadat filtry pro libovolnou vlastnost v datové části JSON. Tyto filtry jsou modelovány jako množina `AND` podmínek s každou vnější podmínkou, která má volitelné vnitřní `OR` podmínky. Pro každou `AND` podmínku zadejte následující hodnoty:

* `OperatorType` – Typ porovnání.
* `Key` – Cesta JSON k vlastnosti, na které má být filtr použit.
* `Value` – Referenční hodnota, na kterou se filtr spouští (nebo) `Values` – sada referenčních hodnot, na které se filtr spouští.

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

Všimněte si, že ve výše uvedeném příkladu JSON `AdvancedFilters` je pole. Jednotlivé prvky pole si můžete představit `AdvancedFilter` jako `AND` podmínku.

Pro operátory, které podporují více hodnot (například `NumberIn` , `NumberNotIn` , `StringIn` , atd.), je každá hodnota považována za `OR` podmínku. Takže a `StringBeginsWith("a", "b", "c")` bude odpovídat libovolné řetězcové hodnotě, která začíná buď `a` nebo `b` nebo `c` .

> [!CAUTION]
> Operátory NOT a se `NumberNotIn` `StringNotIn` chovají jako a podmínky na každé hodnotě zadané v `Values` poli.
>
> V takovém případě filtr Accept-All filtr a předá účel filtrování.

## <a name="floating-point-rounding-behavior"></a>Chování zaokrouhlení plovoucí desetinné čárky

Event Grid používá `decimal` typ .NET ke zpracování všech číselných hodnot. Číselné hodnoty zadané v JSON pro odběr událostí nepodléhají chování při zaokrouhlování plovoucí desetinné čárky.

## <a name="case-sensitivity-of-string-filters"></a>Rozlišování velkých a malých písmen u filtrů řetězců

U všech porovnávání řetězců se nerozlišují malá a velká písmena. Neexistuje žádný způsob, jak toto chování změnit ještě dnes.

## <a name="allowed-advanced-filter-keys"></a>Povolené klíče rozšířeného filtru

`Key`Vlastnost může být buď dobře známá vlastnost nejvyšší úrovně, nebo může být cesta JSON s více tečkami, kde každá tečka označuje krokování vnořeného objektu JSON.

Event Grid nemá žádný zvláštní význam pro `$` znak v klíči, na rozdíl od specifikace JSONPath.

### <a name="event-grid-schema"></a>Schéma Event gridu

Pro události ve schématu Event Grid:

* ID
* Téma
* Předmět
* Typ události
* Dataverze
* Data. Prop1
* Data. Prop * Prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>Vlastní schéma událostí

`Key`Ve vlastním schématu událostí neexistuje žádné omezení, protože Event Grid nevynutila žádné schéma obálky v datové části.

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
