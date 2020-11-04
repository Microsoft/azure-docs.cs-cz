---
title: Matematické funkce v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o matematických funkcích v Azure Cosmos DB k provedení výpočtu na základě vstupních hodnot, které jsou k dispozici jako argumenty, a vrácení číselné hodnoty.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9dc3631b738da78df83113d89d23051033674d11
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338362"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Matematické funkce (Azure Cosmos DB)  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tyto matematické funkce provádějí výpočet na základě vstupních hodnot, které jsou k dispozici jako argumenty, a vracejí číselnou hodnotu.

Můžete spustit dotazy jako v následujícím příkladu:

```sql
    SELECT VALUE ABS(-4)
```

Výsledek je následující:

```json
    [4]
```

## <a name="functions"></a>Functions

Následující podporované integrované matematické funkce provádějí výpočet, který je obvykle založen na vstupních argumentech a vrací číselný výraz:
 
* [ABS](sql-query-abs.md)
* [ACOS](sql-query-acos.md)
* [ASIN](sql-query-asin.md)
* [ATAN](sql-query-atan.md)
* [ATN2](sql-query-atn2.md)
* [CEILING](sql-query-ceiling.md)
* [COS](sql-query-cos.md)
* [COT](sql-query-cot.md)
* [DEGREES](sql-query-degrees.md)
* [EXP](sql-query-exp.md)
* [FLOOR](sql-query-floor.md)
* [LOG](sql-query-log.md)
* [LOG10](sql-query-log10.md)
* [PI](sql-query-pi.md)
* [POWER](sql-query-power.md)
* [RADIANS](sql-query-radians.md)
* [RAND](sql-query-rand.md)
* [ROUND](sql-query-round.md)
* [SIGN](sql-query-sign.md)
* [SIN](sql-query-sin.md)
* [SQRT](sql-query-sqrt.md)
* [ČTVERCOVÉ](sql-query-square.md)
* [TAN](sql-query-tan.md)
* [TRUNC](sql-query-trunc.md)

  
Všechny matematické funkce s výjimkou funkce RAND jsou deterministické funkce. To znamená, že vrátí stejné výsledky pokaždé, když jsou volány pomocí konkrétní sady vstupních hodnot.

## <a name="next-steps"></a>Další kroky

- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregates.md)
