---
title: OBSAHUJE v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si, jak funkce CONTAINS SQL System v Azure Cosmos DB vrátí logickou hodnotu, která označuje, zda první řetězcový výraz obsahuje sekundu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302589"
---
# <a name="contains-azure-cosmos-db"></a>OBSAHUJE (Azure Cosmos DB)
 Vrátí hodnotu typu Boolean označující, zda řetězec prvního výrazu obsahuje druhý.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězcový výraz, který má být prohledán.  
  
*str_expr2*  
   Je řetězcový výraz, který se má najít.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad zkontroluje, zda "ABC" obsahuje "AB" a "ABC" obsahuje "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Zde je sada výsledků.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
