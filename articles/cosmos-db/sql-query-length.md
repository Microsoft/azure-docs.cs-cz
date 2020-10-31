---
title: Délka ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o délce funkce systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0c7766ac81df02755fad741c6fa733ba3163baff
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083647"
---
# <a name="length-azure-cosmos-db"></a>Délka (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí počet znaků zadaného řetězcového výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který má být vyhodnocen.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí délku řetězce.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Zde je sada výsledků.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
