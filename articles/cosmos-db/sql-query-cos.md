---
title: COS v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si, jak funkce kosinus (COS) SQL System v Azure Cosmos DB vrací trigonometrický kosinus zadaného úhlu v radiánech v zadaném výrazu.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f11307d4f24bab06973e33b4640e26b1c445336
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339018"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
