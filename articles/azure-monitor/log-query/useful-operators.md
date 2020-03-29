---
title: Užiteční operátoři v dotazech protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Běžné funkce, které se používají pro různé scénáře v dotazech protokolu Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: ff63b9b7027e99c70971230936ed98186c2208e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397711"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Užiteční operátoři v dotazech protokolu Azure Monitor

Níže uvedená tabulka obsahuje některé běžné funkce, které se mají používat pro různé scénáře v dotazech protokolu Azure Monitor.

## <a name="useful-operators"></a>Užitečné operátory

Kategorie                                |Relevantní analytická funkce
----------------------------------------|----------------------------------------
Aliasy výběru a sloupce            |`project`, `project-away`, `extend`
Dočasné tabulky a konstanty          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Porovnání a operátory řetězců         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Běžné funkce řetězce                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Běžné matematické funkce                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Analýza textu                            |`extract()`, `extractjson()`, `parse`, `split()`
Omezení výstupu                         |`take`, `limit`, `top`, `sample`
Datové funkce                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Seskupení a agregace                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Spojení a sjednocení                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Seřadit, seřadit                             |`sort`, `order` 
Dynamický objekt (JSON a pole)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Logické operátory                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Strojové učení                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Další kroky

- Projděte si lekci [o dotazech protokolu zápisu v Azure Monitoru](get-started-queries.md).
