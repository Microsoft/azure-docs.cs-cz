---
title: Dotazy protokolů užitečné operátory ve službě Azure Monitor | Dokumentace Microsoftu
description: Běžné funkce pro různé scénáře v dotazů na protokoly Azure monitoru.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: d11445c3f31f9aced6fdb9783575d10a026de1f0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000144"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Užitečné operátorů v dotazech protokolu Azure Monitor

Následující tabulka obsahuje některé běžné funkce pro různé scénáře v dotazů na protokoly Azure monitoru.

## <a name="useful-operators"></a>Užitečné operátory

Kategorie                                |Relevantní Analytics – funkce
----------------------------------------|----------------------------------------
Výběr a ve sloupci aliasy            |`project`, `project-away`, `extend`
Dočasné tabulky a konstanty          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Porovnání a operátory řetězce         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Běžné funkce řetězce                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Běžné matematické funkce                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Analýza textu                            |`extract()`, `extractjson()`, `parse`, `split()`
Omezení výstupu                         |`take`, `limit`, `top`, `sample`
Datové funkce                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Seskupení a agregace                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Spojení a sjednocení                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Pořadí řazení                             |`sort`, `order` 
Dynamických objektů (JSON a pole)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Logické operátory                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Strojové učení                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Další postup

- Projděte si lekci [psaní dotazů protokolu ve službě Azure Monitor](get-started-queries.md).
