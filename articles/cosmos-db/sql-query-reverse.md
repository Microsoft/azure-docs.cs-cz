---
title: OBRÁTIT se na Azure Cosmos DB dotazovací jazyk
description: Přečtěte si informace o funkci systému SQL obrátit v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7d70d65609211ea18f566dbae42aca5231ed2eb7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341569"
---
# <a name="reverse-azure-cosmos-db"></a>OBRÁTIT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí obrácené pořadí řetězcové hodnoty.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězcový výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak použít `REVERSE` v dotazu.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Zde je sada výsledků.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
