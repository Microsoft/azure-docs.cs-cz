---
title: TRIM v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o funkci SYSTÉMU SQL TRIM v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 073efbd91e637a17693617b16c7787d61e150ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304238"
---
# <a name="trim-azure-cosmos-db"></a>TRIM (Azure Cosmos DB)
 Vrátí řetězcový výraz po odebrání úvodních a koncových mezer.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
TRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězec výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězec výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, `TRIM` jak používat uvnitř dotazu.  
  
```sql
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Zde je sada výsledků.  
  
```json
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
