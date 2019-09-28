---
title: IS_DEFINED v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o IS_DEFINED funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c3193262a41b3c6772d4fe29c78a132bc51bbd8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349869"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Vrátí logickou hodnotu označující, pokud vlastnost byla přiřazena hodnota.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*výrazu*  
   Je libovolný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad zkontroluje přítomnost vlastnosti v rámci zadaného dokumentu JSON. První vrátí hodnotu true, protože "a" je k dispozici, ale druhá vrátí hodnotu false, protože chybí "b".  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Tady je sada výsledků.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="next-steps"></a>Další kroky

- [Funkce pro kontrolu typu Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
