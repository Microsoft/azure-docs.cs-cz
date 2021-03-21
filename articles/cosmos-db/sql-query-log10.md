---
title: Log10 – v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o funkci log10 – SQL System v Azure Cosmos DB, která vrátí logaritmus o základu 10 zadaného číselného výrazu.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27f7d916c1a3f84f26674fea6b04597fd9e546bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338423"
---
# <a name="log10-azure-cosmos-db"></a>Log10 – (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  Log10 – a POWER Functions spolu vzájemně souvisí. Například 10 ^ log10 – (n) = n. Tato systémová funkce nebude index využívat.
  
## <a name="examples"></a>Příklady
  
  Následující příklad deklaruje proměnnou a vrátí hodnotu log10 – zadané proměnné (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Zde je sada výsledků.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
