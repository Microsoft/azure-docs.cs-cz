---
title: Vytváření aliasů v Azure Cosmos DB
description: Naučte se používat aliasy v Azure Cosmos DB SQL dotazy k rozlišení dvou vlastností se stejným názvem.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873467"
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

Výsledky jsou následující:

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
