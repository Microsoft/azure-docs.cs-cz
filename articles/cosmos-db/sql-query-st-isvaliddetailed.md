---
title: ST_ISVALIDDETAILED v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o funkcích systému SQL ST_ISVALIDDETAILED v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: df2d4265393085a58699b3576ce2461ed63de317
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080035"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
