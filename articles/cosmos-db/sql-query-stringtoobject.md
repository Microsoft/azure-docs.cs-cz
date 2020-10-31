---
title: StringToObject v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o StringToObject funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3492d463133c15bd5e6cabb8a173ff806712125
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097086"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí výraz přeložený na objekt. Pokud výraz nelze přeložit, vrátí nedefinované funkce.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který má být analyzován jako výraz objektu JSON. Všimněte si, že hodnoty vnořených řetězců musí být zapsány s dvojitými uvozovkami, aby byly platné. Podrobnosti o formátu JSON najdete v tématu [JSON.org](https://json.org/) .  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí výraz objektu nebo nedefinovaný.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak se `StringToObject` chová v různých typech. 
  
 Níže jsou uvedeny příklady s platným vstupem.

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
I když jsou v rámci dotazu platné, nebudou analyzovány na platné objekty. Řetězce v řetězci objektu musí být buď uvozené řídicím znakem "{ \\ " a \\ ": \\ " str \\ "}", nebo okolní uvozovka musí být jednoduché "{" a ":" str "}".

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

 Předaný výraz se analyzuje jako objekt JSON. Tyto vstupy se nevyhodnotí na typ Object a tak vrátí nedefinované.

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

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
