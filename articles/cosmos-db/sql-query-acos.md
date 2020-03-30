---
title: ACOS v dotazovacím jazyce Azure Cosmos DB
description: Informace o tom, jak funkce systému ACOS (arccosic) SQL v Azure Cosmos DB vrací úhel v radiánech, jejichž kosinus je zadaný číselný výraz
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300957"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Vrátí úhel v radiánech, jehož kosinus je zadaný číselný výraz; také nazývá arckosin.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí `ACOS` hodnotu -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Zde je sada výsledků.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
