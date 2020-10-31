---
title: REPLIKovat v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o replikaci funkce systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8b1fc1cb28b62a388df53238df58420bd3317a8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082857"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKovat (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zopakuje řetězcovou hodnotu v zadaném počtu opakování.
  
## <a name="syntax"></a>Syntaxe
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz.
  
*num_expr*  
   Je numerický výraz. Pokud je *num_expr* záporné nebo nekonečno, výsledek není definován.
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.
  
## <a name="remarks"></a>Poznámky

  Maximální délka výsledku je 10 000 znaků, například (length ( *str_expr* ) *  *num_expr* ) <= 10 000. Tato systémová funkce nebude index využívat.

## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak použít `REPLICATE` v dotazu.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Zde je sada výsledků.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
