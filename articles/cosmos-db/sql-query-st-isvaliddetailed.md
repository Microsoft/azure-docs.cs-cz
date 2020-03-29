---
title: ST_ISVALIDDETAILED v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o ST_ISVALIDDETAILED funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349352"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 Vrátí hodnotu JSON obsahující logickou hodnotu, pokud je zadaný výraz GeoJSON Point, Polygon nebo LineString platný, a pokud je neplatný, navíc důvod jako řetězcová hodnota.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je bod GeoJSON nebo polygon výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu JSON obsahující logickou hodnotu, pokud je zadaný bod GeoJSON nebo vícenohý výraz platný a pokud je neplatný, navíc důvod jako řetězcová hodnota.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad, jak zkontrolovat platnost (s `ST_ISVALIDDETAILED`podrobnostmi) pomocí .  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Zde je sada výsledků.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Další kroky

- [Prostorové funkce Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
