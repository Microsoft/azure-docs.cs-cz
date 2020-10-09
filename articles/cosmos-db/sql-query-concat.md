---
title: CONCAT v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si, jak funkce CONCAT SQL System v Azure Cosmos DB vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcových hodnot.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302606"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcových hodnot.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který se má zřetězit k ostatním hodnotám. `CONCAT`Funkce vyžaduje alespoň dva argumenty *str_expr* .  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí zřetězený řetězec zadaných hodnot.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Zde je sada výsledků.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
