---
title: ARRAY_CONTAINS v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si, jak pole obsahuje funkci systému SQL v Azure Cosmos DB vrátí logickou hodnotu, která označuje, zda pole obsahuje zadanou hodnotu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 35136bbcf8ada90681ea72d8396a58ab1bc02828
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090898"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Vrátí logickou hodnotu, která označuje, zda pole obsahuje zadanou hodnotu. Můžete kontrolovat částečnou nebo úplnou shodu objektu pomocí logického výrazu v rámci příkazu. 

## <a name="syntax"></a>Syntaxe
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Je výraz pole, který má být prohledán.  
  
*výrazu*  
   Je výraz, který se má najít.  

*bool_expr*  
   Je logický výraz. Pokud se vyhodnotí jako true a zadaná Hledaná hodnota je objekt, příkaz zkontroluje částečnou shodu (objekt hledání je podmnožinou jednoho z objektů). Pokud se vyhodnotí jako false, příkaz zkontroluje úplnou shodu všech objektů v poli. Výchozí hodnota, pokud není zadána, je false. 
  
## <a name="return-types"></a>Návratové typy
  
  Vrací logickou hodnotu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad, jak kontrolovat členství v poli pomocí `ARRAY_CONTAINS` .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Zde je sada výsledků.  
  
```json
[{"b1": true, "b2": false}]  
```  

Následující příklad ukazuje, jak vyhledat částečnou shodu JSON v poli pomocí ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Zde je sada výsledků.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Funkce pole Azure Cosmos DB](sql-query-array-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
