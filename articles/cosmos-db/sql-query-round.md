---
title: ZAOKROUHLit Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci SQL System ROUND v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e2447c713ff418f8d5ccc0e6bc64bf81f000524
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082721"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí číselnou hodnotu zaokrouhlenou na nejbližší celočíselnou hodnotu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="remarks"></a>Poznámky
  
  Operace zaokrouhlení proběhla podle středního bodu zaokrouhlení směrem od nuly. Pokud je vstupním výrazem, který se přesně shoduje se dvěma celými čísly, výsledkem bude nejbližší celočíselná hodnota od nuly. Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy).
  
  |<numeric_expr>|Zaokrouhlování|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0.5|1|
  |6,5000|7||
  
## <a name="examples"></a>Příklady
  
  Následující příklad zaokrouhlí následující kladná a záporná čísla na nejbližší celé číslo.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Zde je sada výsledků.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
