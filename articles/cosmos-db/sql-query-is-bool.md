---
title: IS_BOOL v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o IS_BOOL funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cb928558483a703a554d3eb6eb049af544f72eb1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349893"
---
# <a name="is_bool-azure-cosmos-db"></a>IS_BOOL (Azure Cosmos DB)
 Vrátí logickou hodnotu označující, pokud typ z určeného výrazu je logická hodnota.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
IS_BOOL(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*výrazu*  
   Je libovolný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad kontroluje objekty typu Boolean, Number, String, null, Object, Array a undefined pomocí funkce `IS_BOOL`.  
  
```sql
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Tady je sada výsledků.  
  
```json
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  

## <a name="next-steps"></a>Další kroky

- [Funkce pro kontrolu typu Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
