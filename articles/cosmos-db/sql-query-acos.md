---
title: ACOS v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si, jak funkce systému SQL ACOS (arccosice) v Azure Cosmos DB vrátí úhel v radiánech, jehož kosinus je zadaný numerický výraz.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ed5adaad6c0ee160fd55341e1d7c48fcf3e49a28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93332793"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí úhel v radiánech, jehož kosinus je zadaný numerický výraz; nazývá se také arkuskosinus.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí hodnotu `ACOS` -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Zde je sada výsledků.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
