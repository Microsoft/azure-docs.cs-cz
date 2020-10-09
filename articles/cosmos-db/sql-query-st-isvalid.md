---
title: ST_ISVALID v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o funkcích systému SQL ST_ISVALID v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "71349357"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Vrací logickou hodnotu označující, zda je zadaný bodový bod JSON, mnohoúhelník nebo výraz LineString platný.  
  
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
  
  Následující příklad ukazuje, jak zjistit, zda je bod platný pomocí ST_VALID.  
  
  Například tento bod má hodnotu Zeměpisná šířka, která není v platném rozsahu hodnot [-90, 90], takže dotaz vrátí hodnotu false.  
  
  Pro mnohoúhelníky vyžaduje specifikace injson, aby byl poslední poskytnutý pár souřadnic stejný jako první, aby se vytvořil uzavřený obrazec. Body v mnohoúhelníku musí být zadány v pořadí proti směru hodinových ručiček. Mnohoúhelník zadaný v pořadí zleva doprava představuje invertující oblast v oblasti.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Zde je sada výsledků.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB prostorových funkcí](sql-query-spatial-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
