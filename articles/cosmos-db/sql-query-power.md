---
title: VÝKON v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o výkonu funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349636"
---
# <a name="power-azure-cosmos-db"></a>NAPÁJENÍ (Azure Cosmos DB)
 Vrátí hodnotu zadaného výrazu na zadanou mocninu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr1*  
   Je číselný výraz.  
  
*numeric_expr2*  
   Je mocninou, kterou chcete vyvolat *numeric_expr1*.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje vyvolání číslo exponentem 3 (datové krychle čísla).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Tady je sada výsledků.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
