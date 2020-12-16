---
title: Tabulka tahák dotazu protokolu SQL do protokolu Azure Monitor | Microsoft Docs
description: Nápovědu pro uživatele obeznámené s SQL při psaní dotazů protokolu v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: e9f937323f4e14b5c8c3f30c94bf9d2daef0baea
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606687"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>Tabulka tahák dotazu protokolu SQL pro Azure Monitor 

Tabulka níže pomáhá uživatelům, kteří jsou obeznámeni s SQL, získat informace o jazyce dotazů Kusto pro zápis dotazů protokolu v Azure Monitor. Podíváme se na příkaz T-SQL pro řešení běžných scénářů a ekvivalentu v dotazu protokolu Azure Monitor.

## <a name="sql-to-azure-monitor"></a>SQL pro Azure Monitor

Popis |Dotaz SQL | Azure Monitor dotaz protokolu
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Výběr všech dat z tabulky |`SELECT * FROM dependencies` |<code>dependencies</code>
Vybrat konkrétní sloupce z tabulky |`SELECT name, resultCode FROM dependencies` |<code>dependencies <br>&#124; project name, resultCode</code>
Vybrat záznamy 100 z tabulky |`SELECT TOP 100 * FROM dependencies` |<code>dependencies <br>&#124; take 100</code>
Vyhodnocení hodnoty null |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL` |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Porovnání řetězců: rovnost |`SELECT * FROM dependencies WHERE name = "abcde"` |<code>dependencies <br>&#124; where name == "abcde"</code>
Porovnání řetězců: podřetězec |`SELECT * FROM dependencies WHERE name like "%bcd%"` |<code>dependencies <br>&#124; where name contains "bcd"</code>
Porovnání řetězců: zástupný znak |`SELECT * FROM dependencies WHERE name like "abc%"` |<code>dependencies <br>&#124; where name startswith "abc"</code>
Porovnání data: poslední 1 den |`SELECT * FROM dependencies WHERE timestamp > getdate()-1` |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Porovnání data: rozsah dat |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'` |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Logické porovnání |`SELECT * FROM dependencies WHERE !(success)` |<code>dependencies <br>&#124; where success == "False" </code>
Seřadit |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc` |<code>dependencies <br>&#124; order by timestamp asc </code>
Distinct |`SELECT DISTINCT name, type  FROM dependencies` |<code>dependencies <br>&#124; summarize by name, type </code>
Seskupení, agregace |`SELECT name, AVG(duration) FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Aliasy sloupce, rozšířené |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Prvních n záznamů podle míry |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc` |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Sjednocení |`SELECT * FROM dependencies UNION SELECT * FROM exceptions` |<code>union dependencies, exceptions</code>
Sjednocení: s podmínkami |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5` |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Spojit |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Další kroky

- Projděte si lekce o [psaní dotazů protokolu v Azure monitor](get-started-queries.md).
