---
title: NÁHĚ v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL NÁHČÍSLO v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302215"
---
# <a name="rand-azure-cosmos-db"></a>Náhroce (Azure Cosmos DB)
 Vrátí náhodně generovanou číselnou hodnotu z [0,1).
 
## <a name="syntax"></a>Syntaxe
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Návratové typy

  Vrátí číselný výraz.

## <a name="remarks"></a>Poznámky

  `RAND`je nedeterministická funkce. Opakované volání `RAND` nevracejí stejné výsledky.

## <a name="examples"></a>Příklady
  
  Následující příklad vrátí náhodně generovanou číselnou hodnotu.
  
```sql
SELECT RAND() AS rand 
```  
  
 Zde je sada výsledků.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
