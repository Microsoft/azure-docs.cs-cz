---
title: StringToNull v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL StringToNull v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296435"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Vrátí výraz přeložený na hodnotu null. Pokud výraz nelze přeložit, vrátí undefined.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězec výraz, který má být analyzován jako prázdný výraz.
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí nulový výraz nebo nedefinovaný.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak `StringToNull` se chová napříč různými typy. 

Následují příklady s platným vstupem.

 Mezery jsou povoleny pouze před nebo za "null".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Zde je sada výsledků.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Následují příklady s neplatným vstupem.

Null rozlišuje malá a malá písmena a musí být zapsána se všemi znaky s nižšími písmeny, tj.

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Zde je sada výsledků.  
  
```json
[{}]
```  

Předaný výraz bude analyzován jako prázdný výraz; tyto vstupy nevyhodnocují na typ null a proto vrátit undefined.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
