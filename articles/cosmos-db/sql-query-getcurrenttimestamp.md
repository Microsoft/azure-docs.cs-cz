---
title: GetCurrentTimestamp v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL GetCurrentTimestamp v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351010"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Vrátí počet milisekund, které uplynuly od 00:00:00 čtvrtek 1 leden 1970. 
  
## <a name="syntax"></a>Syntaxe
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselnou hodnotu, aktuální počet milisekund, které uplynuly od epochy unixu, tj.

## <a name="remarks"></a>Poznámky

  GetCurrentTimestamp() je nedeterministická funkce.
  
  Vrácený výsledek je UTC (Koordinovaný světový čas).

## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak získat aktuální časové razítko pomocí getcurrentTimestamp() vestavěné funkce.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Zde je příklad sady výsledků.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Další kroky

- [Funkce data a času Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
