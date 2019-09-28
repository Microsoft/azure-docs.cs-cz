---
title: OBSAHUJE v Azure Cosmos DB dotazovací jazyk
description: Informace o funkci systému SQL obsahuje v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5d5018d0173c316a0a31bd2f70e15e5ff972d153
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351092"
---
# <a name="contains-azure-cosmos-db"></a>OBSAHUJE (Azure Cosmos DB)
 Vrátí hodnotu typu Boolean označující, zda řetězec prvního výrazu obsahuje druhý.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězcový výraz, který má být prohledán.  
  
*str_expr2*  
   Je řetězcový výraz, který se má najít.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad zkontroluje, zda "ABC" obsahuje "AB" a "ABC" obsahuje "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Tady je sada výsledků.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
