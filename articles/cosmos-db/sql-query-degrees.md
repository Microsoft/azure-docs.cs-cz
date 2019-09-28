---
title: STUPNĚ ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o STUPNích funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8c2109c2c11f137b1966741a95d1d0c02895016
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351199"
---
# <a name="degrees-azure-cosmos-db"></a>STUPNĚ (Azure Cosmos DB)
 Vrátí odpovídající úhel ve stupních, kterým je úhel zadaný v radiánech.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí počet stupňů v úhel PÍ/2 radiánů.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Tady je sada výsledků.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
