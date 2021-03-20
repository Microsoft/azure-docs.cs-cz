---
title: Vytváření logických funkcí definice uživatelského rozhraní
description: Popisuje funkce pro provádění logických operací.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096950"
---
# <a name="createuidefinition-logical-functions"></a>Logické funkce CreateUiDefinition

Tyto funkce lze použít v podmíněných výrazech. Některé funkce nemusí podporovat všechny datové typy JSON.

## <a name="and"></a>a

Vrátí, `true` zda jsou všechny parametry vyhodnoceny `true` . Tato funkce podporuje dva nebo více parametrů pouze typu Boolean.

Následující příklad vrátí `true` :

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

Následující příklad vrátí `false` :

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>COALESCE

Vrací hodnotu prvního parametru, který není null. Tato funkce podporuje všechny datové typy JSON.

Přepokládejme `element1` a `element2` nejsou definovány. Následující příklad vrátí `"Contoso"` :

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

Tato funkce je užitečná hlavně v kontextu volitelného vyvolání, ke kterému dochází kvůli akci uživatele po načtení stránky. Příkladem je, že omezení umístěná v jednom poli v uživatelském rozhraní závisí na aktuálně vybrané hodnotě jiného, **zpočátku neviditelného** pole. V takovém případě `coalesce()` lze použít k povolení syntakticky platné v době načítání stránky a přitom mít požadovaný efekt při interakci uživatele s polem.

Vezměte v úvahu `DropDown` , který uživateli umožňuje vybrat z několika různých typů databází:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Chcete-li nastavit podmínku pro akci jiného pole na aktuální zvolené hodnotě tohoto pole, použijte `coalesce()` , jak je znázorněno zde:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

To je nezbytné, protože `databaseType` zpočátku není viditelné, a proto nemá hodnotu. To způsobí, že se celý výraz nevyhodnotí správně.

## <a name="equals"></a>equals

Vrátí, `true` zda oba parametry mají stejný typ a hodnotu. Tato funkce podporuje všechny datové typy JSON.

Následující příklad vrátí `true` :

```json
"[equals(0, 0)]"
```

Následující příklad vrátí `true` :

```json
"[equals('web', 'web')]"
```

Následující příklad vrátí `false` :

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

Vrátí `true` , zda je první parametr striktně větší než druhý parametr. Tato funkce podporuje pouze parametry typu Number a String.

Následující příklad vrátí `false` :

```json
"[greater(1, 2)]"
```

Následující příklad vrátí `true` :

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

Vrátí, `true` zda je první parametr větší než nebo roven druhému parametru. Tato funkce podporuje pouze parametry typu Number a String.

Následující příklad vrátí `true` :

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

Vrátí hodnotu na základě toho, zda je podmínka pravdivá, nebo false. První parametr je podmínka, která má být testována. Druhým parametrem je hodnota, která se má vrátit, pokud je podmínka pravdivá. Třetí parametr je hodnota, která se má vrátit, pokud je podmínka nepravdivá.

Následující ukázka vrátí `yes` .

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

Vrátí `true` , zda je první parametr striktně menší než druhý parametr. Tato funkce podporuje pouze parametry typu Number a String.

Následující příklad vrátí `true` :

```json
"[less(1, 2)]"
```

Následující příklad vrátí `false` :

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

Vrátí, `true` zda je první parametr menší nebo roven druhému parametru. Tato funkce podporuje pouze parametry typu Number a String.

Následující příklad vrátí `true` :

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

Vrátí, `true` zda je parametr vyhodnocen jako `false` . Tato funkce podporuje pouze parametry typu Boolean.

Následující příklad vrátí `true` :

```json
"[not(false)]"
```

Následující příklad vrátí `false` :

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>nebo

Vrátí `true` , zda je alespoň jeden z parametrů vyhodnocen jako `true` . Tato funkce podporuje dva nebo více parametrů pouze typu Boolean.

Následující příklad vrátí `true` :

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

Následující příklad vrátí `true` :

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](../management/overview.md).
