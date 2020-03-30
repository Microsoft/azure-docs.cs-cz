---
title: ST_DISTANCE v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o ST_DISTANCE funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537291"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Vrátí vzdálenost mezi dvěma výrazy GeoJSON Point, Polygon, MultiPolygon nebo LineString. Další informace naleznete v článku [Geospatial a GeoJSON o lokalizačních datech.](sql-query-geospatial-intro.md)
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je platný výraz objektu GeoJSON Point, Polygon nebo LineString.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz obsahující vzdálenost. To je vyjádřeno v metrech pro výchozí referenční systém.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak vrátit všechny rodinné dokumenty, které jsou `ST_DISTANCE` do 30 km od zadaného umístění pomocí vestavěné funkce. .  
  
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

Tato systémová funkce bude mít prospěch z [geoprostorového indexu](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Další kroky

- [Prostorové funkce Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
