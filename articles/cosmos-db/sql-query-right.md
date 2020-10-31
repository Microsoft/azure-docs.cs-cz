---
title: PŘÍMO v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci systému SQL přímo v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3219ccc58d5a4c517adfaf9fef196fae3a955d3e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082823"
---
# <a name="right-azure-cosmos-db"></a>RIGHT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí pravou část řetězce se zadaným počtem znaků.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, ze kterého se mají extrahovat znaky.  
  
*num_expr*  
   Je číselný výraz, který určuje počet znaků.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí pravou část "ABC" pro různé hodnoty délky.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Zde je sada výsledků.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
