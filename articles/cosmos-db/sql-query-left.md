---
title: VLEVO v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL LEFT v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303745"
---
# <a name="left-azure-cosmos-db"></a>VLEVO (Azure Cosmos DB)
 Vrátí levou část řetězce se zadaným počtem znaků.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězec výraz extrahovat znaky z.  
  
*num_expr*  
   Je číselný výraz, který určuje počet znaků.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězec výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí levou část "abc" pro různé hodnoty délky.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Zde je sada výsledků.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude mít prospěch z [indexu rozsahu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
