---
title: ST_ISVALIDDETAILED v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o ST_ISVALIDDETAILED funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349352"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 Vrátí hodnotu JSON obsahující logická hodnota, pokud zadaný výraz GeoJSON bodu mnohoúhelníku či LineString je platný a pokud není platný, kromě důvod jako hodnotu řetězce.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je výraz typu "bodový kód JSON" nebo mnohoúhelník.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu JSON obsahující logická hodnota, pokud zadaný výraz bodu nebo mnohoúhelník GeoJSON je platný a pokud není platný, kromě důvod jako hodnotu řetězce.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad, jak ověřit platnost (s podrobnostmi) pomocí `ST_ISVALIDDETAILED`.  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Tady je sada výsledků.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB prostorových funkcí](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
