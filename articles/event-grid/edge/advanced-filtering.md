---
title: Pokročilé filtrování – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Pokročilé filtrování v mřížce událostí na okraji IoT.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992558"
---
# <a name="advanced-filtering"></a>Rozšířené filtrování
Event Grid umožňuje zadat filtry na libovolnou vlastnost v datové části json. Tyto filtry jsou modelovány jako sada `AND` podmínek, `OR` přičemž každá vnější podmínka má volitelné vnitřní podmínky. Pro `AND` každou podmínku zadáte následující hodnoty:

* `OperatorType`- Typ srovnání.
* `Key`- Cesta json k vlastnosti, na kterou chcete použít filtr.
* `Value`- Referenční hodnota, proti které je `Values` filtr spuštěn (nebo) - Sada referenčních hodnot, proti které je filtr spuštěn.

## <a name="json-syntax"></a>Syntaxe JSON

Syntaxe JSON pro rozšířený filtr je následující:

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

## <a name="filtering-on-array-values"></a>Filtrování hodnot pole

Event Grid dnes nepodporuje filtrování na poli hodnot. Pokud příchozí událost má hodnotu pole pro klíč rozšířeného filtru, odpovídající operace se nezdaří. Příchozí událost skončí neodpovídá předplatné události.

## <a name="and-or-not-semantics"></a>Sémantiku a nebo ne

Všimněte si, že v příkladu json uvedené dříve, `AdvancedFilters` je pole. Každý prvek `AdvancedFilter` pole si `AND` můžete zamyslet jako podmínku.

Pro operátory, které podporují `NumberIn`více `NumberNotIn` `StringIn`hodnot (například , , , `OR` atd.), každá hodnota je považována za podmínku. `StringBeginsWith("a", "b", "c")` Takže bude odpovídat jakékoli řetězci `a` hodnotu, která začíná buď nebo nebo `b` `c`.

> [!CAUTION]
> Not operátory `NumberNotIn` `StringNotIn` - a chovat se jako and `Values` podmínky pro každou hodnotu uvedenou v poli.
>
> Pokud tak neučiníte, bude filtr přijmout vše filtr a porazit účel filtrování.

## <a name="floating-point-rounding-behavior"></a>Chování zaokrouhlení s plovoucí desetinnou táhou

Event Grid `decimal` používá typ .NET ke zpracování všech číselných hodnot. Číselné hodnoty zadané v odběru událostí JSON nepodléhají chování zaokrouhlení s plovoucí desetinnou táhou.

## <a name="case-sensitivity-of-string-filters"></a>Rozlišování staňových písmen filtrů řetězců

Všechna porovnání řetězců nerozlišují malá a velká písmena. Neexistuje žádný způsob, jak změnit toto chování dnes.

## <a name="allowed-advanced-filter-keys"></a>Povolené rozšířené klíče filtru

Vlastnost `Key` může být známá vlastnost nejvyšší úrovně nebo json cesta s více tečkami, kde každá tečka znamená krokování do vnořeného objektu json.

Event Grid nemá žádný zvláštní význam `$` pro znak v klíči, na rozdíl od specifikace JSONPath.

### <a name="event-grid-schema"></a>Schéma mřížky událostí

Pro události ve schématu Mřížka událostí:

* ID
* Téma
* Subjekt
* Typ události
* Datová verze
* Data.Prop1
* Data.Prop*Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>Vlastní schéma události

Neexistuje žádné omezení na `Key` in vlastní události schématu, protože Event Grid nevynucuje žádné schéma obálky na datové části.

## <a name="numeric-single-value-filter-examples"></a>Příklady numerických filtrů s jednou hodnotou

* NumberGreaterThan
* NumberGreaterThanOrEquals
* Bezpočet než
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

## <a name="numeric-range-value-filter-examples"></a>Příklady filtru číselných hodnot rozsahu

* Číslov
* NumberNotin

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

## <a name="string-range-value-filter-examples"></a>Příklady filtru rozsahu řetězců

* Obsahuje řetězec
* StringBeginsWith
* Řetězec endswith
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

## <a name="boolean-single-value-filter-examples"></a>Příklady filtrů s logickou hodnotou

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
