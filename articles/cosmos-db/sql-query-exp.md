---
title: EXP v dotazovacím jazyce Azure Cosmos DB
description: Informace o funkci systému Exponent (EXP) SQL v Azure Cosmos DB vrátí exponenciální hodnotu zadaného číselného výrazu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873314"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Vrátí exponenciální hodnotu zadaného číselného výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="remarks"></a>Poznámky
  
  Konstanta **e** (2.718281...) je základem přirozených logaritmů.  
  
  Exponent čísla je konstanta **e** umocněná na mocninu čísla. Například EXP(1.0) = e^1.0 = 2.71828182845905 a EXP(10) = e^10 = 22026.4657948067.  
  
  Exponenciální přirozenélogaritu čísla je číslo samo o sobě: EXP (LOG (n)) = n. A přirozený logaritmus exponenciálního čísla je samotné číslo: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad deklaruje proměnnou a vrátí exponenciální hodnotu zadané proměnné (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Zde je sada výsledků.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 Následující příklad vrátí exponenciální hodnotu přirozeného logaritmu 20 a přirozený logaritmus exponenciál 20. Vzhledem k tomu, že tyto funkce jsou inverzní funkce navzájem, vrácená hodnota s zaokrouhlení pro matematiku s plovoucí desetinnou desetinnou desetinnou desetinnou hodnotou v obou případech je 20.  
  
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
- [Úvod do Azure Cosmos DB](introduction.md)
