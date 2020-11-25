---
title: ST_DISTANCE v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o funkcích systému SQL ST_DISTANCE v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 736cf488efbdc095823a1fa603f191442d907de3
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004462"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí vzdálenost mezi dvěma LineStringmi body, mnohoúhelníkem, výrazem pro více bodových JSON. Další informace najdete v článku o [datech geoprostorového a geografického umístění JSON](sql-query-geospatial-intro.md) .
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je libovolný platný výraz pro LineStringal JSON, mnohoúhelník nebo objekt.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz obsahující vzdálenost. To je vyjádřeno v měřičích pro výchozí referenční systém.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak vrátit všechny rodiny dokumentů, které jsou do 30 km v zadaném umístění pomocí `ST_DISTANCE` předdefinované funkce. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Zde je sada výsledků.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat [geoprostorové rejstřík](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB prostorových funkcí](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
