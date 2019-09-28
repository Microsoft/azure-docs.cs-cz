---
title: ENDSWITH v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci SQL System ENDSWITH v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c988d63e597c77bc09a1d21ad391909bb55901
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351053"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce končí na druhý.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězcový výraz.  
  
*str_expr2*  
   Je řetězcový výraz, který má být porovnán s koncem *str_expr1*.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí "abc" končí "b" a "bc".  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Tady je sada výsledků.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
