---
title: Parametrizované dotazy v Azure Cosmos DB
description: Přečtěte si, jak parametrizované dotazy SQL poskytují robustní zpracování a uvozovací znaky vstupu uživatele a zabraňují nechtěnému úniku dat prostřednictvím injektáže SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870815"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Parametrizované dotazy v Azure Cosmos DB

Cosmos DB podporuje dotazy s parametry vyjádřenými známým znakem @ Notation. Parametrizované SQL poskytují robustní zpracování a uvozovací znaky vstupu uživatele a zabraňují nechtěnému úniku dat prostřednictvím injektáže SQL.

## <a name="examples"></a>Příklady

Můžete například napsat dotaz, který přebírá `lastName` a `address.state` jako parametry a provede ho pro různé hodnoty `lastName` a `address.state` na základě vstupu uživatele.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Tuto žádost pak můžete odeslat Cosmos DB jako parametrizovaný dotaz JSON, jako je například následující:

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

Hodnoty parametrů můžou být jakékoli platné JSON: řetězce, čísla, logické hodnoty, hodnoty null, sudé pole nebo vnořené JSON. Vzhledem k tomu, že Cosmos DB je bez schématu, parametry nejsou ověřeny proti žádnému typu.


## <a name="next-steps"></a>Další kroky

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Data modelu dokumentu](modeling-data.md)
