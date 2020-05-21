---
title: OBSAHUJE v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si, jak funkce CONTAINS SQL System v Azure Cosmos DB vrátí logickou hodnotu, která označuje, zda první řetězcový výraz obsahuje sekundu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0de34e6e0e238887b8f75ae2397e9e650eaac340
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711699"
---
# <a name="contains-azure-cosmos-db"></a>OBSAHUJE (Azure Cosmos DB)

 Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz obsahuje sekundu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr1*  
   Je řetězcový výraz, který má být prohledán.  
  
*str_expr2*  
   Je řetězcový výraz, který se má najít.  

*bool_expr* Volitelná hodnota pro ignorování velkých a malých písmen Pokud je nastaveno na hodnotu true, obsahuje se při hledání bez rozlišování velkých a malých písmen. Je-li tento parametr zadán, je tato hodnota false.
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad zkontroluje, zda "ABC" obsahuje "AB" a "ABC" obsahuje "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2
```  
  
 Zde je sada výsledků.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
