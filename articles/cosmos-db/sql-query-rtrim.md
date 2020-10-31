---
title: RTRIM v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci SQL System funkce RTRIM v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b24e83aef6ee36f9c55810e0f4607cd015213e53
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082688"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí řetězcový výraz po odebrání koncových prázdných hodnot.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je libovolný platný řetězcový výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak použít `RTRIM` uvnitř dotazu.  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Zde je sada výsledků.  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
