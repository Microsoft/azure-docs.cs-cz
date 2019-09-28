---
title: ST_INTERSECTS v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o ST_INTERSECTS funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 549c6b69e9112a491060478e859338c14e977612
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349380"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
 Vrací výraz Boolean určující, zda objekt GeoJSON (bodu, mnohoúhelník nebo LineString) zadané v prvním argumentu protíná GeoJSON (bodu, mnohoúhelník nebo LineString) v druhý argument.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je bodový výraz, mnohoúhelník nebo LineString objektu pro injson.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logickou hodnotu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak najít všechny oblasti, které průnik s danou mnohoúhelníku.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Tady je sada výsledků.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB prostorových funkcí](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
