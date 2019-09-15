---
title: Vytváření aliasů v Azure Cosmos DB
description: Další informace o hodnotách aliasů v Azure Cosmos DBch dotazech SQL
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002073"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Vytváření aliasů v Azure Cosmos DB

V dotazech můžete explicitně aliasovat hodnoty. Pokud má dotaz dvě vlastnosti se stejným názvem, použijte aliasing pro přejmenování jedné nebo obou vlastností tak, aby byly v očekávaném výsledku nejednoznačné.

## <a name="examples"></a>Příklady

Klíčové slovo AS použité pro aliasing je volitelné, jak je znázorněno v následujícím příkladu při projekci druhé hodnoty jako `NameInfo`:

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

## <a name="next-steps"></a>Další kroky

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule SELECT](sql-query-select.md)
- [FROM – klauzule](sql-query-from.md)
