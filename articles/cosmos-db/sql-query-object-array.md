---
title: Práce s poli a objekty v Azure Cosmos DB
description: Naučte se syntaxi SQL pro vytváření polí a objektů v Azure Cosmos DB. Tento článek také poskytuje některé příklady pro provádění operací s objekty pole.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493783"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Práce s poli a objekty v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Klíčovou funkcí rozhraní API pro Azure Cosmos DB SQL je vytvoření pole a objektu. Tento dokument používá příklady, které lze znovu vytvořit pomocí [datové sady rodiny](sql-query-getting-started.md#upload-sample-data).

Tady je příklad položky v této datové sadě:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

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

## <a name="iteration"></a><a id="Iteration"></a>Iterace

Rozhraní SQL API poskytuje podporu pro iteraci přes pole JSON s [klíčovým slovem in](sql-query-keywords.md#in) ve zdroji from. V následujícím příkladu:

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
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

Výsledky jsou následující:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> Při použití klíčového slova IN pro iteraci nelze filtrovat ani projektovat žádné vlastnosti mimo pole. Místo toho byste měli použít [spojení](sql-query-join.md).

Další příklady najdete v našem [blogovém příspěvku o práci s poli v Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Spojení](sql-query-join.md)
