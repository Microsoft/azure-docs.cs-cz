---
title: MAXIMÁLNÍ hodnota v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci Max (MAX) SQL System v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96551867"
---
# <a name="max-azure-cosmos-db"></a>MAX (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tato agregační funkce vrací maximum hodnot ve výrazu.
  
## <a name="syntax"></a>Syntaxe
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumenty

*scalar_expr*  
   Je skalární výraz. 
  
## <a name="return-types"></a>Návratové typy
  
Vrátí skalární výraz.  
  
## <a name="examples"></a>Příklady
  
Následující příklad vrátí maximální hodnotu `propertyA` :
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy). Argumenty v `MAX` můžou být číslo, řetězec, logická hodnota nebo hodnota null. Všechny nedefinované hodnoty budou ignorovány.

Při porovnávání různých typů dat se používá následující pořadí priorit (v sestupném pořadí):

- řetězec
- číslo
- boolean
- null

## <a name="next-steps"></a>Další kroky

- [Matematické funkce v Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce v Azure Cosmos DB](sql-query-system-functions.md)
- [Agregační funkce v Azure Cosmos DB](sql-query-aggregate-functions.md)