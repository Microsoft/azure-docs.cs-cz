---
title: ST_ISVALID v dotazovacím jazyce Azure Cosmos DB
description: Další informace o ST_ISVALID funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349357"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Vrátí logickou hodnotu označující, zda je zadaný výraz GeoJSON Point, Polygon nebo LineString platný.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je výraz Bodu GeoJSON, polygon nebo linestring.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak zkontrolovat, zda je bod platný pomocí ST_VALID.  
  
  Například tento bod má hodnotu šířky, která není v platném rozsahu hodnot [-90, 90], takže dotaz vrátí false.  
  
  U vícenohých polygonů specifikace GeoJSON vyžaduje, aby poslední zadaný pár souřadnic byl stejný jako první, aby se vytvořil uzavřený tvar. Body v polygonu musí být zadány v pořadí proti směru hodinových ručiček. Polygon zadaný v pořadí ve směru hodinových ručiček představuje inverzní oblast v něm.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Zde je sada výsledků.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Další kroky

- [Prostorové funkce Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
