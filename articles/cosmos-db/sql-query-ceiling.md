---
title: STROP v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o STROPu funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7056cfd1773e0d98fa134c9fdec3ad6a43af1bcf
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351114"
---
# <a name="ceiling-azure-cosmos-db"></a>STROP (Azure Cosmos DB)
 Vrátí nejmenší hodnotu celé číslo větší než nebo rovna zadané číselný výraz.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad zobrazuje kladné číselné, záporné a nulové hodnoty pomocí funkce `CEILING`.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Tady je sada výsledků.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
