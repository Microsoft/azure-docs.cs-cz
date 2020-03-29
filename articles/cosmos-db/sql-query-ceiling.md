---
title: STROP v dotazovacím jazyce Azure Cosmos DB
description: Zjistěte, jak funkce systému CEILING SQL v Azure Cosmos DB vrátí nejmenší celou hodnotu čísla větší než nebo rovno zadanému číselnému výrazu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 81f113aa51a7f739b506ec7e3eb5bf2cb9f49a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302640"
---
# <a name="ceiling-azure-cosmos-db"></a>STROP (Azure Cosmos DB)
 Vrátí nejmenší hodnotu celého čísla větší nebo rovnou zadanému číselnému výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje kladné číselné, záporné a nulové hodnoty s `CEILING` funkcí.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Zde je sada výsledků.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude mít prospěch z [indexu rozsahu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
