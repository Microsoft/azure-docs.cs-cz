---
title: Matematické funkce v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o matematických funkcích v Azure Cosmos DB k provedení výpočtu na základě vstupních hodnot, které jsou k dispozici jako argumenty, a vrácení číselné hodnoty.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bd53feb175c5be77f559a4d2e724a55e41df48eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85562826"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Matematické funkce (Azure Cosmos DB)  

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
* [MAXIMÁLNÍ](sql-query-ceiling.md)
* [COS](sql-query-cos.md)
* [COT](sql-query-cot.md)
* [URČITÝ](sql-query-degrees.md)
* [OČEK](sql-query-exp.md)
* [ŘÍZENÍ](sql-query-floor.md)
* [PROTOKOLU](sql-query-log.md)
* [Log10 –](sql-query-log10.md)
* [ČÍSLA](sql-query-pi.md)
* [POWER](sql-query-power.md)
* [RAD](sql-query-radians.md)
* [FUNKCÍ](sql-query-rand.md)
* [ZPOŽDĚNÍ](sql-query-round.md)
* [OSOBĚ](sql-query-sign.md)
* [SIN](sql-query-sin.md)
* [SQRT](sql-query-sqrt.md)
* [ČTVERCOVÉ](sql-query-square.md)
* [TAN](sql-query-tan.md)
* [TRUNC](sql-query-trunc.md)

  
Všechny matematické funkce s výjimkou funkce RAND jsou deterministické funkce. To znamená, že vrátí stejné výsledky pokaždé, když jsou volány pomocí konkrétní sady vstupních hodnot.

## <a name="next-steps"></a>Další kroky

- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregates.md)
