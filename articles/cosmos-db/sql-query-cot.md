---
title: COT v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si, jak funkce kotangens (COT) SQL System v Azure Cosmos DB vrátí trigonometrický kotangens zadaného úhlu v radiánech v zadaném číselném výrazu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78299483"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
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
