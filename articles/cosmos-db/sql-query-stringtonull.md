---
title: StringToNull v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o StringToNull funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78296435"
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
  
  Následující příklad ukazuje, jak se `StringToNull` chová v různých typech. 

Níže jsou uvedeny příklady s platným vstupem.

 Prázdný znak je povolen pouze před nebo za "null".

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

Hodnota null rozlišuje velká a malá písmena a musí být zapsána bez malých písmen, tj. "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Zde je sada výsledků.  
  
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
