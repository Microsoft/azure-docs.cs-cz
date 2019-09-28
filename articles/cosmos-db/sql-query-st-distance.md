---
title: ST_DISTANCE v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o ST_DISTANCE funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1c55bac14b3379f29d57bbad36026749089ec0fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349394"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Vrací vzdálenost mezi dvěma GeoJSON bodu mnohoúhelníku či LineString výrazy.  
  
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

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB prostorových funkcí](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
