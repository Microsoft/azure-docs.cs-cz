---
title: COT v dotazovacím jazyce Azure Cosmos DB
description: Informace o tom, jak funkce systému Cotangent(COT) SQL v Azure Cosmos DB vrací goniometrický kotangent zadaného úhlu v radiánech v zadaném číselném výrazu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299483"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Vrátí goniometrický kotangent zadaného úhlu v radiánech v zadaném číselném výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočítá `COT` zadaný úhel.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Zde je sada výsledků.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
