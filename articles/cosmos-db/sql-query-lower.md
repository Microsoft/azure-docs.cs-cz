---
title: NIŽŠÍ v dotazovacím jazyce Azure Cosmos DB
description: Informace o funkci nižšího systému SQL v Azure Cosmos DB vrátit řetězcový výraz po převodu velkých znakových dat na malá písmena
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302266"
---
# <a name="lower-azure-cosmos-db"></a>NIŽŠÍ (Azure Cosmos DB)
 Vrátí řetězcový výraz po převodu velkých znakových dat na malá písmena.  

Funkce systému LOWER nevyužívá index. Pokud máte v plánu provádět časté porovnávání bez rozlišování velkých a malých písmen, funkce nižší systém může spotřebovat značné množství Ru. Pokud se jedná o tento případ, namísto použití funkce nižší systém normalizovat data pokaždé, když pro porovnání, můžete normalizovat kryt při vložení. Pak dotaz jako SELECT * FROM c KDE LOWER(c.name) = 'bob' jednoduše stane SELECT * FROM c WHERE c.name = 'bob'.

## <a name="syntax"></a>Syntaxe
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězec výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězec výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, `LOWER` jak se používá v dotazu.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Zde je sada výsledků.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
