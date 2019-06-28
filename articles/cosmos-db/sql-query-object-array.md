---
title: Práce s poli a objektů ve službě Azure Cosmos DB
description: Další informace o pole a objektu vytváření syntaxi SQL pro službu Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342584"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Práce s poli a objektů ve službě Azure Cosmos DB

Klíčovou funkcí služby SQL API služby Azure Cosmos DB je vytvoření pole a objektu.

## <a name="arrays"></a>Pole

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

Můžete také použít [výraz pole](sql-query-subquery.md#array-expression) k vytvoření pole z [na poddotaz](sql-query-subquery.md) výsledky. Tento dotaz získá všechny různé křestní jména všech dětí v poli.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iterace

Rozhraní SQL API poskytuje podporu pro iterace přes pole JSON, s novou konstrukci přidány prostřednictvím [IN – klíčové slovo](sql-query-keywords.md#in) ve zdroji FROM. V následujícím příkladu:

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

Další dotaz provádí iterace nad `children` v `Families` kontejneru. Pole výstup se liší od předchozího dotazu. V tomto příkladu rozdělí `children`a výsledky se sloučí do jediného pole:  

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

Můžete filtrovat, dále na každou položku pole, jak je znázorněno v následujícím příkladu:

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

Můžete také agregovat přes výsledek iteraci pole. Například následující dotaz vrátí počet podřízených mezi všechny řady:

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

## <a name="next-steps"></a>Další postup

- [Začínáme](sql-query-getting-started.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Spojení](sql-query-join.md)