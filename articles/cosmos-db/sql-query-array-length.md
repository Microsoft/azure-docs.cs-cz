---
title: ARRAY_LENGTH v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o ARRAY_LENGTH funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b27b3f09212047e2e8937a4bf649fa3335e15cb2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348670"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 Vrátí počet prvků výrazu určeném poli.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Je výraz pole.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak získat délku pole pomocí `ARRAY_LENGTH`.  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Tady je sada výsledků.  
  
```json
[{"len": 3}]  
```  
  

## <a name="next-steps"></a>Další kroky

- [Funkce pole Azure Cosmos DB](sql-query-array-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
