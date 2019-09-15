---
title: Práce s poli a objekty v Azure Cosmos DB
description: Seznamte se s syntaxí SQL pole a objektu pro Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 17a0e4ddf5acd267a4cfbb68c218fe9409a91d57
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003923"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Práce s poli a objekty v Azure Cosmos DB

Klíčovou funkcí rozhraní API pro Azure Cosmos DB SQL je vytvoření pole a objektu.

## <a name="arrays"></a>Řadách

Můžete vytvořit pole, jak je znázorněno v následujícím příkladu:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Výsledky jsou:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

[Výraz Array](sql-query-subquery.md#array-expression) můžete také použít k vytvoření pole z výsledků [poddotazu](sql-query-subquery.md) . Tento dotaz získá všechny jednotlivé názvy jednotlivých podřízených objektů v poli.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iterace

Rozhraní SQL API poskytuje podporu pro iteraci přes pole JSON s novou konstrukcí přidanou prostřednictvím [klíčového slova in](sql-query-keywords.md#in) ve zdroji from. V následujícím příkladu:

```sql
    SELECT *
    FROM Families.children
```

Výsledky jsou:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Další dotaz provede iteraci `children` `Families` v kontejneru. Výstupní pole se liší od předchozího dotazu. Tento příklad rozdělí `children`a sloučí výsledky do jednoho pole:  

```sql
    SELECT *
    FROM c IN Families.children
```

Výsledky jsou:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Můžete filtrovat dále pro každou jednotlivou položku pole, jak je znázorněno v následujícím příkladu:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Výsledky jsou:

```json
    [{
      "givenName": "Lisa"
    }]
```

Můžete také agregovat výsledek iterace pole. Například následující dotaz počítá počet podřízených objektů mezi všemi rodinami:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Výsledky jsou:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Starat](sql-query-join.md)