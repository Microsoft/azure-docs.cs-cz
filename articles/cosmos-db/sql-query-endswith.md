---
title: ENDSWITH v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci ENDSWITH SQL System v Azure Cosmos DB a vrátí logickou hodnotu, která označuje, jestli první řetězcový výraz končí druhým.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d7e7f3e33389d4a201ec3281829cb9f0415978e6
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713552"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)

 Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz končí druhým.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr1*  
   Je řetězcový výraz.  
  
*str_expr2*  
   Je řetězcový výraz, který má být porovnán s koncem *str_expr1*.

*bool_expr* Volitelná hodnota pro ignorování velkých a malých písmen Když je nastaveno na hodnotu true, ENDSWITH provede vyhledávání bez rozlišování velkých a malých písmen. Je-li tento parametr zadán, je tato hodnota false.
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí "ABC" končí "b" a "BC".  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Zde je sada výsledků.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
