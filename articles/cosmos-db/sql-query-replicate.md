---
title: REPLIKovat v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o replikaci funkce systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5e0b7f29c503daa8a95dcc46238e60728c0cec50
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349570"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKovat (Azure Cosmos DB)
 Opakuje hodnotu řetězce zadaného počtu opakování.
  
## <a name="syntax"></a>Syntaxe
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz.
  
*num_expr*  
   Je číselný výraz. Pokud je *num_expr* negativní nebo nekonečný, výsledek není definován.
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.
  
## <a name="remarks"></a>Poznámky
  Maximální délka výsledku je 10 000 znaků, například (length (*str_expr*) * *num_expr*) < = 10 000.

## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak použít `REPLICATE` v dotazu.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Tady je sada výsledků.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
