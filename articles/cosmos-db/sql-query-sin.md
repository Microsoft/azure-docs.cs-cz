---
title: SIN v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL SIN v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25e7cf66fdd55a0b641c35443e38b0a67cbe365d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303099"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 Vrátí goniometrické sinus zadaného úhlu v radiánech v určeném výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočítá `SIN` zadaný úhel.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Zde je sada výsledků.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
