---
title: Prům. Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o průměrné (průměrné) systémové funkci SQL v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553387"
---
# <a name="avg-azure-cosmos-db"></a>PRŮMĚR (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tato agregační funkce vrací průměr hodnot ve výrazu.
  
## <a name="syntax"></a>Syntaxe
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
Následující příklad vrací průměrnou hodnotu `propertyA` :
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy). Pokud jsou argumenty v `AVG` typu String, Boolean nebo null, vrátí celá agregační systémová funkce `undefined` . Pokud má libovolný argument `undefined` hodnotu, nebude mít vliv na `AVG` Výpočet.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce v Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce v Azure Cosmos DB](sql-query-system-functions.md)
- [Agregační funkce v Azure Cosmos DB](sql-query-aggregate-functions.md)