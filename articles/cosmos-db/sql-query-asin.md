---
title: ASIN v dotazovacím jazyce Azure Cosmos DB
description: Informace o tom, jak funkce systému ARCSINE (ASIN) SQL v Azure Cosmos DB vrátí úhel v radiánech, jehož sinus je zadaný číselný výraz
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302691"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Vrátí úhel v radiánech, jehož sinus je zadaný číselný výraz. To se také nazývá arcsin.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí `ASIN` hodnotu -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Zde je sada výsledků.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
