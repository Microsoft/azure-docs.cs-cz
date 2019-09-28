---
title: Podřetězec v jazyce Azure Cosmos DB Query
description: Přečtěte si o podřetězcůch funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bf14bda9bd1acc62820bf07f83ac074a8d1b691c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349201"
---
# <a name="substring-azure-cosmos-db"></a>Podřetězec (Azure Cosmos DB)
 Vrátí část řetězcového výrazu počínaje pozice s nulovým základem zadaný znak a pokračuje na určenou délku nebo na konci řetězce.  
  
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
  
  Následující příklad vrátí podřetězec "abc", spouštění na 1 a pro o délce 1 znak.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Tady je sada výsledků.  
  
```json
[{"substring": "b"}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
