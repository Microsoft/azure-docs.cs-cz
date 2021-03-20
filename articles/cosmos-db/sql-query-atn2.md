---
title: ATN2 v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o tom, jak funkce ATN2 SQL System v Azure Cosmos DB vrací hlavní hodnotu tečny oblouku y/x vyjádřené v radiánech.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6db42713ec6f0eac64e0f1123825c21a4206fa99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341939"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrací hlavní hodnotu tečny oblouku y/x vyjádřenou v radiánech.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočítá ATN2 pro zadané komponenty x a y.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Zde je sada výsledků.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
