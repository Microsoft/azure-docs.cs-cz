---
title: PŘÍMO v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci systému SQL přímo v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5aeee91db0b1ce891d7e4090a074ddda1f15a576
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349530"
---
# <a name="right-azure-cosmos-db"></a>RIGHT (Azure Cosmos DB)
 Vrátí část reprezentující správný řetězec zadaný počet znaků.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, ze kterého se mají extrahovat znaky.  
  
*num_expr*  
   Je číselný výraz, který určuje počet znaků.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí pravou část "abc" pro různé hodnoty pro délku.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Tady je sada výsledků.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
