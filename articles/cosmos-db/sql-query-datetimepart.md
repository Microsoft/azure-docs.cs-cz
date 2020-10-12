---
title: DateTimePart v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o DateTimePart funkcí SQL systému v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 87663c18ddaa5da6740a0f54aa5f2812cbb06af8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227185"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)

Vrací hodnotu zadaného DateTimePart mezi zadaným datem a časem.
  
## <a name="syntax"></a>Syntaxe
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>Argumenty
  
*DateTimePart*  
   Část data, pro kterou bude DateTimePart vracet hodnotu. Tato tabulka uvádí všechny platné argumenty DateTimePart:

| DateTimePart | zkratky        |
| ------------ | -------------------- |
| Year         | "Year", "rrrr", "RR" |
| Month        | "Month", "mm", "m"   |
| Den          | "Day", "dd", "d"     |
| Hodina         | "hour", "HH"         |
| Minuta       | "Minute", "mi", "n"  |
| Second       | "Second", "SS", "s"  |
| Komponentu  | "milisekund", "MS"  |
| Úrovni mikrosekund  | "mikrosekunda", "MCS" |
| Nanosekund   | "nanosekund", "NS"   |

*Datum a čas*  
   Hodnota řetězce data a času STANDARDu ISO 8601 ve formátu `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Návratové typy

Vrátí kladnou celočíselnou hodnotu.

## <a name="remarks"></a>Poznámky

DateTimePart se vrátí `undefined` z následujících důvodů:

- Zadaná hodnota DateTimePart není platná.
- DateTime není platným časem ISO 8601 DateTime.

Tato systémová funkce nebude index využívat.

## <a name="examples"></a>Příklady

Tady je příklad, který vrací celočíselnou hodnotu v měsíci:

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

Tady je příklad, který vrátí počet mikrosekund:

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce data a času](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
