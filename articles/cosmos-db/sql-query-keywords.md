---
title: Klíčová slova SQL pro službu Azure Cosmos DB
description: Další informace o klíčových slov SQL pro službu Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342687"
---
# <a name="keywords-in-azure-cosmos-db"></a>Klíčová slova ve službě Azure Cosmos DB
Tento článek podrobně popisuje klíčová slova, které mohou být použity v dotazech SQL služby Azure Cosmos DB.

## <a name="between"></a>MEZI

Jako ANSI SQL můžete použít klíčové slovo BETWEEN vyjádřit dotazy na rozsah řetězec nebo číselné hodnoty. Například následující dotaz vrátí všechny položky, ve kterých je prvním podřízeným objektem na podnikové úrovni 1-5, včetně.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Na rozdíl od v ANSI SQL, můžete také v klauzuli BETWEEN v klauzuli FROM, jako v následujícím příkladu.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

V rozhraní SQL API, na rozdíl od ANSI SQL můžete vyjádřit rozsahu dotazy na vlastnosti smíšené typy. Například `grade` může být počet lajk `5` v některé položky a řetězec jako `grade4` v jiných. V těchto případech, jako v jazyce JavaScript, výsledkem porovnání mezi dvěma různými typy `Undefined`, takže položka se přeskočí.

> [!TIP]
> Pro kratší doby provádění dotazu vytvoření zásady indexování, který používá index typu rozsah proti číselné vlastnosti nebo cesty, které BETWEEN klauzule filtry.

## <a name="distinct"></a>DISTINCT

Klíčové slovo DISTINCT odstraňuje duplicity v projekci dotazu.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

V tomto příkladu dotaz projekty hodnoty pro každý příjmení.

Výsledky jsou:

```json
[
    "Andersen"
]
```

Můžete také promítnout tento počet jedinečných objektů. V tomto případě pole lastName neexistuje v jednom ze dvou dokumentů, takže dotaz vrátí prázdný objekt.

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

DISTINCT je také možné v projekci v poddotazu:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Tento dotaz projekty obsahující jednotlivých podřízených givenName s odebranými pole. Toto pole je alias jako ChildNames a vykreslují ve vnější dotaz.

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
## <a name="in"></a> IN

Pomocí klíčového slova v můžete zkontrolovat, zda zadaná hodnota odpovídá libovolné hodnotě v seznamu. Například následující dotaz vrátí všechny rodiny položek kde `id` je `WakefieldFamily` nebo `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Následující příklad vrátí všechny položky. Pokud stav není žádný ze zadaných hodnot:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Rozhraní SQL API poskytuje podporu pro [iterování přes pole JSON](sql-query-object-array.md#Iteration), s novou konstrukci přidány prostřednictvím klíčového slova v ve zdroji FROM. 

## <a name="top"></a>NAHORU

HORNÍ – klíčové slovo vrátí první `N` počet výsledků dotazu v nedefinované pořadí. Jako osvědčený postup, použijte horní s klauzulí ORDER BY omezit rozsah výsledků na první `N` počet seřazené hodnoty. Kombinace těchto dvou klauzulí je jediný způsob, jak předvídatelně označení, které řádky nejvyšší ovlivňuje.

Horní části můžete použít s konstantní hodnotou, stejně jako v následujícím příkladu nebo s hodnotou proměnné použití parametrizovaných dotazů.

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

## <a name="next-steps"></a>Další postup

- [Začínáme](sql-query-getting-started.md)
- [Spojení](sql-query-join.md)
- [Poddotazy](sql-query-subquery.md)