---
title: StylesObject pro dynamickou Azure Maps
description: Referenční příručka ke schématu a syntaxi JSON pro StylesObject, která se používá při vytváření v dynamickém Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8eb4e49e6c0e3f011015d40b8eca036d5218674c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891695"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Referenční příručka schématu StylesObject pro dynamická mapování

Tento článek představuje referenční příručku ke schématu a syntaxi JSON pro `StylesObject` . `StylesObject`Je `StyleObject` pole reprezentující styly stateset. Pomocí [služby stavu funkce](/rest/api/maps/featurestate) Azure Maps Creator můžete použít styly stateset pro funkce dat mapy v interiéru. Jakmile vytvoříte styly stateset a přidružíte je k funkcím vnitřních map, můžete je použít k vytvoření dynamické mapy vnitřních oken. Další informace o vytváření dynamických map vnitřníchy najdete v tématu [Implementace dynamického stylu pro mapy vnitřních vnitřních](indoor-map-dynamic-styling.md)přípon.

## <a name="styleobject"></a>StyleObject

A `StyleObject` je buď jako [`BooleanTypeStyleRule`](#booleantypestylerule) nebo [`NumericTypeStyleRule`](#numerictypestylerule) .

Následující kód JSON znázorňuje `BooleanTypeStyleRule` pojmenované `occupied` a `NumericTypeStyleRule` pojmenované `temperature` .

```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 A `NumericTypeStyleRule` je  [`StyleObject`](#styleobject) a skládá se z následujících vlastností:

| Vlastnost | Typ | Popis | Povinné |
|-----------|----------|-------------|-------------|
| `keyName` | řetězec | Název *stavu* nebo dynamické vlastnosti. `keyName`Element by měl být v `StyleObject` poli jedinečný.| Ano |
| `type` | řetězec | Hodnota je "číselná hodnota". | Ano |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Pole číselných stylů a rozsahů s přidruženými barvami. Každý rozsah definuje barvu, která se má použít, když hodnota *stavu* splní rozsah.| Ano |

### <a name="numberruleobject"></a>NumberRuleObject

`NumberRuleObject`Sestává z [`RangeObject`](#rangeobject) `color` vlastností a. Pokud hodnota *stavu* spadá do rozsahu, jeho barva pro zobrazení bude barva zadaná ve `color` Vlastnosti.

Pokud definujete více překrývajících se rozsahů, vybraná barva bude barva, která je definována v prvním rozsahu, který je splněn.

V následujícím příkladu JSON budou oba rozsahy platit, pokud je hodnota *stavu* mezi 50-60. Barva, která se bude používat, ale je `#343deb` v seznamu první oblast, která byla splněná.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Vlastnost | Typ | Popis | Povinné |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject) definuje sadu logických podmínek rozsahu, které v případě `true` změny barvy zobrazení *stavu* na barvu určenou `color` vlastností. Pokud `range` parametr není zadán, bude vždy použita barva definovaná ve `color` Vlastnosti.   | Ne |
| `color` | řetězec | Barva, která se má použít, pokud hodnota stavu spadá do rozsahu. `color`Vlastnost je řetězec formátu JSON v jednom z následujících formátů: <ul><li> Šestnáctkové hodnoty ve stylu HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100; 50%; 50%; 1)")</li><li> Předdefinované názvy barev HTML, jako je žlutá a modrá.</li></ul> | Ano |

### <a name="rangeobject"></a>RangeObject

`RangeObject`Definuje hodnotu číselného rozsahu [`NumberRuleObject`](#numberruleobject) . Aby hodnota *stavu* mohla být do rozsahu, všechny definované podmínky musí držet hodnotu true. 

| Vlastnost | Typ | Popis | Povinné |
|-----------|----------|-------------|-------------|
| `minimum` | double | Celé číslo x, které x ≥ `minimum` .| Ne |
| `maximum` | double | Vše číslo x, které x ≤ `maximum` . | Ne |
| `exclusiveMinimum` | double | Číslo x > `exclusiveMinimum` .| Ne |
| `exclusiveMaximum` | double | Číslo x < `exclusiveMaximum` .| Ne |

### <a name="example-of-numerictypestylerule"></a>Příklad NumericTypeStyleRule

Následující kód JSON ukazuje `NumericTypeStyleRule` *stav* s názvem `temperature` . V tomto příkladu [`NumberRuleObject`](#numberruleobject) obsahuje dva definované rozsahy teplot a jejich přidružené styly barev. Pokud je rozsah teploty 50-69, displej by měl používat barvu `#343deb` .  Pokud je rozsah teploty 31-70, displej by měl používat barvu `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

A `BooleanTypeStyleRule` je [`StyleObject`](#styleobject) a skládá se z následujících vlastností:

| Vlastnost | Typ | Popis | Povinné |
|-----------|----------|-------------|-------------|
| `keyName` | řetězec |  Název *stavu* nebo dynamické vlastnosti.  `keyName`Element by měl být jedinečný uvnitř Style Array.| Ano |
| `type` | řetězec |Hodnota je "Boolean". | Ano |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)první| Logický pár s barvami pro `true` a `false` hodnoty *stavu* .| Ano |

### <a name="booleanruleobject"></a>BooleanRuleObject

`BooleanRuleObject`Definuje barvy pro `true` hodnoty a `false` .

| Vlastnost | Typ | Popis | Povinné |
|-----------|----------|-------------|-------------|
| `true` | řetězec | Barva, která se má použít, pokud je hodnota *stavu* `true` . `color`Vlastnost je řetězec formátu JSON v jednom z následujících formátů: <ul><li> Šestnáctkové hodnoty ve stylu HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100; 50%; 50%; 1)")</li><li> Předdefinované názvy barev HTML, jako je žlutá a modrá.</li></ul>| Ano |
| `false` | řetězec | Barva, která se má použít, pokud je hodnota *stavu* `false` . | Ano |

### <a name="example-of-booleantypestylerule"></a>Příklad BooleanTypeStyleRule

Následující kód JSON ukazuje `BooleanTypeStyleRule` *stav* s názvem `occupied` . [`BooleanRuleObject`](#booleanruleobject)Definuje barvy pro `true` hodnoty a `false` .

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```