---
title: StringToNumber v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL StringToNumber v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296418"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Vrátí výraz přeložený na Číslo. Pokud výraz nelze přeložit, vrátí undefined.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězec výraz, který má být analyzován jako výraz Číslo JSON. Čísla v JSON musí být celé číslo nebo plovoucí bod. Podrobnosti o formátu JSON naleznete v [json.org](https://json.org/)  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí výraz Číslo nebo nedefinovaný.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak `StringToNumber` se chová napříč různými typy. 

Mezery jsou povoleny pouze před nebo za Number.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Zde je sada výsledků.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

V JSON platné číslo musí být celé číslo nebo číslo s plovoucí desetinnou úsecí.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Zde je sada výsledků.  
  
```json
{{}}
```  

Předaný výraz bude analyzován jako výraz Number; tyto vstupy nevyhodnocují na typ Číslo a proto vrátí undefined. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Zde je sada výsledků.  
  
```json
{{}}
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
