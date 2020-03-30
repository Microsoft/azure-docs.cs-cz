---
title: Aliasing v Azure Cosmos DB
description: Zjistěte, jak používat aliasing v dotazech Azure Cosmos DB SQL k rozlišení dvou vlastností se stejným názvem
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873467"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing v Azure Cosmos DB

Hodnoty aliasu můžete explicitně vyslovit v dotazech. Pokud má dotaz dvě vlastnosti se stejným názvem, použijte aliasing přejmenovat jednu nebo obě vlastnosti tak, aby byly v předpokládaném výsledku disambiguated.

## <a name="examples"></a>Příklady

Klíčové slovo AS použité pro aliasování je volitelné, jak je znázorněno v následujícím příkladu při promítání druhé hodnoty jako `NameInfo`:

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

- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule SELECT](sql-query-select.md)
- [FROM klauzule](sql-query-from.md)
