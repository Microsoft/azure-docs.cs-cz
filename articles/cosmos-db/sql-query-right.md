---
title: Přímo v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL přímo v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302147"
---
# <a name="right-azure-cosmos-db"></a>VPRAVO (Azure Cosmos DB)
 Vrátí pravou část řetězce se zadaným počtem znaků.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězec výraz extrahovat znaky z.  
  
*num_expr*  
   Je číselný výraz, který určuje počet znaků.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězec výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí pravou část "abc" pro různé hodnoty délky.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Zde je sada výsledků.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
