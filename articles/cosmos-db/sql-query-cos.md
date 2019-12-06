---
title: COS v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si, jak funkce kosinus (COS) SQL System v Azure Cosmos DB vrací trigonometrický kosinus zadaného úhlu v radiánech v zadaném výrazu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 05546ef531059e05a1426a288a48446d63d8e5df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873399"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Vrátí trigonometrických kosinus úhlu určeného v radiánech v zadaným výrazem.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočítá `COS` zadaného úhlu.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Tady je sada výsledků.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
