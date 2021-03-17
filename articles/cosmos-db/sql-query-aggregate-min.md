---
title: MINIMÁLNÍ v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci min (MIN) SQL System v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553381"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tato agregační funkce vrací minimum hodnot ve výrazu.
  
## <a name="syntax"></a>Syntaxe
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*scalar_expr*  
   Je skalární výraz. 
  
## <a name="return-types"></a>Návratové typy
  
Vrátí skalární výraz.  
  
## <a name="examples"></a>Příklady
  
Následující příklad vrátí minimální hodnotu `propertyA` :
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy). Argumenty v `MIN` můžou být číslo, řetězec, logická hodnota nebo hodnota null. Všechny nedefinované hodnoty budou ignorovány.

Při porovnávání různých typů dat se používá následující pořadí priorit (ve vzestupném pořadí):

- null
- boolean
- číslo
- řetězec

## <a name="next-steps"></a>Další kroky

- [Matematické funkce v Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce v Azure Cosmos DB](sql-query-system-functions.md)
- [Agregační funkce v Azure Cosmos DB](sql-query-aggregate-functions.md)