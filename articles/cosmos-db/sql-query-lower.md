---
title: NIŽŠÍ v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o nižší funkci systému SQL v Azure Cosmos DB pro vrácení řetězcového výrazu po převedení velkých znakových dat na malá písmena.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 163b893b74b5f6d3950ae92e209f96abde8cd90e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075275"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
