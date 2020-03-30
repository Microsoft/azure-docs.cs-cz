---
title: Práce s poli a objekty v Azure Cosmos DB
description: Naučte se syntaxi SQL k vytvoření polí a objektů v Azure Cosmos DB. Tento článek také obsahuje některé příklady pro provádění operací s objekty pole
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246549"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Práce s poli a objekty v Azure Cosmos DB

Klíčovou funkcí rozhraní SQL API Azure Cosmos DB je vytváření polí a objektů.

## <a name="arrays"></a>Pole

Můžete vytvořit pole, jak je znázorněno v následujícím příkladu:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Výsledky jsou následující:

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

Výraz [ARRAY](sql-query-subquery.md#array-expression) můžete také použít k vytvoření pole z výsledků [poddotazu.](sql-query-subquery.md) Tento dotaz získá všechny odlišné křestní názvy podřízených v poli.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>Iterace

Rozhraní SQL API poskytuje podporu pro iterace přes pole JSON, s novou konstrukci přidánprostřednictvím [klíčového slova IN](sql-query-keywords.md#in) ve zdroji FROM. V následujícím příkladu:

```sql
    SELECT *
    FROM Families.children
```

Výsledky jsou následující:

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

Další dotaz provede `children` iteraci v kontejneru. `Families` Výstupní pole se liší od předchozího dotazu. Tento příklad `children`rozdělí a sloučí výsledky do jednoho pole:  

```sql
    SELECT *
    FROM c IN Families.children
```

Výsledky jsou následující:

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

Můžete filtrovat dále na každé jednotlivé položky pole, jak je znázorněno v následujícím příkladu:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Výsledky jsou následující:

```json
    [{
      "givenName": "Lisa"
    }]
```

Můžete také agregovat přes výsledek iterace pole. Například následující dotaz počítá počet dětí ze všech rodin:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Výsledky jsou následující:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Spojení](sql-query-join.md)