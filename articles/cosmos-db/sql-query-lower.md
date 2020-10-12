---
title: NIŽŠÍ v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o nižší funkci systému SQL v Azure Cosmos DB pro vrácení řetězcového výrazu po převedení velkých znakových dat na malá písmena.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302266"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 Vrátí řetězcový výraz po převedení velkých znakových dat na malá písmena.  

NIŽŠÍ systémová funkce nevyužívá index. Pokud se chystáte rozlišovat velká a malá písmena, může nižší systémová funkce spotřebovat značné množství RU. Pokud se jedná o tento případ, místo použití nižší systémové funkce k normalizaci dat pokaždé pro porovnání můžete normalizovat velikost písmen při vložení. Pak dotaz, jako je SELECT * FROM c, kde LOWER (c. Name) = ' Bob ', se jednoduše vybere z jazyka c, kde c.name = ' Bob '.

## <a name="syntax"></a>Syntaxe
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak použít `LOWER` v dotazu.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Zde je sada výsledků.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
