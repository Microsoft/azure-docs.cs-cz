---
title: StringEquals v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si, jak funkce StringEquals SQL System v Azure Cosmos DB vrátí logickou hodnotu, která označuje, jestli první řetězcový výraz odpovídá druhému.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a34ea2e2421433777ff1b9dd44d2ffc5599017bc
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079780"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz odpovídá druhému.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je prvním řetězcovým výrazem, který se má porovnat.  
  
*str_expr2*  
   Je druhým řetězcovým výrazem, který se má porovnat.  

*bool_expr* Volitelná hodnota pro ignorování velkých a malých písmen Když se nastaví na true, StringEquals provede vyhledávání bez rozlišování velkých a malých písmen. Je-li tento parametr zadán, je tato hodnota false.
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad zkontroluje, zda "ABC" odpovídá "ABC" a pokud "ABC" odpovídá "ABC".  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 Zde je sada výsledků.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
