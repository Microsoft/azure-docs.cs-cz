---
title: ARRAY_CONCAT v dotazovacím jazyce Azure Cosmos DB
description: Seznamte se s tím, jak funkce pole Concat SQL System v Azure Cosmos DB vrátí pole, které je výsledkem zřetězení dvou nebo více hodnot pole.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b6424a161b5fc4d0c075ade6f852b33dff20a09
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332742"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí pole, které je výsledkem zřetězení dvou nebo více hodnot pole.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Je výraz pole, který se má zřetězit k ostatním hodnotám. `ARRAY_CONCAT`Funkce vyžaduje alespoň dva argumenty *arr_expr* .  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí výraz pole.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad zřetězení dvou polí.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Zde je sada výsledků.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Funkce pole Azure Cosmos DB](sql-query-array-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
