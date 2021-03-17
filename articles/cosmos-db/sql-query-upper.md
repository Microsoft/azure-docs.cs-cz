---
title: HORNÍ v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci systému SQL UPPER v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 06f85d97266e78b343ad8da233b77e369da5ee65
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334901"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí řetězcový výraz po převodu malých znakových dat na velká písmena.  

Funkce horní soustavy nevyužívá index. Pokud se chystáte provádět časté porovnávání velkých a malých písmen, může funkce HORNÍch systémových funkcí spotřebovat značné množství RU. Pokud se jedná o tento případ, namísto použití horní systémové funkce k normalizaci dat pokaždé, když se postará o porovnávání, můžete při vložení normalizovat velká a malá písmena. Pak dotaz, jako je SELECT * FROM c, kde UPPER (c. Name) = ' BOB ', se jednoduše vybere jako * FROM c WHERE c.name = ' BOB '.

## <a name="syntax"></a>Syntaxe
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak použít `UPPER` v dotazu.  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Zde je sada výsledků.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
