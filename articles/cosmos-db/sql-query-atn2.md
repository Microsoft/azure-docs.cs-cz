---
title: ATN2 v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o ATN2 funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43bf2f6e27d093b72560b87349150268e0f58a60
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350191"
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
