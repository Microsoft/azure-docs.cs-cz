---
title: RAND v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci systému SQL NÁHČÍSLO v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fb3e310970fcc2146ee0d4b790a9744dcd566bad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341650"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí náhodně generovanou číselnou hodnotu z [0, 1).
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Návratové typy

  Vrátí číselný výraz.

## <a name="remarks"></a>Poznámky

  `RAND` je nedeterministické funkce. Opakující se volání `RAND` nevrací stejné výsledky. Tato systémová funkce nebude index využívat.


## <a name="examples"></a>Příklady
  
  Následující příklad vrací náhodně generovanou číselnou hodnotu.
  
```sql
SELECT RAND() AS rand 
```  
  
 Zde je sada výsledků.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
