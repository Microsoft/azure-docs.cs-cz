---
title: GetCurrentTimestamp v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o GetCurrentTimestamp funkcí SQL systému v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: fa7d1ec2af12065fb7d761073cd982a561cf53c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524257"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí počet milisekund, které uplynuly od 00:00:00 ve čtvrtek, 1. ledna 1970.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Návratové typy
  
Vrací číselnou hodnotu se znaménkem, aktuální počet milisekund, které uplynuly od epocha systému UNIX, tj. počet milisekund, které uplynuly od 00:00:00. ledna 1970.

## <a name="remarks"></a>Poznámky

GetCurrentTimestamp () je nedeterministické funkce. Vrácený výsledek je UTC (koordinovaný světový čas).

> [!NOTE]
> Tato systémová funkce nebude index využívat. Pokud potřebujete porovnat hodnoty s aktuálním časem, Získejte aktuální čas před provedením dotazu a použijte tuto konstantní řetězcovou hodnotu v `WHERE` klauzuli.

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
- [Úvod do služby Azure Cosmos DB](introduction.md)
