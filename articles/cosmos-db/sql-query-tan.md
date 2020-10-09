---
title: TAN v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci SQL System Function TAN v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78301977"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Vrátí tangens zadaného úhlu v radiánech v zadaném výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočítá tangens pí ()/2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Zde je sada výsledků.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
