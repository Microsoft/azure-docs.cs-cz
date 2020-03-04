---
title: Klíčová slova SQL pro Azure Cosmos DB
description: Seznamte se s klíčovými slovy SQL pro Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: a9de9435c0e2fb2b67733a995ff412978ea02d89
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250299"
---
# <a name="keywords-in-azure-cosmos-db"></a>Klíčová slova v Azure Cosmos DB
Tento článek podrobně popisuje klíčová slova, která se dají použít v Azure Cosmos DBch dotazech SQL.

## <a name="between"></a>JEDNOTLIVÝCH

Jako v ANSI SQL můžete použít klíčové slovo BETWEEN k vyjádření dotazů na rozsahy řetězcových nebo číselných hodnot. Například následující dotaz vrátí všechny položky, ve kterých je první podřízená hodnota 1-5 (včetně).

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Na rozdíl od v ANSI SQL můžete také použít klauzuli BETWEEN v klauzuli FROM, jak je uvedeno v následujícím příkladu.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

V rozhraní SQL API na rozdíl od ANSI SQL můžete vyjádřit dotazy na rozsah proti vlastnostem smíšených typů. `grade` může být například číslo, jako je `5` v některých položkách, a řetězec, jako je `grade4` v jiných. V těchto případech, jako v jazyce JavaScript, porovnání dvou různých typů má za následek `Undefined`, takže se položka přeskočí.

> [!TIP]
> Pro rychlejší dobu provádění dotazů vytvořte zásadu indexování, která používá typ indexu rozsahu pro všechny číselné vlastnosti nebo cesty, které filtry klauzule BETWEEN.

## <a name="distinct"></a>ZNAK

Klíčové slovo DISTINCT eliminuje duplicity v projekci dotazu.

V tomto příkladu se dotazují hodnoty pro každý poslední název:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Výsledky jsou:

```json
[
    "Andersen"
]
```

Můžete také projektovat jedinečné objekty. V takovém případě pole lastName v jednom ze dvou dokumentů neexistuje, takže dotaz vrátí prázdný objekt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Výsledky jsou:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

KLAUZULE DISTINCT se dá použít taky v projekci v rámci poddotazu:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Tento dotaz projektuje pole, které obsahuje dané jméno dítěte s odebranými duplicitami. Toto pole má aliasy jako ChildNames a prochází z vnějšího dotazu.

Výsledky jsou:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Dotazy s agregovanou systémovou funkcí a poddotaz s příponou DISTINCT nejsou podporovány. Například následující dotaz není podporován:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>PRO

Použijte klíčové slovo IN ke kontrole, zda zadaná hodnota odpovídá jakékoli hodnotě v seznamu. Například následující dotaz vrátí všechny rodinné položky, kde je `id` `WakefieldFamily` nebo `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Následující příklad vrátí všechny položky, kde je stav kterákoli z určených hodnot:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Rozhraní SQL API poskytuje podporu pro [iteraci přes pole JSON](sql-query-object-array.md#Iteration)s novou konstrukcí přidanou prostřednictvím klíčového slova in ve zdroji from. 

## <a name="top"></a>VRCHOL

Klíčové slovo TOP vrátí první `N` počet výsledků dotazu v nedefinovaném pořadí. Jako osvědčený postup použijte klauzuli TOP s klauzulí ORDER BY k omezení výsledků na první `N` počet seřazených hodnot. Kombinování těchto dvou klauzulí je jediným způsobem, jak prediktivním označovat, které řádky mají vliv na nejvyšší vliv.

Můžete použít TOP s konstantní hodnotou, jako v následujícím příkladu, nebo s hodnotou proměnné s použitím parametrizovaných dotazů.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Výsledky jsou:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Starat](sql-query-join.md)
- [Poddotazy](sql-query-subquery.md)