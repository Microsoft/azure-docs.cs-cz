---
title: Užitečné operátory v Azure Monitorch dotazech protokolu | Microsoft Docs
description: Běžné funkce, které se mají použít pro různé scénáře v Azure Monitor dotazy protokolu
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 022a9f638b3a7d8ae4ebeff8062f258ada7a14f8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932882"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Užitečné operátory v Azure Monitorch dotazech protokolu

Následující tabulka poskytuje některé běžné funkce pro různé scénáře v Azure Monitorch dotazech protokolu.

## <a name="useful-operators"></a>Užitečné operátory

Kategorie                                |Relevantní Analytická funkce
----------------------------------------|----------------------------------------
Výběr a aliasy sloupců            |`project`, `project-away`, `extend`
Dočasné tabulky a konstanty          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Operátory porovnání a řetězce         |`startswith`, `!startswith`, `has``!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in``!in` <br> `matches regex` <br> `==`, `=~`, `!=``!~`
Běžné řetězcové funkce                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()``strlen()`
Běžné matematické funkce                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Analýza textu                            |`extract()`, `extractjson()`, `parse``split()`
Omezení výstupu                         |`take`, `limit`, `top``sample`
Funkce data                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()``startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()``endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()``monthofyear()`
Seskupení a agregace                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()``sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()``argmin()` <br> `percentiles()`, `percentile_array()`
Spojení a sjednocení                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter``leftanti` <br> `union`
Seřadit, pořadí                             |`sort`, `order` 
Dynamic – objekt (JSON a Array)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Logické operátory                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()``binary_xor()`
Strojové učení                        |`evaluate autocluster`, `basket`, `diffpatterns``extractcolumns`


## <a name="next-steps"></a>Další kroky

- Projděte si lekci o [zápisu dotazů protokolu v Azure monitor](get-started-queries.md).
