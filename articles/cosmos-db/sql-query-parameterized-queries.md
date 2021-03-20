---
title: Parametrizované dotazy v Azure Cosmos DB
description: Přečtěte si, jak parametrizované dotazy SQL poskytují robustní zpracování a uvozovací znaky vstupu uživatele a zabraňují nechtěnému úniku dat prostřednictvím injektáže SQL.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: dc32aab89e50b500001fd2267f62e3031154be62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96549153"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Parametrizované dotazy v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB podporuje dotazy s parametry vyjádřenými známým znakem @ Notation. Parametrizované SQL poskytují robustní zpracování a uvozovací znaky vstupu uživatele a zabraňují nechtěnému úniku dat prostřednictvím injektáže SQL.

## <a name="examples"></a>Příklady

Můžete například napsat dotaz, který přebírá `lastName` a `address.state` jako parametry a provede ho pro různé hodnoty `lastName` a `address.state` na základě vstupu uživatele.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Tuto žádost pak můžete odeslat Azure Cosmos DB jako parametrizovaný dotaz JSON, jako je například následující:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Následující příklad nastaví horní argument s parametrizovaným dotazem:

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Hodnoty parametrů můžou být jakékoli platné JSON: řetězce, čísla, logické hodnoty, hodnoty null, sudé pole nebo vnořené JSON. Vzhledem k tomu, že Azure Cosmos DB je bez schématu, parametry nejsou ověřeny proti žádnému typu.

Tady jsou příklady pro parametrizované dotazy v každé sadě Azure Cosmos DB SDK:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>Další kroky

- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelování dat dokumentů](modeling-data.md)
