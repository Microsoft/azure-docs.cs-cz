---
title: CONCAT v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si, jak funkce CONCAT SQL System v Azure Cosmos DB vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcových hodnot.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0b3401e8de4987670f74a02286cb66eb7a9dfb29
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081616"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
