---
title: ZAOKROUHLit Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci SQL System ROUND v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7dc4d78f7af1086f9a4de9aa7392acb388df966e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590666"
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
|0,5|1|
|6,5000|7|
  
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
