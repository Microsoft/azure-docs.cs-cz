---
title: ST_ISVALID v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o ST_ISVALID funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349357"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Vrátí logickou hodnotu označující, zda je zadaný výraz GeoJSON bodu mnohoúhelníku či LineString platný.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Je bodový bod JSON, mnohoúhelník nebo LineString výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak zkontrolovat, zda je platný pomocí ST_VALID bod.  
  
  Například tento bod má hodnotu šířky, který není v platném rozsahu hodnot [-90, 90], takže dotaz vrátí hodnotu false.  
  
  Mnohoúhelníky vyžaduje specifikaci GeoJSON poslední souřadnic pár poskytuje by měl být stejný jako první, chcete-li vytvořit zavřeného tvaru. Body v rámci mnohoúhelníku musí zadat v pořadí proti směru hodinových ručiček. Mnohoúhelník zadat v pořadí po směru hodinových ručiček představuje inverzní oblasti v rámci něj.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Tady je sada výsledků.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB prostorových funkcí](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
