---
title: DateTimeDiff v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o DateTimeDiff funkcí SQL systému v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 09b801c48bf2998a2d8926009cae76287c1ac9b6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342262"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Vrátí počet (jako celočíselnou hodnotu se znaménkem) zadaného hranice DateTimePart mezi zadanými *StartDate* a *EndDate*.
  
## <a name="syntax"></a>Syntaxe
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
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

*StartDate*  
    Hodnota řetězce data a času STANDARDu ISO 8601 ve formátu, `YYYY-MM-DDThh:mm:ss.fffffffZ` kde:
  
  |Formát|Popis|
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

*EndDate*  
   Hodnota řetězce data a času STANDARDu ISO 8601 ve formátu `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Návratové typy

Vrací celočíselnou hodnotu se znaménkem.

## <a name="remarks"></a>Poznámky

DateTimeDiff se vrátí `undefined` z následujících důvodů:

- Zadaná hodnota DateTimePart není platná.
- StartDate nebo EndDate není platným datovým časem ISO 8601.

DateTimeDiff vždycky vrátí celočíselnou hodnotu se znaménkem a jedná se o měření počtu DateTimePart hranic, které se neměří v časovém intervalu.

## <a name="examples"></a>Příklady
  
Následující příklad vypočítá počet hranic dní mezi `2020-01-01T01:02:03.1234527Z` a `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

Následující příklad vypočítá počet hranic roku přesahujících mezi `2028-01-01T01:02:03.1234527Z` a `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

Následující příklad vypočítá počet hodinových hranic přesahujících mezi `2020-01-01T01:00:00.1234527Z` a `2020-01-01T01:59:59.1234567Z` . I když tyto hodnoty DateTime mají více než 0,99 hodin, `DateTimeDiff` vrátí hodnotu 0, protože nebyly překročeny žádné hranice hodin.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce data a času](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
