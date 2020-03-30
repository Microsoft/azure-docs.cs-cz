---
title: StringToBoolean v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL StringToBoolean v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296537"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Vrátí výraz přeložený do logické hodnoty. Pokud výraz nelze přeložit, vrátí undefined.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který má být analyzován jako logický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz nebo nedefinovaný.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak `StringToBoolean` se chová napříč různými typy. 
 
 Následují příklady s platným vstupem.

Mezery jsou povoleny pouze před nebo za "true"/"false".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Zde je sada výsledků.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Následují příklady s neplatným vstupem.

 Logické hodnoty rozlišují malá a velká písmena a musí být zapsána se všemi znaky s malými písmeny, tj.

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Zde je sada výsledků.  
  
```json
[{}]
``` 

Předaný výraz bude analyzován jako logický výraz; tyto vstupy nevyhodnocují na typ logické a proto vrátit undefined.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
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
