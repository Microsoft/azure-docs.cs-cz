---
title: CONCAT v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci systému SQL CONCAT v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e61d61a3d64ca7d7808619159e4dfc8e8b33d68
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351266"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcových hodnot.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který se má zřetězit k ostatním hodnotám. Funkce `CONCAT` vyžaduje nejméně dva argumenty *str_expr* .  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrací spojený řetězec ze zadaných hodnot.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Tady je sada výsledků.  
  
```json
[{"concat": "abcdef"}]  
```  
  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
