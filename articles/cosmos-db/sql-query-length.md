---
title: Délka ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o délce funkce systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa430152415b1662a73a388a03ba6d4721c730f0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349751"
---
# <a name="length-azure-cosmos-db"></a>Délka (Azure Cosmos DB)
 Vrátí počet znaků ze zadaného řetězcového výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který má být vyhodnocen.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí délku řetězce.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Tady je sada výsledků.  
  
```json
[{"len": 3}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
