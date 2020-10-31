---
title: STUPNĚ ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o funkci DEGREES SQL System v Azure Cosmos DB, která vrátí odpovídající úhel ve stupních pro úhel zadaný v radiánech.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 991d83a329603342975a8186fe704afc53813e08
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095760"
---
# <a name="degrees-azure-cosmos-db"></a>STUPNĚ (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí odpovídající úhel ve stupních pro úhel zadaný v radiánech.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí počet stupňů ve úhlu pí/2 radiánů.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Zde je sada výsledků.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
