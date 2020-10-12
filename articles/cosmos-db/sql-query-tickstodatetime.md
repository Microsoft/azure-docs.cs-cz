---
title: TicksToDateTime v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o TicksToDateTime funkcí SQL systému v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 89a8dba97725049b86fc6b38c09e0dd125bb48d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88608735"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)

Převede zadanou hodnotu osové hodnoty na DateTime.
  
## <a name="syntax"></a>Syntaxe
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>Argumenty

*Ticks*  

Podepsaná číselná hodnota: aktuální počet taktů 100 nanosekund, které uplynuly od epocha systému UNIX. Jinými slovy je to počet taktů 100 nanosekund, které uplynuly od 00:00:00 čtvrtka, 1. ledna 1970.

## <a name="return-types"></a>Návratové typy

Vrátí hodnotu řetězce data UTC a Time ISO 8601 ve formátu, `YYYY-MM-DDThh:mm:ss.fffffffZ` kde:
  
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
  |Z|Označení UTC (koordinovaný světový čas)||
  
  Další informace o formátu ISO 8601 naleznete v tématu [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Poznámky

TicksToDateTime se vrátí `undefined` , pokud je zadaná hodnota taktu neplatná.

## <a name="examples"></a>Příklady
  
Následující příklad převede osové značky na typ DateTime:

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce data a času](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
