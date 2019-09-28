---
title: SIN v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci systému SQL SIN v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b4450ea9dd50f8093fa9569d7f82fe124022c3a1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349463"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 Vrátí trigonometrických sinus úhlu určeného v radiánech v zadaným výrazem.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočítá `SIN` zadaného úhlu.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Tady je sada výsledků.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
