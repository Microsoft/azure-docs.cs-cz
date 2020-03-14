---
title: ST_DISTANCE v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o funkcích systému SQL ST_DISTANCE v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 972712d37c146ce288c49af7832919946f5503cd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297114"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Vrátí vzdálenost mezi dvěma LineStringmi body, mnohoúhelníkem, výrazem pro více bodových JSON. Další informace najdete v článku o [datech geoprostorového a geografického umístění JSON](sql-query-geospatial-intro.md) .
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je libovolný platný výraz, objektu GeoJSON bodu mnohoúhelníku či LineString.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu obsahujícího vzdálenost. Vyjadřuje se v metrech pro výchozí odkaz na systém.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak vrátit všechny rodiny dokumentů, které jsou do 30 km od zadaného umístění pomocí předdefinované funkce `ST_DISTANCE`. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Tady je sada výsledků.  
  
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
- [Úvod do Azure Cosmos DB](introduction.md)
