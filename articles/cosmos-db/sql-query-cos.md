---
title: COS v dotazovacím jazyce Azure Cosmos DB
description: Informace o tom, jak funkce systému Cosine (COS) SQL v Azure Cosmos DB vrací goniometrický kosinus zadaného úhlu v radiánech v určeném výrazu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304017"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Vrátí goniometrický kosinus zadaného úhlu v radiánech v určeném výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočítá `COS` zadaný úhel.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Zde je sada výsledků.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
