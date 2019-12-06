---
title: ASIN v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si, jak funkce arkussinus (ASIN) systému SQL v Azure Cosmos DB vrátí úhel v radiánech, jejichž sinus je zadaný numerický výraz.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e790eb5ae5eb780637b199a1a65dec2dd02d1bc
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871734"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Vrací úhel v radiánech, jehož sinus je zadaný číselný výraz. Označuje se také Arkus sinus.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí `ASIN` hodnotu-1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Tady je sada výsledků.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
