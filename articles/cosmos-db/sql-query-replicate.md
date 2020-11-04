---
title: REPLIKovat v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o replikaci funkce systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7c0f1673c620ceadeb5ccca2a15cc9b7ce8d7685
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341599"
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
