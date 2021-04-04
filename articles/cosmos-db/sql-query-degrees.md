---
title: STUPNĚ ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o funkci DEGREES SQL System v Azure Cosmos DB, která vrátí odpovídající úhel ve stupních pro úhel zadaný v radiánech.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5c7aa5496533a42ad726f9ed8efd8ed7429375fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338879"
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
