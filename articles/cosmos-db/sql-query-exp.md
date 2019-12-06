---
title: EXP in Azure Cosmos DB dotazovací jazyk
description: Další informace o funkci exponent (EXP) SQL System v Azure Cosmos DB pro návrat exponenciální hodnoty zadaného číselného výrazu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873314"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Vrátí hodnotu exponenciální zadaný číselný výraz.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="remarks"></a>Poznámky
  
  Konstanta **e** (2.718281...), je základ přirozeného logaritmu.  
  
  Exponent číslo je konstanta **e** umocněné na čísla. Například EXP(1.0) = e ^ 1.0 = 2.71828182845905 a EXP(10) = e ^ 10 = 22026.4657948067.  
  
  Exponent přirozený logaritmus čísla je číslo samotné: EXP (protokol (n).) = n. A přirozený logaritmus čísla exponenciální je číslo samotné: protokolu (EXP (n).) = n.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad deklaruje proměnnou a vrátí hodnotu exponenciální hodnotu zadanou proměnnou (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Tady je sada výsledků.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 Následující příklad vrátí hodnotu exponenciální natural logarithm 20 a přirozený logaritmus exponenciální 20. Protože tyto funkce jsou inverzní funkce navzájem, vrácená hodnota s zaokrouhlení s plovoucí desetinnou čárkou matematické bod v obou případech je 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Tady je sada výsledků.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
