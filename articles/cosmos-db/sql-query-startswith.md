---
title: STARTSWITH v jazyce Azure Cosmos DB Query Language
description: Další informace o funkci SQL System Function STARTSWITH v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a7822425f17d6e121dfcb20d8766f0b3bc7032a2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349312"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce začíná druhé.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězcový výraz.
  
*str_expr2*  
   Je řetězcový výraz, který má být porovnán s počátkem *str_expr1*.

## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad zkontroluje, pokud se řetězec "abc" začíná písmenem "b" a "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Tady je sada výsledků.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
