---
title: Sledování výkonu pomocí dmvs
description: Zjistěte a diagnostikujte běžné problémy s výkonem pomocí zobrazení dynamické správy ke sledování databáze Microsoft Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 04/19/2020
ms.openlocfilehash: 6f33f49be74419a8f0cd31d973d64798f5d76a2c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683007"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Sledování výkonu Azure SQL Database pomocí zobrazení dynamické správy

Microsoft Azure SQL Database umožňuje podmnožinu zobrazení dynamické správy diagnostikovat problémy s výkonem, které mohou být způsobeny blokovanými nebo dlouhotrvajícími dotazy, kritickými body prostředků, špatnými plány dotazů a tak dále. Toto téma obsahuje informace o tom, jak zjistit běžné problémy s výkonem pomocí zobrazení dynamické správy.

SQL Database částečně podporuje tři kategorie zobrazení dynamické správy:

- Zobrazení dynamické správy související s databází.
- Zobrazení dynamické správy související s prováděním.
- Zobrazení dynamické správy související s transakcí.

Podrobné informace o zobrazeních dynamické správy naleznete v [tématu Dynamic kácení a funkce správy (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) v SQL Server Books Online.

## <a name="permissions"></a>Oprávnění

V databázi SQL vyžaduje dotazování na zobrazení dynamické správy oprávnění **STAVU DATABÁZE ZOBRAZENÍ.** Oprávnění **STAV DATABÁZE ZOBRAZENÍ** vrátí informace o všech objektech v aktuální databázi.
Chcete-li udělit oprávnění **STAVU DATABÁZE ZOBRAZENÍ** určitému uživateli databáze, spusťte následující dotaz:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

V instanci místního serveru SQL Server vrátí zobrazení dynamické správy informace o stavu serveru. V databázi SQL vrátí informace týkající se pouze aktuální logické databáze.

Tento článek obsahuje kolekci dotazů DMV, které můžete spustit pomocí SQL Server Management Studio nebo Azure Data Studio ke zjištění následujících typů problémů s výkonem dotazů:

- [Identifikace dotazů souvisejících s nadměrnou spotřebou procesoru](#identify-cpu-performance-issues)
- [PAGELATCH_* a WRITE_LOG čeká v souvislosti s iO úzkými místy](#identify-io-performance-issues)
- [PAGELATCH_* čeká způsobené konflikty bytTempDB](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE čeká způsobené problémy s čekáním na udělení paměti](#identify-memory-grant-wait-performance-issues)
- [Identifikace velikosti databáze a objektů](#calculating-database-and-objects-sizes)
- [Načítání informací o aktivních relacích](#monitoring-connections)
- [Načtení informací o využití prostředků pro celý systém a databázové prostředky](#monitor-resource-use)
- [Načítání informací o výkonu dotazu](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>Identifikace problémů s výkonem procesoru

Pokud je spotřeba procesoru po delší dobu vyšší než 80 %, zvažte následující kroky řešení potíží:

### <a name="the-cpu-issue-is-occurring-now"></a>Problém s procesorem se nyní vyskytuje

Pokud k problému dochází právě teď, existují dva možné scénáře:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Mnoho jednotlivých dotazů, které kumulativně spotřebovávají vysoký procesor

K identifikaci hlavních hodnot hashe dotazů použijte následující dotaz:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Dlouho spuštěné dotazy, které spotřebovávají procesor, jsou stále spuštěny.

K identifikaci těchto dotazů použijte následující dotaz:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>K problému s procesorem došlo v minulosti

Pokud k problému došlo v minulosti a chcete provést analýzu hlavní příčiny, použijte [Úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Uživatelé s přístupem k databázi mohou pomocí t-SQL dotazovat data úložiště dotazů. Výchozí konfigurace úložiště dotazů používají rozlišovací schopnost 1 hodinu. Následující dotaz slouží k zobrazení aktivity pro dotazy s vysokou spotřebou procesoru. Tento dotaz vrátí prvních 15 dotazů náročných na procesor. Nezapomeňte změnit `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Jakmile identifikujete problematické dotazy, je čas tyto dotazy vyladit, abyste snížili využití procesoru.  Pokud nemáte čas naladit dotazy, můžete také provést upgrade SLO databáze k řešení problému.

## <a name="identify-io-performance-issues"></a>Identifikace problémů s výkonem vi.

Při identifikaci problémů s výkonem vi, horní typy čekání spojené s problémy vi jsou:

- `PAGEIOLATCH_*`

  Pro problémy vi `PAGEIOLATCH_SH`datovýsoubor (včetně , `PAGEIOLATCH_EX`), `PAGEIOLATCH_UP`).  Pokud název typu čekání obsahuje **iO** v něm, odkazuje na problém vi. Pokud není žádný **vi vi** v názvu čekání západka stránky, odkazuje na jiný typ problému (například tempdb tvrzení).

- `WRITE_LOG`

  Pro problémy vi.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Pokud problém vo dochází právě teď

Pomocí [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) nebo [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) `wait_type` zobce a `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Identifikace dat a protokolování využití vi.

Následující dotaz slouží k identifikaci dat a protokolování využití vi. Pokud je vstupně-mailem protokolu více než 80 %, znamená to, že uživatelé použili dostupné vstupně-maily pro úroveň služby SQL DB.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Pokud bylo dosaženo limitu vi, máte dvě možnosti:

- Možnost 1: Upgrade výpočetní velikosti nebo úrovně služby
- Možnost 2: Identifikujte a vyladěte dotazy, které spotřebovávají nejvíce vi.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Zobrazení vizolovaných dotazů souvisejících s vyrovnávací pamětí pomocí úložiště dotazů

Pro možnost 2 můžete použít následující dotaz proti Query Store pro vi ono související s vyrovnávací pamětí k zobrazení posledních dvou hodin sledované aktivity:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Zobrazit celkový počet io protokolu pro čekání WRITELOG

Pokud je `WRITELOG`typ čekání , použijte následující dotaz k zobrazení celkového počtu vznětových záznamů protokolu podle příkazu:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identifikovat `tempdb` problémy s výkonem

Při identifikaci problémů s výkonem vi, horní typy čekání spojené s `tempdb` problémy je `PAGELATCH_*` (ne `PAGEIOLATCH_*`). Nicméně, `PAGELATCH_*` čeká ne vždy znamenat, že máte `tempdb` sváry.  Toto čekání může také znamenat, že máte konflikty datových stránek objektu uživatele z důvodu souběžných požadavků zaměřených na stejnou datovou stránku. Chcete-li `tempdb` dále potvrdit tvrzení, použijte [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) k `2:x:y` potvrzení, `tempdb` že hodnota wait_resource `x` začíná kde 2 `y` je ID databáze, je ID souboru a je ID stránky.  

Pro konflikty tempdb je běžnou metodou snížení nebo přepsání `tempdb`kódu aplikace, který závisí na .  Mezi `tempdb` oblasti běžného použití patří:

- Dočasné tabulky
- Proměnné tabulky
- Parametry s hodnotou tabulky
- Využití úložiště verzí (konkrétně spojené s dlouho běžícími transakcemi)
- Dotazy, které mají plány dotazů, které používají řazení, spojení hash a zařazují

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Hlavní dotazy, které používají proměnné tabulky a dočasné tabulky

Následující dotaz slouží k identifikaci hlavních dotazů, které používají proměnné tabulky a dočasné tabulky:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identifikace dlouhotrvajících transakcí

Následující dotaz slouží k identifikaci dlouhotrvajících transakcí. Dlouhotrvající transakce brání vyčištění úložiště verzí.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identifikovat problémy s výkonem čekání udělit paměť

Pokud je `RESOURCE_SEMAHPORE` váš typ čekání na vrchol a nemáte problém s vysokým využitím procesoru, můžete mít problém s čekáním na udělení paměti.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Určení, `RESOURCE_SEMAHPORE` zda čekání je horní čekání

Pomocí následujícího dotazu `RESOURCE_SEMAHPORE` určete, zda čekání je horní čekání

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>Příkazy s vysokou spotřebou paměti identity

Následující dotaz slouží k identifikaci příkazů s vysokou spotřebou paměti:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Identifikace 10 nejlepších grantů na aktivní paměť

Následující dotaz slouží k identifikaci 10 nejlepších udělené aktivní paměti:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Výpočet velikosti databáze a objektů

Následující dotaz vrátí velikost databáze (v megabajtech):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Následující dotaz vrátí velikost jednotlivých objektů (v megabajtech) v databázi:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Sledování připojení

Pomocí zobrazení [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) můžete načíst informace o připojeních vytvořených na konkrétním serveru Azure SQL Database a podrobnosti o každém připojení. Kromě toho je zobrazení [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) užitečné při načítání informací o všech aktivních uživatelských připojeních a interních úlohách.
Následující dotaz načte informace o aktuálním připojení:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Při provádění zobrazení **sys.dm_exec_requests** a **sys.dm_exec_sessions**, pokud máte **oprávnění STAV DATABÁZE ZOBRAZENÍ** DATABÁZE v databázi, zobrazí se v databázi všechny spuštěné relace; v opačném případě se zobrazí pouze aktuální relace.

## <a name="monitor-resource-use"></a>Sledování využití prostředků

Využití prostředků můžete sledovat pomocí nástroje [SQL Database Query Performance Insight](sql-database-query-performance.md) a Query [Store](https://msdn.microsoft.com/library/dn817826.aspx).

Využití můžete také sledovat pomocí těchto dvou zobrazení:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

Zobrazení [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) můžete použít v každé databázi SQL. Zobrazení **sys.dm_db_resource_stats** zobrazuje data o využití zdrojů z posledních zdrojů vzhledem k úrovni služby. Průměrná procenta pro procesor, vi, zápisy protokolu a paměti jsou zaznamenávány každých 15 sekund a jsou udržovány po dobu 1 hodiny.

Vzhledem k tomu, že toto zobrazení poskytuje podrobnější pohled na využití prostředků, použijte **nejprve sys.dm_db_resource_stats** pro analýzu aktuálního stavu nebo řešení potíží. Tento dotaz například zobrazuje průměrné a maximální využití prostředků pro aktuální databázi za poslední hodinu:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Další dotazy naleznete v příkladech v [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresource_stats"></a>sys.resource_stats

Zobrazení [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) v **hlavní** databázi obsahuje další informace, které vám mohou pomoci sledovat výkon databáze SQL na konkrétní úrovni služby a velikosti výpočetních prostředků. Údaje jsou shromažďovány každých 5 minut a jsou uchovávány přibližně 14 dní. Toto zobrazení je užitečné pro dlouhodobější historickou analýzu toho, jak databáze SQL používá prostředky.

Následující graf ukazuje využití prostředků procesoru pro databázi Premium s velikostí výpočetních prostředků P2 pro každou hodinu v týdnu. Tento graf začíná v pondělí, zobrazuje 5 pracovních dnů a pak ukazuje víkend, kdy se v aplikaci stane mnohem méně.

![Použití prostředků databáze SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Z dat tato databáze má aktuálně špičkové zatížení procesoru o něco více než 50 procent využití procesoru vzhledem k velikosti výpočetní hodnoty P2 (poledne v úterý). Pokud je procesor dominantním faktorem v profilu prostředku aplikace, můžete se rozhodnout, že P2 je správná velikost výpočetních prostředků, která zaručuje, že úloha vždy vyhovuje. Pokud očekáváte, že aplikace růst v průběhu času, je vhodné mít další prostředek vyrovnávací paměti tak, aby aplikace nikdy nedosáhne limitu úrovně výkonu. Pokud zvětšíte velikost výpočetních prostředků, můžete se vyhnout chybám viditelným pro zákazníka, ke kterým může dojít, když databáze nemá dostatek energie pro efektivní zpracování požadavků, zejména v prostředích citlivých na latenci. Příkladem je databáze, která podporuje aplikaci, která maluje webové stránky na základě výsledků volání databáze.

Jiné typy aplikací mohou interpretovat stejný graf odlišně. Například pokud se aplikace pokusí zpracovat mzdová data každý den a má stejný graf, tento druh modelu "dávkové úlohy" může být v pořádku při výpočetní velikosti P1. Velikost výpočetních prostředků P1 má 100 DTU ve srovnání s 200 DTU ve výpočetní velikosti P2. Velikost výpočetních prostředků P1 poskytuje polovinu výkonu výpočetní velikosti P2. Takže 50 procent využití procesoru v P2 se rovná 100 procent využití procesoru v P1. Pokud aplikace nemá časové osy, nemusí záležet na tom, zda úloha trvá 2 hodiny nebo 2,5 hodiny na dokončení, pokud se provede dnes. Aplikace v této kategorii pravděpodobně můžete použít velikost výpočetních prostředků P1. Můžete využít skutečnosti, že existují období během dne, kdy je využití zdrojů nižší, takže jakýkoli "velký vrchol" se může přelít do jednoho z koryt později během dne. Velikost výpočetních prostředků P1 může být dobré pro tento druh aplikace (a ušetřit peníze), tak dlouho, dokud úlohy můžete dokončit včas každý den.

Azure SQL Database zveřejňuje informace o spotřebovaných prostředků pro každou aktivní databázi v zobrazení **sys.resource_stats** **hlavní** databáze na každém serveru. Data v tabulce jsou agregována v intervalech 5 minut. U úrovní služeb Basic, Standard a Premium může zobrazení dat v tabulce trvat déle než 5 minut, takže tato data jsou užitečnější pro historickou analýzu, nikoli pro analýzu téměř v reálném čase. Dotaz na zobrazení **sys.resource_stats** zobrazíte nedávnou historii databáze a ověříte, zda vybraná rezervace poskytla požadovaný výkon v případě potřeby.

> [!NOTE]
> Chcete-li se v následujících příkladech dotazovat na **soubor sys.resource_stats,** musíte být připojeni k **hlavní** databázi serveru SQL Database.

Tento příklad ukazuje, jak jsou data v tomto zobrazení vystavena:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Zobrazení katalogu sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

Následující příklad ukazuje různé způsoby, jak můžete použít zobrazení katalogu **sys.resource_stats** získat informace o tom, jak databáze SQL používá prostředky:

1. Chcete-li se podívat na použití prostředků z minulého týdne pro databázi userdb1, můžete spustit tento dotaz:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Chcete-li vyhodnotit, jak dobře vaše úloha odpovídá velikosti výpočetních prostředků, musíte přejít k podrobnostem do každého aspektu metriky prostředků: PROCESOR, čtení, zápisy, počet pracovníků a počet relací. Tady je revidovaný dotaz používající **sys.resource_stats** k vykazování průměrných a maximálních hodnot těchto metrik prostředků:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Pomocí těchto informací o průměrné a maximální hodnoty jednotlivých metrik prostředků můžete posoudit, jak dobře vaše úloha zapadá do výpočetní velikosti, kterou jste zvolili. Průměrné hodnoty ze **souboru sys.resource_stats** obvykle poskytují dobrý směrný plán pro použití proti cílové velikosti. Měla by to být vaše primární měřicí tyčinka. Například můžete používat úroveň služby Standard s výpočetní velikostí S2. Procento průměrného použití pro čtení a zápisy procesoru a vstupně-odpovědí je nižší než 40 procent, průměrný počet pracovníků je nižší než 50 a průměrný počet relací je nižší než 200. Vaše úloha se může vejít do výpočetní velikosti S1. Je snadné zjistit, zda vaše databáze zapadá do omezení pracovního procesu a relace. Chcete-li zjistit, zda databáze zapadá do nižší výpočetní velikosti s ohledem na procesor, čte a zapisuje, vydělte číslo DTU nižší výpočetní velikost číslem DTU aktuální výpočetní velikost a pak vynásobte výsledek 100:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    Výsledkem je relativní rozdíl výkonu mezi dvěma výpočetními velikostmi v procentech. Pokud vaše využití prostředků nepřekročí tuto částku, vaše úloha se může vejít do nižší výpočetní velikosti. Je však třeba podívat se na všechny rozsahy hodnoty použití prostředků a určit podle procenta, jak často by se zatížení databáze vešlo do nižší výpočetní velikosti. Následující dotaz vyvodí procento přizpůsobení na dimenzi prostředku na základě prahové hodnoty 40 procent, kterou jsme vypočítali v tomto příkladu:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Na základě vrstvy databázové služby se můžete rozhodnout, jestli vaše úloha zapadá do nižší výpočetní velikosti. Pokud je cíl úlohy databáze 99,9 % a předchozí dotaz vrátí hodnoty větší než 99,9 % pro všechny tři dimenze prostředků, vaše úloha pravděpodobně vejde do nižší výpočetní velikosti.

    Při pohledu na procento uložení také získáte přehled o tom, zda byste se měli přesunout na další vyšší výpočetní velikost, abyste splnili svůj cíl. Například userdb1 zobrazuje následující využití procesoru za poslední týden:

   | Průměrné procento procesoru | Maximální procento procesoru |
   | --- | --- |
   | 24.5 |100.00 |

    Průměrný procesor je asi čtvrtina limitu výpočetní velikosti, což by se dobře vešlo do výpočetní velikosti databáze. Ale maximální hodnota ukazuje, že databáze dosáhne limitu výpočetní velikosti. Potřebujete přejít na další vyšší výpočetní velikost? Podívejte se, kolikrát vaše úloha dosáhne 100 procent, a pak ji porovnejte s cílem úlohy databáze.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Pokud tento dotaz vrátí hodnotu menší než 99,9 procenta pro některou ze tří dimenzí prostředků, zvažte přesunutí na další vyšší výpočetní velikost nebo použijte techniky optimalizace aplikací ke snížení zatížení databáze SQL.

4. Toto cvičení také bere v úvahu plánované zvýšení pracovní zátěže v budoucnu.

U elastických fondů můžete monitorovat jednotlivé databáze ve fondu pomocí technik popsaných v této části. Můžete ale také monitorovat fond jako celek. Další informace najdete v tématu [Monitorování a správa elastického fondu](sql-database-elastic-pool-manage-portal.md).

### <a name="maximum-concurrent-requests"></a>Maximální počet souběžných požadavků

Chcete-li zobrazit počet souběžných požadavků, spusťte tento dotaz Transact-SQL v databázi SQL:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Chcete-li analyzovat zatížení místní databáze serveru SQL Server, upravte tento dotaz tak, aby filtrovat na konkrétní databázi, kterou chcete analyzovat. Například pokud máte místní databázi s názvem MyDatabase, tento dotaz Transact-SQL vrátí počet souběžných požadavků v této databázi:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Toto je jen snímek v jednom bodě v čase. Chcete-li získat lepší pochopení úlohy a požadavky na souběžné požadavky na požadavky na požadavky na požadavky na požadavky na požadavky, budete muset v průběhu času shromáždit mnoho vzorků.

### <a name="maximum-concurrent-logins"></a>Maximální počet souběžných přihlášení

Můžete analyzovat vaše uživatelské a aplikační vzory získat představu o četnosti přihlášení. Můžete také spustit reálné zatížení v testovacím prostředí a ujistěte se, že nedosahujete tohoto nebo jiných limitů, které diskutujeme v tomto článku. Neexistuje jediný dotaz nebo zobrazení dynamické správy (DMV), které by vám mohly zobrazovat počet souběžných přihlášení nebo historii.

Pokud více klientů používá stejný připojovací řetězec, služba ověří každé přihlášení. Pokud 10 uživatelů současně připojit k databázi pomocí stejnéuživatelské jméno a heslo, by bylo 10 souběžných přihlášení. Toto omezení platí pouze pro dobu trvání přihlášení a ověřování. Pokud stejný 10 uživatelů připojit k databázi postupně, počet souběžných přihlášení by nikdy větší než 1.

> [!NOTE]
> V současné době toto omezení se nevztahuje na databáze v elastických fondech.

### <a name="maximum-sessions"></a>Maximální počet relací

Chcete-li zobrazit počet aktuálních aktivních relací, spusťte tento dotaz Transact-SQL v databázi SQL:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections
```

Pokud analyzujete místní úlohu serveru SQL Server, upravte dotaz tak, aby se zaměřil na konkrétní databázi. Tento dotaz vám pomůže určit možné potřeby relace pro databázi, pokud uvažujete o jeho přesunutí do Azure SQL Database.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase'
```

Opět tyto dotazy vrátí počet bodů v čase. Pokud v průběhu času shromažďujete více vzorků, budete mít nejlepší znalosti o použití relace.

Pro analýzu databáze SQL můžete získat historické statistiky o relacích dotazem na zobrazení [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) a kontrolou **sloupce active_session_count.**

## <a name="monitoring-query-performance"></a>Sledování výkonu dotazu

Pomalé nebo dlouhotrvající dotazy mohou spotřebovat významné systémové prostředky. Tato část ukazuje, jak pomocí zobrazení dynamické správy zjistit několik běžných problémů s výkonem dotazu. Starší, ale stále užitečný odkaz pro řešení potíží je [poradce při potížích s problémy s výkonem v SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) článek na Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Hledání nejlepších dotazů N

Následující příklad vrátí informace o prvních pěti dotazech seřazené podle průměrného času procesoru. Tento příklad agreguje dotazy podle jejich hash dotazu, takže logicky ekvivalentní dotazy jsou seskupeny podle jejich kumulativní spotřeby prostředků.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Sledování blokovaných dotazů

Pomalé nebo dlouhotrvající dotazy mohou přispět k nadměrné spotřebě prostředků a být důsledkem blokovaných dotazů. Příčinou blokování může být špatný návrh aplikace, plány dotazů, nedostatek užitečných indexů a tak dále. Pomocí zobrazení sys.dm_tran_locks můžete získat informace o aktuální aktivitě uzamčení v azure sql database. Například kód, viz [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) v SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Sledování plánů dotazů

Neefektivní plán dotazu může také zvýšit spotřebu procesoru. Následující příklad používá zobrazení [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) k určení, který dotaz používá nejvíce kumulativní procesor.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Viz také

[Úvod do služby SQL Database](sql-database-technical-overview.md)
