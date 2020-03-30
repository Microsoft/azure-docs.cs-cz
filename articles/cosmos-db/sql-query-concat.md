---
title: CONCAT v dotazovacím jazyce Azure Cosmos DB
description: Informace o tom, jak funkce systému CONCAT SQL v Azure Cosmos DB vrátí řetězec, který je výsledkem zřetězení dvou nebo více hodnot řetězce
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302606"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Vrátí řetězec, který je výsledkem zřetězení dvou nebo více hodnot řetězce.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz zřetězit na jiné hodnoty. Funkce `CONCAT` vyžaduje alespoň dva *str_expr* argumenty.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězec výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí zřetězený řetězec zadaných hodnot.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Zde je sada výsledků.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
