---
title: Log10 – v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o funkci log10 – SQL System v Azure Cosmos DB, která vrátí logaritmus o základu 10 zadaného číselného výrazu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302487"
---
# <a name="log10-azure-cosmos-db"></a>Log10 – (Azure Cosmos DB)
 Vrátí logaritmus se základem 10 zadaného číselného výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expression*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="remarks"></a>Poznámky
  
  Log10 – a POWER Functions spolu vzájemně souvisí. Například 10 ^ log10 – (n) = n.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad deklaruje proměnnou a vrátí hodnotu log10 – zadané proměnné (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Zde je sada výsledků.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
