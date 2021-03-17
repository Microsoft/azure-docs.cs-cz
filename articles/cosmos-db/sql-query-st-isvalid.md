---
title: ST_ISVALID v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o funkcích systému SQL ST_ISVALID v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e7c124da91a947a0ac8426ce8c92347396236c4
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004428"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrací logickou hodnotu, která označuje, zda je platný zadaný bodový bod JSON, mnohoúhelník, výraz pro více mnohoúhelníků nebo LineString.  
  
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
