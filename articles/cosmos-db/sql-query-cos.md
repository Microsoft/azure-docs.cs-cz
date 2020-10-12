---
title: COS v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si, jak funkce kosinus (COS) SQL System v Azure Cosmos DB vrací trigonometrický kosinus zadaného úhlu v radiánech v zadaném výrazu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78304017"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Vrátí trigonometrický kosinus zadaného úhlu v radiánech v zadaném výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočte `COS` úhel zadaného úhlu.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Zde je sada výsledků.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
