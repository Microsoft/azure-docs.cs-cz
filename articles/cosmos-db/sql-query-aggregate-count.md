---
title: POČET ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o funkcích systému SQL počet (COUNT) v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551866"
---
# <a name="count-azure-cosmos-db"></a>POČET (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tato systémová funkce vrací počet hodnot ve výrazu.
  
## <a name="syntax"></a>Syntaxe
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*scalar_expr*  
   Je libovolný skalární výraz
  
## <a name="return-types"></a>Návratové typy
  
Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
Následující příklad vrátí celkový počet položek v kontejneru:
  
```sql
SELECT COUNT(1)
FROM c
``` 
POČET může jako vstup být libovolný skalární výraz. Následující dotaz vytvoří ekvivalentní výsledky:

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude těžit z [indexu rozsahu](index-policy.md#includeexclude-strategy) pro jakékoli vlastnosti ve filtru dotazu.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce v Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce v Azure Cosmos DB](sql-query-system-functions.md)
- [Agregační funkce v Azure Cosmos DB](sql-query-aggregate-functions.md)