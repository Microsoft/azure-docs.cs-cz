---
title: IS_ARRAY v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o IS_ARRAY funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 11ff29dcae7bf5c2e532eee658ee39d39b964e35
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350977"
---
# <a name="is_array-azure-cosmos-db"></a>IS_ARRAY (Azure Cosmos DB)
 Vrátí logickou hodnotu označující, zda je typ z určeného výrazu pole.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
IS_ARRAY(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*výrazu*  
   Je libovolný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad kontroluje objekty typu Boolean, Number, String, null, Object, Array a undefined pomocí funkce `IS_ARRAY`.  
  
```sql
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Tady je sada výsledků.  
  
```json
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  

## <a name="next-steps"></a>Další kroky

- [Funkce pro kontrolu typu Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
