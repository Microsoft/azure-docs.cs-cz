---
title: ST_ISVALID v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o funkcích systému SQL ST_ISVALID v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1af008889ece71bc2158e0a74c30ef17d24207f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080052"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
