---
title: ZAOKROUHLit Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci SQL System ROUND v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4bad140ea1c6b1c59c8f752bc5336bbd49952e16
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349513"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
 Vrátí číselnou hodnotu, zaokrouhlený na nejbližší celočíselnou hodnotu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="remarks"></a>Poznámky
  
  Operace zaokrouhlení proběhla podle středního bodu zaokrouhlení směrem od nuly. Pokud je vstupním výrazem, který se přesně shoduje se dvěma celými čísly, výsledkem bude nejbližší celočíselná hodnota od nuly.  
  
  |< numeric_expr >|Zaokrouhleno|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0,5|1|
  |6,5000|7||
  
## <a name="examples"></a>Příklady
  
  V následujícím příkladu se zaokrouhlí na následující kladná a záporná čísla na nejbližší celé číslo.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Tady je sada výsledků.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
