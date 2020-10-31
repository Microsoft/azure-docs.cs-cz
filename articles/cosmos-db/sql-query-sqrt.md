---
title: Odmocnina v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci systému SQL SQRT v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 64cef5fc3c6a90f31674cee245a7669addadb427
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082415"
---
# <a name="sqrt-azure-cosmos-db"></a>SQRT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí druhou odmocninu zadané číselné hodnoty.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SQRT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí čtvercové kořeny čísel 1-3.  
  
```sql
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 Zde je sada výsledků.  
  
```json
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
