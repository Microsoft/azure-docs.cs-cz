---
title: LTRIM v dotazovacím jazyce Azure Cosmos DB
description: Informace o funkci systému LTRIM SQL v Azure Cosmos DB vrátit řetězcový výraz po odebrání úvodních prázdných míst
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 08c069de70684a8562e86963ddb2e84ee889e7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302249"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
 Vrátí řetězcový výraz po odebrání úvodních prázdných míst.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězec výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězec výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, `LTRIM` jak používat uvnitř dotazu.  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Zde je sada výsledků.  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
