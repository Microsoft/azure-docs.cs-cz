---
title: NIŽŠÍ v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o nižší funkci systému SQL v Azure Cosmos DB pro vrácení řetězcového výrazu po převedení velkých znakových dat na malá písmena.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f7cd864d90b8ab17a180a00a7a6d4bb683da9be
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873297"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 Vrátí řetězcový výraz po převedení dat velkým písmenem na malá písmena.  
  
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
  
 Tady je sada výsledků.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
