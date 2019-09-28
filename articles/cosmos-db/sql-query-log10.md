---
title: Log10 – v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o log10 – funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 56f1e96e7d4ee1b5f38ee7392665e17819ae554b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349727"
---
# <a name="log10-azure-cosmos-db"></a>Log10 – (Azure Cosmos DB)
 Vrátí logaritmus o základu base 10 zadaný číselný výraz.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expression*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="remarks"></a>Poznámky
  
  LOG10 a výkonu funkce jsou nepřímo sebou mají nějaký vztah. Například 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad deklaruje proměnnou a vrátí LOG10 hodnotu zadanou proměnnou (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Tady je sada výsledků.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
