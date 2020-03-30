---
title: Klíčová slova SQL pro Azure Cosmos DB
description: Další informace o klíčových slovech SQL pro Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: f2da2695ec20eac9dd2636104d3314427e60d541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498538"
---
# <a name="keywords-in-azure-cosmos-db"></a>Klíčová slova v Azure Cosmos DB

Tento článek podrobnosti klíčová slova, která mohou být použity v Azure Cosmos DB SQL dotazy.

## <a name="between"></a>BETWEEN

`BETWEEN` Klíčové slovo můžete použít k vyjádření dotazů proti rozsahům řetězcových nebo číselných hodnot. Například následující dotaz vrátí všechny položky, ve kterých je první dítě stupeň 1-5, včetně.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Můžete také použít `BETWEEN` klíčové `SELECT` slovo v klauzuli, jako v následujícím příkladu.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

V rozhraní SQL API, na rozdíl od ANSI SQL, můžete vyjádřit dotazy rozsahu proti vlastnostem smíšených typů. Může být `grade` například číslo `5` jako u některých `grade4` položek a řetězec jako v jiných. V těchto případech, stejně jako v Jazyce JavaScript, porovnání mezi dvěma různými typy má za následek `Undefined`, takže položka je přeskočena.

> [!TIP]
> Pro rychlejší časy spuštění dotazu vytvořte zásadu indexování, která používá typ `BETWEEN` indexu rozsahu proti všem číselným vlastnostem nebo cestám, které klauzule filtruje.

## <a name="distinct"></a>DISTINCT

Klíčové `DISTINCT` slovo eliminuje duplikáty v projekci dotazu.

V tomto příkladu dotaz předpokládá hodnoty pro každé příjmení:

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

Můžete také promítat jedinečné objekty. V tomto případě pole lastName v jednom ze dvou dokumentů neexistuje, takže dotaz vrátí prázdný objekt.

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

DISTINCT lze také použít v projekci v rámci poddotazu:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Tento dotaz projekty pole, které obsahuje každé dítě givenName s duplikáty odebrány. Toto pole je aliasováno jako ChildNames a promítáno ve vnějším dotazu.

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

Dotazy s agregační systémovou funkcí `DISTINCT` a poddotazem s nejsou podporovány. Například následující dotaz není podporován:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Pomocí klíčového slova IN zkontrolujte, zda zadaná hodnota odpovídá libovolné hodnotě v seznamu. Například následující dotaz vrátí všechny položky `WakefieldFamily` `AndersenFamily`rodiny, `id` kde je nebo .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Následující příklad vrátí všechny položky, kde je stav některé ze zadaných hodnot:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Rozhraní SQL API poskytuje podporu pro [iterace přes pole JSON](sql-query-object-array.md#Iteration), s novou konstrukci přidánprostřednictvím v klíčové slovo ve zdroji FROM.

Pokud do filtru zahrnete klíč oddílu, `IN` dotaz se automaticky přefiltruje pouze na příslušné oddíly.

## <a name="top"></a>TOP

Klíčové slovo TOP `N` vrátí první počet výsledků dotazu v nedefinovaném pořadí. Jako osvědčený postup použijte TOP `ORDER BY` s klauzulí k `N` omezení výsledků na první počet objednaných hodnot. Kombinace těchto dvou klauzulí je jediný způsob, jak předvídatelně určit, které řádky TOP ovlivňuje.

Top můžete použít s konstantní hodnotou, jako v následujícím příkladu, nebo s hodnotou proměnné pomocí parametrizovaných dotazů.

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
- [Poddotazů](sql-query-subquery.md)