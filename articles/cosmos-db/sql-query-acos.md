---
title: ACOS v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o ACOS funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c9d29fba6b5dc55a98bf90cfafe0940d7bf9674
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348742"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Vrací úhel v radiánech, jehož kosinus je zadaný číselný výraz. Zkratka Arkus kosinus.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí `ACOS` z-1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Tady je sada výsledků.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
