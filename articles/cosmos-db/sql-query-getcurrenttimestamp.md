---
title: GetCurrentTimestamp v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o GetCurrentTimestamp funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1e0a8d69edab0c01005268ee49c23625236f03a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606916"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)

 Vrátí počet milisekund, které uplynuly od 00:00:00 ve čtvrtek, 1. ledna 1970.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Návratové typy
  
Vrací číselnou hodnotu se znaménkem, aktuální počet milisekund, které uplynuly od epocha systému UNIX, tj. počet milisekund, které uplynuly od 00:00:00. ledna 1970.

## <a name="remarks"></a>Poznámky

GetCurrentTimestamp () je nedeterministické funkce. Vrácený výsledek je UTC (koordinovaný světový čas).

Tato systémová funkce nebude index využívat.

## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak získat aktuální časové razítko pomocí předdefinované funkce GetCurrentTimestamp ().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Tady je příklad sady výsledků dotazu.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce data a času](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
