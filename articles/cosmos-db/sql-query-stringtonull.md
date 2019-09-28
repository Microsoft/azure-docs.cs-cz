---
title: StringToNull v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o StringToNull funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c7ea3ca82cadb1351d8581f3d652ce060d67969b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349243"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Vrátí výraz, který je přeložen na hodnotu null. Pokud výraz nelze přeložit, vrátí nedefinované funkce.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který má být analyzován jako výraz s hodnotou null.
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí nulový výraz nebo nedefinovaný.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak `StringToNull` se chová v různých typech. 

Níže jsou uvedeny příklady s platným vstupem.

 Prázdný znak je povolen pouze před nebo za "null".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Tady je sada výsledků.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Následují příklady s neplatným vstupem.

Hodnota null rozlišuje velká a malá písmena a musí být zapsána bez malých písmen, tj. "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Tady je sada výsledků.  
  
```json
[{}]
```  

Předaný výraz se analyzuje jako výraz s hodnotou null. Tyto vstupy se nevyhodnotí na typ null a proto se vrátí nedefinované.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Tady je sada výsledků.  
  
```json
[{}]
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
