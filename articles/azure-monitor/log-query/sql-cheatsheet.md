---
title: Seznam taháků protokolu sql to Azure Monitor | Dokumenty společnosti Microsoft
description: Nápověda pro uživatele obeznámené s SQL při zápisu dotazů protokolu v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: c76ab145fd2fdd077075b345ecac9c6a473f2369
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75365185"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>Rozváděč dotazu na protokol sql to Azure Monitor 

Níže uvedená tabulka pomáhá uživatelům, kteří jsou obeznámeni s SQL naučit kusto dotazovací jazyk pro zápis dotazů protokolu v Azure Monitor. Podívejte se na příkaz T-SQL pro řešení běžných scénářů a ekvivalent v dotazu protokolu Azure Monitor.

## <a name="sql-to-azure-monitor"></a>SQL to Azure Monitor

Popis                             |Dotaz SQL                                                                                          |Dotaz protokolu azure monitoru
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Výběr všech dat z tabulky            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Výběr konkrétních sloupců z tabulky    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Výběr 100 záznamů z tabulky         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Vyhodnocení null                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Porovnání řetězců: rovnost             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Porovnání řetězců: podřetězec            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Porovnání řetězců: zástupný znak             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Porovnání data: poslední 1 den             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Porovnání dat: rozsah dat             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Logické porovnání                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Seřadit                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Distinct                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Seskupení, agregace                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Aliasy sloupců, Rozšíření                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Top n záznamů podle míry                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Sjednocení                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Unie: s podmínkami                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Spojit                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Další kroky

- Projděte si lekce psaní [dotazů protokolu v Azure Monitoru](get-started-queries.md).
