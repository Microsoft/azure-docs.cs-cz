---
title: COT v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si, jak funkce kotangens (COT) SQL System v Azure Cosmos DB vrátí trigonometrický kotangens zadaného úhlu v radiánech v zadaném číselném výrazu.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c247ac477f92985fc722f9c06655b6a7474876fb
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339019"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí trigonometrický kotangens zadaného úhlu v radiánech v zadaném číselném výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočte `COT` úhel zadaného úhlu.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Zde je sada výsledků.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
