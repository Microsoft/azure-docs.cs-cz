---
title: EXP in Azure Cosmos DB dotazovací jazyk
description: Další informace o funkci exponent (EXP) SQL System v Azure Cosmos DB pro návrat exponenciální hodnoty zadaného číselného výrazu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d12ab39474f1df139bf2f9064d43f5ab7ecda16b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100265"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Vrátí exponenciální hodnotu zadaného číselného výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="remarks"></a>Poznámky
  
  Konstanta **e** (2,718281...) je základem přirozených logaritmů.  
  
  Exponent čísla je konstanta **e** umocněná na mocninu čísla. Například EXP (1.0) = e ^ 1.0 = 2.71828182845905 and EXP (10) = e ^ 10 = 22026.4657948067.  
  
  Exponenciální hodnota přirozeného logaritmu čísla je samotné číslo: EXP (LOG (n)) = n. A přirozený logaritmus exponenciálního čísla je samotné číslo: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad deklaruje proměnnou a vrátí exponenciální hodnotu zadané proměnné (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Zde je sada výsledků.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 Následující příklad vrátí exponenciální hodnotu přirozeného logaritmu 20 a přirozený logaritmus exponenciální hodnoty 20. Vzhledem k tomu, že tyto funkce jsou inverzní funkce navzájem, návratová hodnota se zaokrouhlením pro matematiku s plovoucí desetinnou čárkou v obou případech je 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Zde je sada výsledků.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
