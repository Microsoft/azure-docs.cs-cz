---
title: ST_WITHIN v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o funkcích systému SQL ST_WITHIN v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9372da349d0ea9169bb59570b7e6dd0e597d1cdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558227"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí logický výraz, který označuje, zda je objekt LineString (Point, mnohoúhelník,) zadaný v prvním argumentu v rámci objektu typu Boolean (Point, mnohoúhelník, promnohoúhelník nebo LineString) v druhém argumentu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je bodový výraz, mnohoúhelník nebo LineString objektu pro injson.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrací logickou hodnotu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak najít všechny rodiny dokumentů v mnohoúhelníku pomocí `ST_WITHIN` .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Zde je sada výsledků.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [geoprostorového indexu](index-policy.md#spatial-indexes) s výjimkou dotazů s agregacemi.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB prostorových funkcí](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
