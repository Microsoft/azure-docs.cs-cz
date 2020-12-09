---
title: Referenční příručka schématu StylesObject pro dynamickou Azure Maps
description: Referenční příručka k dynamickému Azure Maps schématu a syntaxi StylesObject
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 08379e66c97d34eea53410190475e90e156a58e2
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903339"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Referenční příručka schématu StylesObject pro dynamická mapování

> [!IMPORTANT]
> Služby Azure Maps Creator jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 `StylesObject`Je `StyleObject` pole reprezentující styly stateset. Pomocí [služby stavu funkce](/rest/api/maps/featurestate) Azure Maps Creator (Preview) můžete použít styly stateset pro funkce dat mapy v interiéru. Jakmile vytvoříte styly stateset a přidružíte je k funkcím vnitřních map, můžete je použít k vytvoření dynamické mapy vnitřních oken. Další informace o vytváření dynamických map vnitřníchy najdete v tématu [Implementace dynamického stylu pro mapy vnitřních vnitřních](indoor-map-dynamic-styling.md)přípon.

## <a name="styleobject"></a>StyleObject

A `StyleObject` je jedno z následujících pravidel stylu:

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

Následující kód JSON ukazuje příklady použití každého ze tří typů stylů.  `BooleanTypeStyleRule`Slouží k určení dynamického stylu pro funkce, jejichž `occupied` vlastnost je true a false.  `NumericTypeStyleRule`Slouží k určení stylu pro funkce, jejichž `temperature` vlastnost spadá do určitého rozsahu. Nakonec se `StringTypeStyleRule` používá ke spárování specifických stylů s `meetingType` .



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
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
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
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Pole číselných stylů a rozsahů s přidruženými barvami. Každý rozsah definuje barvu, která se použije, když hodnota *stavu* splní rozsah.| Ano |

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
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject) definuje sadu logických podmínek rozsahu, které v případě `true` změny barvy zobrazení *stavu* na barvu určenou `color` vlastností. Pokud `range` parametr není zadán, bude vždy použita barva definovaná ve `color` Vlastnosti.   | No |
| `color` | řetězec | Barva, která se má použít, pokud hodnota stavu spadá do rozsahu. `color`Vlastnost je řetězec formátu JSON v jednom z následujících formátů: <ul><li> Šestnáctkové hodnoty ve stylu HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100; 50%; 50%; 1)")</li><li> Předdefinované názvy barev HTML, jako je žlutá a modrá.</li></ul> | Ano |

### <a name="rangeobject"></a>RangeObject

`RangeObject`Definuje hodnotu číselného rozsahu [`NumberRuleObject`](#numberruleobject) . Aby hodnota *stavu* mohla být do rozsahu, všechny definované podmínky musí držet hodnotu true.

| Vlastnost | Typ | Popis | Povinné |
|-----------|----------|-------------|-------------|
| `minimum` | double | Celé číslo x, které x ≥ `minimum` .| No |
| `maximum` | double | Vše číslo x, které x ≤ `maximum` . | No |
| `exclusiveMinimum` | double | Číslo x > `exclusiveMinimum` .| No |
| `exclusiveMaximum` | double | Číslo x < `exclusiveMaximum` .| No |

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

## <a name="stringtypestylerule"></a>StringTypeStyleRule

A `StringTypeStyleRule` je [`StyleObject`](#styleobject) a skládá se z následujících vlastností:

| Vlastnost | Typ | Popis | Povinné |
|-----------|----------|-------------|-------------|
| `keyName` | řetězec |  Název *stavu* nebo dynamické vlastnosti.  `keyName`Element by měl být v `StyleObject` poli jedinečný.| Ano |
| `type` | řetězec |Hodnota je "String". | Ano |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| Pole N počtu hodnot *stavu* .| Ano |

### <a name="stringruleobject"></a>StringRuleObject

A `StringRuleObject` se skládá z až N počtu hodnot stavu, které jsou možné řetězcové hodnoty vlastnosti funkce. Pokud hodnota vlastnosti funkce neodpovídá žádné z definovaných hodnot stavu, nebude mít tato funkce dynamický styl. Pokud jsou zadány duplicitní hodnoty stavu, má první z nich přednost.

Při porovnávání řetězcové hodnoty se rozlišují malá a velká písmena.

| Vlastnost | Typ | Popis | Povinné |
|-----------|----------|-------------|-------------|
| `stateValue1` | řetězec | Barva, pokud je řetězec hodnoty stateValue1. | No |
| `stateValue2` | řetězec | Barva, pokud je řetězec hodnoty stateValue. | No |
| `stateValueN` | řetězec | Barva, pokud je řetězec hodnoty stateValueN. | No |

### <a name="example-of-stringtypestylerule"></a>Příklad StringTypeStyleRule

Následující kód JSON ukazuje `StringTypeStyleRule` , který definuje styly přidružené k určitým typům schůzky.

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

A `BooleanTypeStyleRule` je [`StyleObject`](#styleobject) a skládá se z následujících vlastností:

| Vlastnost | Typ | Popis | Povinné |
|-----------|----------|-------------|-------------|
| `keyName` | řetězec |  Název *stavu* nebo dynamické vlastnosti.  `keyName`Element by měl být v `StyleObject` poli jedinečný.| Ano |
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