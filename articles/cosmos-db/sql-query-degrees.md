---
title: STUPNĚ ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o funkci DEGREES SQL System v Azure Cosmos DB, která vrátí odpovídající úhel ve stupních pro úhel zadaný v radiánech.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299466"
---
# <a name="degrees-azure-cosmos-db"></a>STUPNĚ (Azure Cosmos DB)
 Vrátí odpovídající úhel ve stupních, kterým je úhel zadaný v radiánech.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí počet stupňů v úhel PÍ/2 radiánů.  
  
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
- [Úvod do Azure Cosmos DB](introduction.md)
