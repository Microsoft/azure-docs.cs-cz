---
title: SUBSTRING v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL SUBSTRING v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303694"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 Vrátí část řetězcového výrazu začínající na zadané pozici znaku s nulovým základem a pokračuje do zadané délky nebo na konec řetězce.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězec výraz.
  
*num_expr1*  
   Je číselný výraz označujít počáteční znak. Hodnota 0 je první znak *str_expr*.
  
*num_expr2*  
   Je číselný výraz označujících maximální počet znaků *str_expr,* které mají být vráceny. Hodnota 0 nebo méně má za následek prázdný řetězec.

## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězec výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí podřetězec "abc" začínající na 1 a pro délku 1 znak.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Zde je sada výsledků.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude mít prospěch z `0` [indexu rozsahu,](index-policy.md#includeexclude-strategy) pokud je počáteční pozice .

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
