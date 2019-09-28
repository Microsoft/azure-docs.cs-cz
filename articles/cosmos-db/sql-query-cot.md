---
title: COT v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o COT funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d980319730e69fdcb529272ba1b8fb48d2b5b230
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351245"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Vrátí trigonometrických kotangens úhlu určeného v radiánech v zadané číselný výraz.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočítá `COT` zadaného úhlu.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Tady je sada výsledků.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
