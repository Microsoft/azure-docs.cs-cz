---
title: GetCurrentDateTime v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o GetCurrentDateTime funkcí SQL systému v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 12ce8beab082674cd7672713325d4b3f4322aeae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587300"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Vrátí aktuální čas UTC (koordinovaný světový čas) datum a čas jako řetězec ISO 8601.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Návratové typy
  
Vrátí aktuální hodnotu řetězce standardu UTC data a času 8601 ve formátu, `YYYY-MM-DDThh:mm:ss.fffffffZ` kde:
  
|Formát|Description|
|-|-|
|RRRR|rok se čtyřmi číslicemi|
|MM|dvoumístný měsíc (01 = leden atd.)|
|DD|dvoumístné číslo dne v měsíci (01 až 31)|
|T|označuje, že se mají na začátku prvky času.|
|hh|dvoumístný čas (00 až 23)|
|mm|dvoumístné minuty (00 až 59)|
|ss|dvě číslice sekund (00 až 59)|
|. fffffff|dvoumístný zlomek v sekundách|
|Z|Označení UTC (koordinovaný světový čas)|
  
  Další informace o formátu ISO 8601 naleznete v tématu [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Poznámky

GetCurrentDateTime () je nedeterministické funkce. Vrácený výsledek je UTC. Přesnost je 7 číslic a přesnost 100 nanosekund.

> [!NOTE]
> Tato systémová funkce nebude index využívat. Pokud potřebujete porovnat hodnoty s aktuálním časem, Získejte aktuální čas před provedením dotazu a použijte tuto konstantní řetězcovou hodnotu v `WHERE` klauzuli.

## <a name="examples"></a>Příklady
  
Následující příklad ukazuje, jak získat aktuální datum a čas UTC pomocí předdefinované funkce GetCurrentDateTime ().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Tady je příklad sady výsledků dotazu.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce data a času](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
