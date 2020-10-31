---
title: Parametrizované dotazy v Azure Cosmos DB
description: Přečtěte si, jak parametrizované dotazy SQL poskytují robustní zpracování a uvozovací znaky vstupu uživatele a zabraňují nechtěnému úniku dat prostřednictvím injektáže SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: a0f354a0420521ede45740a4eb9c492089e36bda
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089215"
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
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>Další kroky

- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelování dat dokumentů](modeling-data.md)
