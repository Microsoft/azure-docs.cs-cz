---
title: GetCurrentDateTime v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL GetCurrentDateTime v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303898"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Vrátí aktuální datum a čas uTC (koordinovaný světový čas) jako řetězec ISO 8601.
  
## <a name="syntax"></a>Syntaxe
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí aktuální hodnotu řetězce UTC data a času ISO 8601 ve formátu, `YYYY-MM-DDThh:mm:ss.fffffffZ` kde:
  
  |||
  |-|-|
  |RRRR|čtyřmístný rok|
  |MM|dvoumístný měsíc (01 = leden atd.)|
  |DD|dvoumístný den v měsíci (01 až 31)|
  |T|signifier pro začátek časových prvků|
  |hh|dvoumístná hodina (00 až 23)|
  |mm|dvoumístné minuty (00 až 59)|
  |ss|dvoumístné sekundy (00 až 59)|
  |.fffffff|sedmimístné zlomkové sekundy|
  |Z|Označení UTC (koordinovaný světový čas)||
  
  Další informace o formátu ISO 8601 naleznete v [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Poznámky

  GetCurrentDateTime() je nedeterministická funkce. 
  
  Vrácený výsledek je UTC.

  Přesnost je 7 číslic s přesností 100 nanosekund.

## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak získat aktuální čas data UTC pomocí vestavěné funkce GetCurrentDateTime().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Zde je příklad sady výsledků.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Další kroky

- [Funkce data a času Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
