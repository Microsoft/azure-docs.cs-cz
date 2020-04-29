---
title: OŘÍZNOUT v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkcích systému SQL pro OŘEZÁVÁNÍ v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 073efbd91e637a17693617b16c7787d61e150ad7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78304238"
---
# <a name="trim-azure-cosmos-db"></a>TRIM (Azure Cosmos DB)
 Vrátí řetězcový výraz po odebrání počátečních a koncových prázdných znaků.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
TRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak použít `TRIM` uvnitř dotazu.  
  
```sql
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Zde je sada výsledků.  
  
```json
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
