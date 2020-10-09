---
title: ST_ISVALIDDETAILED v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o funkcích systému SQL ST_ISVALIDDETAILED v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "71349352"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 Vrátí hodnotu JSON obsahující logickou hodnotu, pokud je platný LineString výraz pro typ bodového zápisu, mnohoúhelník nebo výraz JSON, a pokud je neplatný, a navíc důvod jako řetězcovou hodnotu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je výraz typu "bodový kód JSON" nebo mnohoúhelník.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu JSON obsahující logickou hodnotu, pokud je zadaný bodový nebo Mnohoúhelníkový výraz platný a je-li neplatný, a navíc důvod jako řetězcovou hodnotu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad, jak ověřit platnost (s podrobnostmi) pomocí `ST_ISVALIDDETAILED` .  
  
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

- [Azure Cosmos DB prostorových funkcí](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
