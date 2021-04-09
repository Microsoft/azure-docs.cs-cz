---
title: GetCurrentTicks v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o GetCurrentTicks funkcí SQL systému v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524270"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Vrátí počet taktů 100 – nanosekund, které uplynuly od 00:00:00 čtvrtka, 1. ledna 1970.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Návratové typy

Vrátí číslo se znaménkem, aktuální počet taktů 100, které uplynuly od epocha systému UNIX. Jinými slovy, GetCurrentTicks vrátí počet taktů 100 nanosekund, které uplynuly od 00:00:00 čtvrtka, 1. ledna 1970.

## <a name="remarks"></a>Poznámky

GetCurrentTicks () je nedeterministické funkce. Vrácený výsledek je UTC (koordinovaný světový čas).

> [!NOTE]
> Tato systémová funkce nebude index využívat. Pokud potřebujete porovnat hodnoty s aktuálním časem, Získejte aktuální čas před provedením dotazu a použijte tuto konstantní řetězcovou hodnotu v `WHERE` klauzuli.

## <a name="examples"></a>Příklady

Tady je příklad, který vrátí aktuální čas měřený v taktech:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce data a času](sql-query-date-time-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
