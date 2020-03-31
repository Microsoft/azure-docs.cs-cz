---
title: StringToArray v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL StringToArray v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302912"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Vrátí výraz přeložený do pole. Pokud výraz nelze přeložit, vrátí undefined.  
  
## <a name="syntax"></a>Syntaxe
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který má být analyzován jako výraz JSON Array. 
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí výraz pole nebo nedefinovaný. 
  
## <a name="remarks"></a>Poznámky
  Vnořené hodnoty řetězce musí být zapsány s dvojitými uvozovkami, aby byly platné JSON. Podrobnosti o formátu JSON naleznete v [json.org](https://json.org/)
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak `StringToArray` se chová napříč různými typy. 
  
 Následují příklady s platným vstupem.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Zde je sada výsledků.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Následuje příklad neplatného vstupu. 
   
 Jednoduché uvozovky v rámci pole nejsou platné JSON.
I když jsou platné v rámci dotazu, nebudou analyzovat na platná pole. Řetězce v řetězci pole musí\\být\\buď uvozeny uvozeny "[ " "]" nebo okolní uvozovky musí být jednoduché '[""]'.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Zde je sada výsledků.

```json
[{}]
```

Následují příklady neplatného vstupu.
   
 Předaný výraz bude analyzován jako pole JSON; následující nevyhodnocují typ pole a proto vrátit undefined.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Zde je sada výsledků.

```json
[{}]
```

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
