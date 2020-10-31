---
title: DateTimeToTicks v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o DateTimeToTicks funkcí SQL systému v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 47bf8a3a2ffe66e295fcb9d8a2a02891812c6813
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095777"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Převede zadaný typ DateTime na takty. Jedna značka představuje 100 nanosekund nebo 1 10 – millionth sekundy. 

## <a name="syntax"></a>Syntaxe
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argumenty
  
*Datum a čas*  
   Hodnota řetězce data a času STANDARDu ISO 8601 ve formátu `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Návratové typy

Vrátí číslo se znaménkem, aktuální počet taktů 100, které uplynuly od epocha systému UNIX. Jinými slovy DateTimeToTicks vrátí počet taktů 100, které uplynuly od 00:00:00 čtvrtka, 1. ledna 1970.

## <a name="remarks"></a>Poznámky

DateTimeDateTimeToTicks vrátí `undefined` , pokud hodnota DateTime není platným časem ISO 8601 DateTime.

Tato systémová funkce nebude index využívat.

## <a name="examples"></a>Příklady

Tady je příklad, který vrátí počet tiků:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Tady je příklad, který vrátí počet tiků bez zadání počtu zlomků sekund:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce data a času](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
