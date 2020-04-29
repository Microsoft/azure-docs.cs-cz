---
title: ARRAY_LENGTH v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si, jak funkce systému SQL délka pole v Azure Cosmos DB vrací počet prvků zadaného výrazu Array.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3596ce4bc702d5e54225d8c90db2f9563feab670
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303983"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 Vrátí počet prvků určeného výrazu pole.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Je výraz pole.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad, jak získat délku pole pomocí `ARRAY_LENGTH`.  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Zde je sada výsledků.  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Funkce pole Azure Cosmos DB](sql-query-array-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
