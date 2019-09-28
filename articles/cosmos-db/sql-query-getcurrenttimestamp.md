---
title: GetCurrentTimestamp v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o GetCurrentTimestamp funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351010"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Vrátí počet milisekund, které uplynuly od 00:00:00 ve čtvrtek, 1. ledna 1970. 
  
## <a name="syntax"></a>Syntaxe
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselnou hodnotu, aktuální počet milisekund, které uplynuly od epocha systému UNIX, tj. počet milisekund, které uplynuly od 00:00:00. ledna 1970.

## <a name="remarks"></a>Poznámky

  GetCurrentTimestamp () je nedeterministické funkce.
  
  Vrácený výsledek je UTC (koordinovaný světový čas).

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
- [Úvod do Azure Cosmos DB](introduction.md)
