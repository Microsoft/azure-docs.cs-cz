---
title: Klíčová slova SQL pro Azure Cosmos DB
description: Seznamte se s klíčovými slovy SQL pro Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 35232f95bc18432db05775807d95f23ceab66aea
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333762"
---
# <a name="keywords-in-azure-cosmos-db"></a>Klíčová slova v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek podrobně popisuje klíčová slova, která se dají použít v Azure Cosmos DBch dotazech SQL.

## <a name="between"></a>BETWEEN

Klíčové slovo lze použít `BETWEEN` pro vyjádření dotazů na rozsahy řetězcových nebo číselných hodnot. Například následující dotaz vrátí všechny položky, ve kterých je první podřízená hodnota 1-5 (včetně).

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Klíčové slovo lze použít také `BETWEEN` v `SELECT` klauzuli, jak je uvedeno v následujícím příkladu.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

V rozhraní SQL API na rozdíl od ANSI SQL můžete vyjádřit dotazy na rozsah proti vlastnostem smíšených typů. `grade`Může se například jednat o číslo, například `5` v některých položkách, a v řetězci podobném `grade4` jiným. V těchto případech, jako v jazyce JavaScript, porovnávání dvou různých typů má za následek `Undefined` , že se položka přeskočí.

## <a name="distinct"></a>DISTINCT

`DISTINCT`Klíčové slovo eliminuje duplicity v projekci dotazu.

V tomto příkladu se dotazují hodnoty pro každý poslední název:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Výsledky jsou následující:

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

Výsledky jsou následující:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` lze také použít v projekci v rámci poddotazu:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Tento dotaz projektuje pole, které obsahuje dané jméno dítěte s odebranými duplicitami. Toto pole má aliasy jako ChildNames a prochází z vnějšího dotazu.

Výsledky jsou následující:

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

Dotazy s agregovanou systémovou funkcí a poddotazem s `DISTINCT` nejsou podporovány. Například následující dotaz není podporován:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Použijte klíčové slovo IN ke kontrole, zda zadaná hodnota odpovídá jakékoli hodnotě v seznamu. Například následující dotaz vrátí všechny rodinné položky, kde `id` je `WakefieldFamily` nebo `AndersenFamily` .

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

Pokud zahrnete klíč oddílu do `IN` filtru, dotaz se automaticky vyfiltruje jenom na relevantní oddíly.

## <a name="top"></a>TOP

Klíčové slovo TOP vrátí první `N` počet výsledků dotazu v nedefinovaném pořadí. Jako osvědčený postup použijte klauzuli TOP s `ORDER BY` klauzulí k omezení výsledků na první `N` počet seřazených hodnot. Kombinování těchto dvou klauzulí je jediným způsobem, jak prediktivním označovat, které řádky mají vliv na nejvyšší vliv.

Můžete použít TOP s konstantní hodnotou, jako v následujícím příkladu, nebo s hodnotou proměnné s použitím parametrizovaných dotazů.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Výsledky jsou následující:

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
- [Spojení](sql-query-join.md)
- [Poddotazy](sql-query-subquery.md)