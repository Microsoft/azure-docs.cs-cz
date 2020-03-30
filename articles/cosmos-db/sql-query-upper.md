---
title: HORNÍ v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL UPPER v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303966"
---
# <a name="upper-azure-cosmos-db"></a>HORNÍ (Azure Cosmos DB)
 Vrátí řetězcový výraz po převodu malých znakových dat na velká písmena.  

Funkce systému UPPER nevyužívá index. Pokud máte v plánu provádět časté porovnávání bez rozlišování velkých a malých písmen, funkce systému UPPER může spotřebovat značné množství RU. Pokud se jedná o tento případ, namísto použití funkce systému UPPER normalizovat data pokaždé, když pro porovnání, můžete normalizovat kryt při vložení. Pak dotaz jako SELECT * FROM c WHERE UPPER(c.name) = 'BOB' jednoduše stane SELECT * FROM c KDE c.name = 'BOB'.

## <a name="syntax"></a>Syntaxe
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězec výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězec výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, `UPPER` jak se používá v dotazu  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Zde je sada výsledků.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
