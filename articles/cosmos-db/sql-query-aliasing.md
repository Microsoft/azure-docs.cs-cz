---
title: Aliasy ve službě Azure Cosmos DB
description: Další informace o hodnotách aliasing v dotazech SQL služby Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342826"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasy ve službě Azure Cosmos DB

Můžete explicitně alias hodnoty v dotazech. Pokud dotaz má dvě vlastnosti se stejným názvem, přejmenujte jednu nebo obě vlastnosti tak, aby se jednoznačně rozlišit ve výsledku předpokládané pomocí aliasů.

## <a name="examples"></a>Příklady

As – klíčové slovo pro aliasy je volitelné, jak je znázorněno v následujícím příkladu, při projekci druhá hodnota jako `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Další postup

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Klauzule SELECT](sql-query-select.md)
- [FROM – klauzule](sql-query-from.md)
