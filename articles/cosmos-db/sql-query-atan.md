---
title: ATAN v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci SQL System Functions ATAN v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4ee3867f3a4938358e8d61aa8a98f747756ee3e8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348562"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Vrací úhel v radiánech, jehož tangens odpovídá zadané číselný výraz. Označuje se také Arkus tangens.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí `ATAN` zadané hodnoty.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Tady je sada výsledků.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
