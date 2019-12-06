---
title: ATN2 v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o tom, jak funkce ATN2 SQL System v Azure Cosmos DB vrací hlavní hodnotu tečny oblouku y/x vyjádřené v radiánech.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 11321ef9d7b81af279b04e0e435b19c645cf3bcf
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871648"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Vrátí hlavní hodnotu arkustangens y / x, vyjádřené v radiánech.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočítá ATN2 pro zadaný rozbočovač x a y komponenty.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Tady je sada výsledků.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
