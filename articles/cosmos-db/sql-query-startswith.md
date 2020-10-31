---
title: StartsWith v jazyce Azure Cosmos DB Query Language
description: Další informace o funkci SQL System Function STARTSWITH v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1ef1a815469e7fb2dd10311b92a28b828772be7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079899"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz začíná druhým.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězcový výraz.
  
*str_expr2*  
   Je řetězcový výraz, který má být porovnán s počátkem *str_expr1* .

*bool_expr* Volitelná hodnota pro ignorování velkých a malých písmen Při nastavení na hodnotu true provede STARTSWITH hledání bez rozlišování velkých a malých písmen. Je-li tento parametr zadán, je tato hodnota false.

## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
Následující příklad zkontroluje, zda řetězec "ABC" začíná písmenem "b" a "A".  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 Zde je sada výsledků.  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
