---
title: Čtvereček v Azure Cosmos DB dotazovací jazyk
description: Další informace o funkci systému SQL ČTVERCe v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683db82f2574da9150fb64767047d6ada6d667da
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349414"
---
# <a name="square-azure-cosmos-db"></a>ČTVEREC (Azure Cosmos DB)
 Vrátí druhou mocninu zadané číselnou hodnotu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SQUARE(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí druhých mocnin čísla 1-3.  
  
```sql
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Tady je sada výsledků.  
  
```json
[{s1: 1, s2: 4, s3: 9}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
