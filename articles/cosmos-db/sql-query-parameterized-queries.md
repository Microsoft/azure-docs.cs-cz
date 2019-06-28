---
title: Parametrizované dotazy ve službě Azure Cosmos DB
description: Další informace o dotazech SQL s parametry
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342902"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Parametrizované dotazy ve službě Azure Cosmos DB

Cosmos DB podporuje dotazy s parametry vyjádřena pomocí známé @ zápis. Parametrizované dotazy SQL poskytuje robustní zpracování a uvození vstupu uživatele a zabraňuje náhodnou expozici dat prostřednictvím útoku prostřednictvím injektáže SQL.

## <a name="examples"></a>Příklady

Můžete například napsat dotaz, který přebírá `lastName` a `address.state` jako parametry a proveďte jej pro různé hodnoty `lastName` a `address.state` na základě uživatelského zadání.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Můžete pak odeslat tuto žádost do služby Cosmos DB jako parametrický dotaz JSON podobný tomuto:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Následující příklad nastaví argument TOP s parametrizovaného dotazu: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Hodnoty parametru může být libovolný platný kód JSON: řetězce, čísla, logické hodnoty null, dokonce i pole nebo vnořené JSON. Cosmos DB je bez schématu, parametry nejsou ověřovat na libovolného typu.


## <a name="next-steps"></a>Další postup

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelování dat dokumentů](modeling-data.md)
