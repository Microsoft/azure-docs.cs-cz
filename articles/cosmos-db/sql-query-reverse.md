---
title: OBRÁTIT se na Azure Cosmos DB dotazovací jazyk
description: Přečtěte si informace o funkci systému SQL obrátit v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8237918232bd8ba8edb2b8f71440ffd73a913334
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349557"
---
# <a name="reverse-azure-cosmos-db"></a>OBRÁTIT (Azure Cosmos DB)
 Vrátí hodnotu řetězce obráceném pořadí.  
  
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
  
 Tady je sada výsledků.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
