---
title: StringToBoolean v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o StringToBoolean funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78296537"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Vrátí výraz přeložený na logickou hodnotu. Pokud výraz nelze přeložit, vrátí nedefinované funkce.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který se má analyzovat jako logický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz nebo nedefinovaný.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak se `StringToBoolean` chová v různých typech. 
 
 Níže jsou uvedeny příklady s platným vstupem.

Prázdný znak je povolený jenom před nebo za "true"/"false".

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

 V logických hodnotách se rozlišují malá a velká písmena a musí být zapsána bez malých písmen, tj. "true" a "false".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Zde je sada výsledků.  
  
```json
[{}]
``` 

Předaný výraz bude analyzován jako logický výraz; Tyto vstupy se nevyhodnotí na typ Boolean, takže vrátí nedefinovanou hodnotu.

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

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
