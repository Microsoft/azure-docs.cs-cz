---
title: ZAOKROUHLit Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci SQL System ROUND v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302113"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
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
  
  Operace zaokrouhlení proběhla podle středního bodu zaokrouhlení směrem od nuly. Pokud je vstupním výrazem, který se přesně shoduje se dvěma celými čísly, výsledkem bude nejbližší celočíselná hodnota od nuly.  
  
  |<numeric_expr>|Zaokrouhlování|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0,5|1|
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

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
