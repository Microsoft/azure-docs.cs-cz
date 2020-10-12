---
title: ASIN v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si, jak funkce arkussinus (ASIN) systému SQL v Azure Cosmos DB vrátí úhel v radiánech, jejichž sinus je zadaný numerický výraz.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302691"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Vrátí úhel v radiánech, jejichž sinus je zadaný numerický výraz. Tato metoda se označuje také jako arkussinus.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí hodnotu `ASIN` -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Zde je sada výsledků.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
