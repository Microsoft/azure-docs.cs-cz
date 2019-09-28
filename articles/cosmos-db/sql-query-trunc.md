---
title: TRUNC – v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o TRUNC – funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e9004db8f8e4ca3f4e3afd0600bc0abd78b1b754
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349086"
---
# <a name="trunc-azure-cosmos-db"></a>TRUNC – (Azure Cosmos DB)
 Vrátí číselnou hodnotu, zkrácen na nejbližší celočíselnou hodnotu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
TRUNC(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  V následujícím příkladu se zkrátí na následující kladná a záporná čísla na nejbližší celočíselnou hodnotu.  
  
```sql
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Tady je sada výsledků.  
  
```json
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
