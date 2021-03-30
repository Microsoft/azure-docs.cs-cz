---
title: ATAN v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si, jak funkce arkustangens (ATAN) SQL System v Azure Cosmos DB vrátí úhel v radiánech, jehož tangens je zadaný numerický výraz.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50c678ad3f382ebdcf23c0ba125a47d7aaa7d7ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332436"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí úhel v radiánech, jejichž tangens je zadaný numerický výraz. Toto je také označováno jako arkustangens.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí `ATAN` zadanou hodnotu.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Zde je sada výsledků.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
