---
title: HORNÍ v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci systému SQL UPPER v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6b0f025948803a23c5b3c8bb6415c0e111b946b2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349042"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Vrátí řetězcový výraz po převedení data znaků na malá písmena na velká písmena.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak použít `UPPER` v dotazu  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Tady je sada výsledků.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
