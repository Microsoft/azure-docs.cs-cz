---
title: NAHRADIT v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o nahrazení funkce systém SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2cb7d82efd010fd7c3395a4f6a9217370d9e5779
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349578"
---
# <a name="replace-azure-cosmos-db"></a>NAHRADIT (Azure Cosmos DB)
 Nahradí všechny výskyty zadaná řetězcová hodnota s jinou hodnotou řetězce.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězcový výraz, který má být prohledán.  
  
*str_expr2*  
   Je řetězcový výraz, který se má najít.  
  
*str_expr3*  
   Je řetězcový výraz, který nahradí výskyty *str_expr2* v *str_expr1*.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak použít `REPLACE` v dotazu.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Tady je sada výsledků.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
