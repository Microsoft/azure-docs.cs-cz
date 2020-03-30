---
title: ST_WITHIN v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o ST_WITHIN funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 07a339d82f5e4bea1ea0412a5d5b19522611b54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296112"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 Vrátí logický výraz označující, zda je objekt GeoJSON (point, polygon nebo linestring) zadaný v prvním argumentu v rámci pole GeoJSON (Point, Polygon nebo LineString) v druhém argumentu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je výraz objektu GeoJSON Point, Polygon nebo LineString.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logickou hodnotu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak najít všechny rodinné dokumenty `ST_WITHIN`v rámci polygonu pomocí .  
  
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

Tato systémová funkce bude mít prospěch z [geoprostorového indexu](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Další kroky

- [Prostorové funkce Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
