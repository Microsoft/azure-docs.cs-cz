---
title: Podřetězec v jazyce Azure Cosmos DB Query
description: Přečtěte si o podřetězcůch funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303694"
---
# <a name="substring-azure-cosmos-db"></a>Podřetězec (Azure Cosmos DB)
 Vrátí část řetězcového výrazu počínaje zadaným znakem na základě nuly a pokračuje určenou délkou nebo na konci řetězce.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz.
  
*num_expr1*  
   Je číselný výraz, který označuje počáteční znak. Hodnota 0 je prvním znakem *str_expr*.
  
*num_expr2*  
   Je číselný výraz, který označuje maximální počet znaků *str_expr* , které mají být vráceny. Hodnota 0 nebo méně výsledků v prázdném řetězci.

## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí podřetězec "ABC" od 1 a po délku 1 znak.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Zde je sada výsledků.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude těžit z [indexu rozsahu](index-policy.md#includeexclude-strategy) , pokud je počáteční pozice `0` .

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
