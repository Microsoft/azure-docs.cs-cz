---
title: TAN v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci SQL System Function TAN v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70636dbc00307ced8ad0cf84f41fe025c759997e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349156"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Vrátí tangens úhlu určeného v radiánech v zadaným výrazem.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočítá tangens (PÍ) / 2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Tady je sada výsledků.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
