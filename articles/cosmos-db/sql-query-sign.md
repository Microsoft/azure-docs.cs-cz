---
title: PŘIHLÁŠENÍ Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o Azure Cosmos DB přihlašování funkcí systému SQL.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 11a9d0ad0c68e1ef9728c0180f136d075776abb6
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349479"
---
# <a name="sign-azure-cosmos-db"></a>SIGN (Azure Cosmos DB)
 Vrátí kladné (+ 1), nula (0) nebo záporné znaménko (-1), z určeného číselného výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SIGN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  V následujícím příkladu jsou vráceny hodnoty `SIGN` čísel od-2 do 2.  
  
```sql
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Tady je sada výsledků.  
  
```json
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
