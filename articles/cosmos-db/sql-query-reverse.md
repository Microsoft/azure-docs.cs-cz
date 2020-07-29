---
title: OBRÁTIT se na Azure Cosmos DB dotazovací jazyk
description: Přečtěte si informace o funkci systému SQL obrátit v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302164"
---
# <a name="reverse-azure-cosmos-db"></a>OBRÁTIT (Azure Cosmos DB)
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
- [Úvod do Azure Cosmos DB](introduction.md)
