---
title: Matematické funkce v dotazovacím jazyce Azure Cosmos DB
description: Další informace o matematických funkcích v Azure Cosmos DB k provedení výpočtu na základě vstupních hodnot, které jsou k dispozici jako argumenty, a vrácení číselné hodnoty.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873263"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Matematické funkce (Azure Cosmos DB)  

Matematické funkce každý provést výpočet, na základě vstupních hodnot, které jsou k dispozici jako argumenty a vrátit číselnou hodnotu.

Dotazy můžete spouštět v následujícím příkladu:

```sql
    SELECT VALUE ABS(-4)
```

Výsledkem je:

```json
    [4]
```

## <a name="functions"></a>Funkce

Následující podporované předdefinované matematické funkce provádějí výpočet, obvykle založený na vstupních argumentech, a vracejí číselný výraz.
  
||||  
|-|-|-|  
|[Abs](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[Strop](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[Exp](sql-query-exp.md)|[Podlaze](sql-query-floor.md)|[Protokolu](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[Kolo](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[Hřích](sql-query-sin.md)|[Sqrt](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[Trunc](sql-query-trunc.md)||  
  
Všechny matematické funkce, s výjimkou funkce NÁHNE, jsou deterministické funkce. To znamená, že vrátí stejné výsledky pokaždé, když jsou volány s konkrétní sadu vstupních hodnot.

## <a name="next-steps"></a>Další kroky

- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
- [Uživatelem definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregates.md)
