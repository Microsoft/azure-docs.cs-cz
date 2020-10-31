---
title: DateTimeFromParts v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o DateTimeFromParts funkcí SQL systému v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a897c5d08abd71fcda380363e3f26304bc69f876
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095805"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Vrací řetězcovou hodnotu DateTime vytvořenou ze vstupních hodnot.
  
## <a name="syntax"></a>Syntaxe
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Argumenty
  
*numberYear* Celočíselná hodnota pro rok ve formátu `YYYY`

*numberMonth*  
   Celočíselná hodnota pro měsíc ve formátu `MM`

*numberDay*  
   Celočíselná hodnota pro den ve formátu `DD`

*numberHour* (volitelné) celočíselná hodnota pro hodinu ve formátu `hh`

*numberMinute* (volitelné) celočíselná hodnota pro minutu ve formátu `mm`

*numberSecond* (volitelné) celočíselná hodnota pro sekundu ve formátu `ss`

*numberOfFractionsOfSecond* (volitelné) celočíselná hodnota pro zlomek sekundy ve formátu `.fffffff`

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
  
 Další informace o formátu ISO 8601 naleznete v tématu [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Poznámky

Pokud by zadaná celá čísla vytvořila neplatné datum a čas, DateTimeFromParts se vrátí `undefined` .

Pokud není zadán nepovinný argument, jeho hodnota bude 0.

## <a name="examples"></a>Příklady

Tady je příklad, který obsahuje pouze povinné argumenty pro vytvoření hodnoty DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Tady je další příklad, který používá k vytvoření hodnoty DateTime také některé volitelné argumenty:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Tady je další příklad, který používá k vytvoření hodnoty DateTime také všechny volitelné argumenty:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce data a času](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
