---
title: Práce s poli a objekty v Azure Cosmos DB
description: Naučte se syntaxi SQL pro vytváření polí a objektů v Azure Cosmos DB. Tento článek také poskytuje některé příklady pro provádění operací s objekty pole.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 357653e89e2581dba7726e41f8a08304c619d2fb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077185"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Práce s poli a objekty v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Klíčovou funkcí rozhraní API pro Azure Cosmos DB SQL je vytvoření pole a objektu.

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

[Výraz Array](sql-query-subquery.md#array-expression) můžete také použít k vytvoření pole z výsledků [poddotazu](sql-query-subquery.md) . Tento dotaz získá všechny jednotlivé názvy jednotlivých podřízených objektů v poli.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>Interakc

Rozhraní SQL API poskytuje podporu pro iteraci přes pole JSON s novou konstrukcí přidanou prostřednictvím [klíčového slova in](sql-query-keywords.md#in) ve zdroji from. V následujícím příkladu:

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

Další dotaz provede iteraci `children` v `Families` kontejneru. Výstupní pole se liší od předchozího dotazu. Tento příklad rozdělí `children` a sloučí výsledky do jednoho pole:  

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

Můžete filtrovat dále pro každou jednotlivou položku pole, jak je znázorněno v následujícím příkladu:

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

Můžete také agregovat výsledek iterace pole. Například následující dotaz počítá počet podřízených objektů mezi všemi rodinami:

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
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Spojení](sql-query-join.md)
