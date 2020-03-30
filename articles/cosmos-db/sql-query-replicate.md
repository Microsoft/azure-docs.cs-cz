---
title: REPLIKOvat v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL REPLICATE v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302181"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKACE (Azure Cosmos DB)
 Opakuje hodnotu řetězce zadaný počet opakování.
  
## <a name="syntax"></a>Syntaxe
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězec výraz.
  
*num_expr*  
   Je číselný výraz. Pokud *je num_expr* záporná nebo neomezená, výsledek není definován.
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězec výraz.
  
## <a name="remarks"></a>Poznámky
  Maximální délka výsledku je 10 000 znaků, tj <*num_expr**str_expr.*

## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, `REPLICATE` jak se používá v dotazu.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Zde je sada výsledků.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
