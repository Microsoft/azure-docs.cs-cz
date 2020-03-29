---
title: Parametrizované dotazy v Azure Cosmos DB
description: Zjistěte, jak parametrizované dotazy SQL poskytují robustní zpracování a únik vstupu uživatele a zabraňují náhodnému vystavení dat prostřednictvím vkládání SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870815"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Parametrizované dotazy v Azure Cosmos DB

Cosmos DB podporuje dotazy s parametry vyjádřené známé @ zápisu. Parametrizované SQL poskytuje robustní zpracování a únik uživatelského vstupu a zabraňuje náhodnému vystavení dat prostřednictvím injektáže SQL.

## <a name="examples"></a>Příklady

Můžete například napsat dotaz, `lastName` který `address.state` bere a jako parametry a `lastName` `address.state` spustit jej pro různé hodnoty a na základě vstupu uživatele.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Tento požadavek pak můžete odeslat do cosmos DB jako parametrizovaný dotaz JSON, jako je následující:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Následující příklad nastaví argument TOP s parametrizovaným dotazem: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Hodnoty parametrů mohou být libovolné platné JSON: řetězce, čísla, logické hodnoty, null, sudá pole nebo vnořené JSON. Vzhledem k tomu, že Cosmos DB je bez schématu, parametry nejsou ověřeny proti jakýkoli typ.


## <a name="next-steps"></a>Další kroky

- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelování dat dokumentů](modeling-data.md)
