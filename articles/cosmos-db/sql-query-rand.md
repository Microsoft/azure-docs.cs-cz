---
title: RAND v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci systému SQL NÁHČÍSLO v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4a0672000e630c9e06df84d9c2da5cb8b988c05a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349596"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Vrátí náhodně generovanou číselnou hodnotu z [0, 1).
 
## <a name="syntax"></a>Syntaxe
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Návratové typy

  Vrátí hodnotu číselného výrazu.

## <a name="remarks"></a>Poznámky

  `RAND` je nedeterministické funkce. Opakující se volání `RAND` nevrátí stejné výsledky.

## <a name="examples"></a>Příklady
  
  Následující příklad vrací náhodně generovanou číselnou hodnotu.
  
```sql
SELECT RAND() AS rand 
```  
  
 Tady je sada výsledků.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
