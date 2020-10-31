---
title: DateTimeAdd v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o DateTimeAdd funkcí SQL systému v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: f6177426ce62f3369092403c52344fa764bcf2e6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095845"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Vrátí hodnotu řetězce DateTime, která je výsledkem přidání zadané číselné hodnoty (jako čísla se znaménkem) k určenému řetězci DateTime.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>Argumenty
  
*DateTimePart*  
   Část data, ke které DateTimeAdd přidá celé číslo. Tato tabulka uvádí všechny platné argumenty DateTimePart:

| DateTimePart | zkratky        |
| ------------ | -------------------- |
| Year (Rok)         | "Year", "rrrr", "RR" |
| Month (Měsíc)        | "Month", "mm", "m"   |
| Den          | "Day", "dd", "d"     |
| Hodina         | "hour", "HH"         |
| Minuta       | "Minute", "mi", "n"  |
| Second       | "Second", "SS", "s"  |
| Komponentu  | "milisekund", "MS"  |
| Úrovni mikrosekund  | "mikrosekunda", "MCS" |
| Nanosekund   | "nanosekund", "NS"   |

*numeric_expr*  
   Je celočíselná hodnota se znaménkem, která bude přidána do DateTimePart zadaného typu DateTime.

*Datum a čas*  
   Hodnota řetězce data a času STANDARDu ISO 8601 ve formátu, `YYYY-MM-DDThh:mm:ss.fffffffZ` kde:
  
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

## <a name="return-types"></a>Návratové typy

Vrátí hodnotu řetězce data a času STANDARDu ISO 8601 ve formátu, `YYYY-MM-DDThh:mm:ss.fffffffZ` kde:
  
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

## <a name="remarks"></a>Poznámky

DateTimeAdd se vrátí `undefined` z následujících důvodů:

- Zadaná hodnota DateTimePart není platná.
- Zadaný numeric_expr není platné celé číslo.
- Hodnota DateTime v argumentu nebo výsledku není platným datovým typem DateTime ISO 8601.

## <a name="examples"></a>Příklady
  
Následující příklad přidá 1 měsíc k hodnotě DateTime: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

Následující příklad odečte 2 hodiny od data a času: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce data a času](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
