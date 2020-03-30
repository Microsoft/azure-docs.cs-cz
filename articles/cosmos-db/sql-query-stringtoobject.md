---
title: StringToObject v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL StringToObject v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296377"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Vrátí výraz přeložený do objektu. Pokud výraz nelze přeložit, vrátí undefined.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který má být analyzován jako výraz objektu JSON. Všimněte si, že vnořené hodnoty řetězce musí být zapsány s dvojitými uvozovkami, aby byly platné. Podrobnosti o formátu JSON naleznete v [json.org](https://json.org/)  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí výraz objektu nebo nedefinovaný.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak `StringToObject` se chová napříč různými typy. 
  
 Následují příklady s platným vstupem.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Zde je sada výsledků.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Následují příklady s neplatným vstupem.
I když jsou platné v rámci dotazu, nebudou analyzovat platné objekty. Řetězce v řetězci objektu musí být\\buď\\uvozeny "{ "a":\\"str\\"}" nebo okolní uvozovky musí být jednoduché '{"a": "str"}'.

Jednoduché uvozovky obklopující názvy vlastností nejsou platné JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Zde je sada výsledků.

```json
[{}]
```  

Názvy vlastností bez okolních uvozovek nejsou platné JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Zde je sada výsledků.

```json
[{}]
``` 

Následují příklady s neplatným vstupem.

 Předaný výraz bude analyzován jako objekt JSON; tyto vstupy nevyhodnocují na typ objektu a proto vrátit undefined.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
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
