---
title: Monitorování výkonu databáze pomocí zobrazení dynamické správy SQL Azure | Dokumentace Microsoftu
description: Zjistěte, jak detekovat a diagnostikovat běžné problémy s výkonem pomocí zobrazení dynamické správy monitorovat Microsoft Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 371632a28d22583f8b206e4d8b9d2b6b4e510ab0
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563945"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Monitorování výkonu Azure SQL Database pomocí zobrazení dynamické správy

Microsoft Azure SQL Database umožňuje podmnožinu zobrazení dynamické správy pro diagnostiku problémů s výkonem, které mohou být způsobeny zablokování nebo dlouho probíhající dotazy, kritických bodů prostředků, plány nízký dotazů a tak dále. Toto téma obsahuje informace o tom, jak detekovat běžné problémy s výkonem pomocí zobrazení dynamické správy.

SQL Database podporuje částečně tři kategorie zobrazení dynamické správy:

- Zobrazení dynamické správy vztahující se k databázi.
- Zobrazení dynamické správy týkající se provádění.
- Zobrazení dynamické správy související s transakcí.

Podrobné informace o zobrazení dynamické správy, naleznete v tématu [funkcí (Transact-SQL) a zobrazení dynamické správy](https://msdn.microsoft.com/library/ms188754.aspx) v SQL Server Books Online.

## <a name="permissions"></a>Oprávnění

Ve službě SQL Database, dotazování zobrazení dynamické správy vyžaduje **VIEW DATABASE STATE** oprávnění. **VIEW DATABASE STATE** oprávnění vrátí informace o všech objektech v aktuální databázi.
Chcete-li udělit **VIEW DATABASE STATE** oprávnění ke konkrétnímu uživateli databáze, spusťte následující dotaz:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

Zobrazení dynamické správy v instanci systému SQL Server v místním, vrátí informace o stavu serveru. Vrátí informace týkající se pouze v aktuální databázi logické ve službě SQL Database.

## <a name="identify-cpu-performance-issues"></a>Identifikace problémů s výkonem procesoru

Pokud využití CPU je vyšší než 80 % dlouhou dobu, zvažte následující kroky:

### <a name="the-cpu-issue-is-occurring-now"></a>Procesor k problému dochází nyní

Pokud se teď k problému dochází, existují dva možné scénáře:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Mnoho jednotlivých dotazů, které využívají kumulativně vysoké využití procesoru

Použijte tento dotaz k identifikaci hodnoty hash dotazu top:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM(SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
     FROM(SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
               CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
     GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Stále běží dlouho běžící dotazy, které využívají procesor

Použijte tento dotaz k identifikaci tyto dotazy:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
     CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>V minulosti došlo k potížím procesoru

Pokud k problému došlo v minulosti a vy chcete root analýzu příčin, použijte [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Uživatelé s přístupem k databázi můžete použít T-SQL k dotazování dat. Query Store.  Výchozí konfigurace Query Store použijte členitosti 1 hodina.  Podívejte se na aktivitu pro dotazy využívající vysoké využití procesoru pomocí následujícího dotazu. Tento dotaz vrátí nejvyšší 15 náročné dotazy procesoru.  Nezapomeňte změnit `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`:

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

Jakmile identifikujete problém dotazy, je čas vyladit tyto dotazy ke snížení využití procesoru.  Pokud nemáte čas optimalizovat dotazy, můžete také upgradovat cíle na úrovni služby databáze pro tento problém obejít.

## <a name="identify-io-performance-issues"></a>Identifikace problémů s výkonem vstupně-výstupních operací

Při identifikaci problémů s výkonem vstupně-výstupních operací, začátek čekání typy související s problémy vstupně-výstupní operace jsou:

- `PAGEIOLATCH_*`

  Pro problémy s vstupně-výstupní operace souboru dat (včetně `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Pokud má název typu čekání **vstupně-výstupních operací** , odkazuje na vstupně-výstupní operace problém. Pokud není žádný **vstupně-výstupních operací** v názvu čekání západky překročila stránky odkazuje na jiný typ problému (například databáze tempdb kolize).

- `WRITE_LOG`

  Pro problémy vstupně-výstupních operací protokolu transakcí.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Pokud se vstupně-výstupní operace k problému dochází hned teď

Použití [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) nebo [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) zobrazíte `wait_type` a `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Identifikaci dat a protokolování využití vstupně-výstupních operací

Použijte tento dotaz k identifikaci dat a protokolování využití vstupně-výstupních operací. Pokud vstupně-výstupní operace dat či protokolu je vyšší než 80 %, znamená to, že uživatelé použili k dispozici vstupně-výstupních operací pro danou vrstvu služeb SQL Database.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Pokud byl dosažen limit vstupně-výstupních operací, máte dvě možnosti:

- Option 1: Upgrade velikosti výpočetního nebo úroveň služby
- Option 2: Identifikujte a vyladění dotazů využívání většina vstupně-výstupních operací.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Zobrazit související vyrovnávací paměti vstupně-výstupních operací pomocí Query Store

Možnost 2 můžete použít následující dotaz proti Query Store pro vstupně-výstupní operace související s vyrovnávací paměti k zobrazení poslední dvě hodiny sledované aktivity:

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

#### <a name="view-total-log-io-for-writelog-waits"></a>Zobrazit protokol celkový počet vstupně-výstupních operací pro WRITELOG čeká

Pokud je typ čekání `WRITELOG`, použijte tento dotaz na Zobrazit celkový počet protokolovacích v/v – příkaz:

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

## <a name="identify-tempdb-performance-issues"></a>Identifikujte `tempdb` problémy s výkonem

Při identifikaci problémů s výkonem vstupně-výstupních operací, horní počkejte typy přidružené ke `tempdb` problémů `PAGELATCH_*` (ne `PAGEIOLATCH_*`). Ale `PAGELATCH_*` čeká vždy neznamená, že máte `tempdb` kolize.  Tato čekací může také znamenat, že máte kolize stránky dat objektu uživatele z důvodu souběžných žádostí, které cílí na stejné stránce data. Pro další potvrzení `tempdb` kolize, použijte [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) potvrďte, že hodnota wait_resource začíná `2:x:y` kde 2 je `tempdb` je id databáze `x` je id souboru a `y` je id stránky.  

Pro databázi tempdb spor běžnou metodou je ke snížení nebo znovu napsat kód aplikace, která závisí na `tempdb`.  Běžné `tempdb` využití oblasti patří:

- Dočasné tabulky
- Tabulka proměnných
- Parametry vracející tabulku
- Využití úložiště verze (konkrétně přidružené dlouho trvající transakcí)
- Dotazy, které mají plány dotazů, které používají řazení, hodnota hash spojení a zařazování

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Nejčastější dotazy, které používají proměnné tabulky a dočasné tabulky

Nejčastější dotazy, které používají proměnné tabulky a dočasné tabulky pomocí následujícího dotazu:

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

### <a name="identify-long-running-transactions"></a>Identifikujte dlouhotrvající transakce

Pomocí následujícího dotazu k identifikaci dlouho spuštěný transakce. Dlouhotrvající transakce zabránit čištění úložiště verzí.

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

## <a name="identify-memory-grant-wait-performance-issues"></a>Identifikujte paměti udělení počkejte problémy s výkonem

Je-li nejvyšší počkejte typ `RESOURCE_SEMAHPORE` a nemáte problém s vysoké využití procesoru, bude pravděpodobně paměti udělit problém čekání.

### <a name="determine-if-a-resourcesemahpore-wait-is-a-top-wait"></a>Určí, zda `RESOURCE_SEMAHPORE` Počkejte, že se začátek čekání

Pomocí následujícího dotazu určete, jestli `RESOURCE_SEMAHPORE` Počkejte, že se začátek čekání

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

### <a name="identity-high-memory-consuming-statements"></a>Příkazy identity vysoké spotřeby paměti

Použijte tento dotaz k identifikaci vysoké spotřeby paměti příkazy:

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

### <a name="identify-the-top-10-active-memory-grants"></a>Identifikujte uděluje aktivní paměti prvních 10

Použijte tento dotaz k identifikaci nejvyšší 10 uděluje aktivní paměti:

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

## <a name="calculating-database-and-objects-sizes"></a>Výpočet velikosti databáze a objekty

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

## <a name="monitoring-connections"></a>Monitorování připojení

Můžete použít [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) zobrazení k načtení informací o připojení pro konkrétní server Azure SQL Database a podrobnosti o každé připojení. Kromě toho [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) zobrazení je užitečné při načítání informací o všech aktivních uživatelských připojení a interních úlohách.
Následující dotaz načte informace o aktuální připojení:

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
> Při provádění **sys.dm_exec_requests** a **zobrazení sys.dm_exec_sessions**, pokud máte **VIEW DATABASE STATE** oprávnění v databázi, se zobrazí všechna spuštění relace v databázi. v opačném případě se zobrazí pouze pro aktuální relaci.

## <a name="monitor-resource-use"></a>Monitorovat využití prostředků

Můžete monitorovat využití prostředků pomocí [SQL Database Query Performance Insight](sql-database-query-performance.md) a [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Můžete také sledovat využití pomocí těchto dvou zobrazení:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats

Můžete použít [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) zobrazení v každé databázi SQL. **Sys.dm_db_resource_stats** zobrazení ukazuje poslední data použití prostředků relativně vzhledem k úrovni služeb. Průměrné procento CPU, datové v/v, zápisy protokolu a paměti se zaznamenávají každých 15 sekund a dobu 1 hodiny se zachovají.

Protože toto zobrazení nabízí podrobnější pohled na využití prostředků, použijte **sys.dm_db_resource_stats** první pro analýzu aktuální stav nebo řešení potíží. Například tento dotaz ukazuje průměrnou a maximální prostředky používané pro aktuální databázi za poslední hodinu:

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

Pro další dotazy, podívejte se na příklady v [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats

[Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) zobrazit **hlavní** databáze obsahuje další informace, které vám může pomoct monitorování výkonu pro SQL database na úroveň konkrétních služby a vypočítat velikost. Data se shromažďují pro každých 5 minut a je zachován z důvodu přibližně 14 dnů. Toto zobrazení je užitečné pro dlouhodobější analýzu historie jak vaši službu SQL database využívá prostředky.

Následující graf ukazuje využití procesoru využití prostředků databáze úrovně Premium s velikostí výpočetních P2 pro každou hodinu za týden. Tento graf začíná v pondělí, zobrazuje 5 pracovních dní a poté zobrazí víkend, když se v aplikaci stane mnohem méně.

![Využití prostředků databáze SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Z dat, tato databáze má momentálně zatížení CPU ve špičce než 50 procent využití procesoru vzhledem k P2 vypočítat velikost (v poledne úterý). Pokud využití procesoru je dominantní faktoru v profilu aplikace prostředků, může rozhodnout, že P2 má velikost správných výpočetních zaručí, že zatížení vždy vyhovovat. Pokud očekáváte, že aplikace čase, je dobré mít vyrovnávací paměť dodatečný prostředek, takže aplikace nebude nikdy dosáhne limitu úroveň výkonu. Zákazník viditelné chyby, které mohou nastat, když databáze nemá dostatek výkonné nástroje pro zpracování žádostí efektivně, zejména v prostředích nízkou latenci můžete vyhnout zvýšíte výpočetního prostředí. Příkladem je databáze, která podporuje aplikace, která jsou vykreslovány webové stránky na základě výsledků volání databáze.

Ostatní typy aplikací může do stejného grafu interpretují odlišně. Například pokud se aplikace pokusí ke zpracování mezd dat každý den a má stejný graf, tento druh "úlohy služby batch" model může provádět bez problémů ve velikosti výpočetní prostředky P1. Velikost výpočetní prostředky P1 má 100 Dtu ve srovnání s 200 jednotek Dtu na P2 vypočítat velikost. Velikost výpočetní prostředky P1 poskytuje že poloviční výkonu P2 výpočetní velikost. Tedy 50 % využití procesoru v P2 se rovná 100 % využití procesoru v P1. Pokud aplikace nemá žádné vypršení časových limitů, nemusí je důležité, pokud úlohy trvá 2 hodiny nebo 2,5 hodin, pokud to proběhne ještě dnes. Aplikace v této kategorii pravděpodobně můžete použít velikost výpočetní prostředky P1. Můžete využít výhod vzhledem k tomu, že mají dobu během dne, kdy je využití prostředků nižší, tak, aby všechny "velký objem ve špičce" může být uložená do jedné ze žlaby později za den. Velikost výpočetní prostředky P1 může být vhodné pro tento typ aplikace (a ušetřit peníze) jako včasné každý den může dokončení úloh.

Azure SQL Database zpřístupňuje využívat informace o prostředku pro každou databázi aktivní **sys.resource_stats** zobrazení **hlavní** databází v každém serveru. Zobrazují se data v tabulce pro 5minutových intervalech. S úrovní služeb Basic, Standard a Premium dat může trvat více než 5 minut se zobrazí v tabulce, takže tato data je užitečnější pro historické analýzy spíše než analýzy téměř v reálném čase. Dotaz **sys.resource_stats** zobrazení nedávné historie databáze a ověřit, jestli se rezervace jste zvolili doručit výkonu chcete, aby v případě potřeby.

> [!NOTE]
> Musíte být připojeni k **hlavní** databázi vašeho serveru databáze SQL k dotazování **sys.resource_stats** v následujících příkladech.

Tento příklad ukazuje, jak vystavit data v tomto zobrazení:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Zobrazení katalogu sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

Další příklad ukazuje různé způsoby, které můžete použít **sys.resource_stats** katalogu zobrazení se získat informace o tom, jak vaši službu SQL database používá prostředky:

1. Podívat se na poslední týden prostředků použijte pro userdb1 databáze, můžete spustit tento dotaz:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Můžete vyhodnotit, jak dobře vaše úloha vyhovuje výpočetního prostředí, budete muset přejít na každý aspekt metriky prostředků: Využití procesoru, čtení, zápisu, počet pracovních procesů a počet relací. Tady je upravená dotazování pomocí **sys.resource_stats** hlášení průměrné a maximální hodnoty tyto metriky prostředků:

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

3. Pomocí těchto informací o průměrné a maximální hodnoty každého prostředku metriky můžete vyhodnotit, jak dobře vaše úloha zapadá do výpočetní velikost, kterou jste zvolili. Obvykle, průměrné hodnoty z **sys.resource_stats** umožňují dobré směrný plán použít proti cílovou velikost. Měla by být stonek vaše primární měření. Příklad může být pomocí úrovně služeb Standard S2 výpočty velikosti. Průměr použít procenta využití procesoru a vstupně-výstupních operací čtení a zápisy jsou pod 40 procent, průměrný počet pracovních procesů, které je nižší než 50 a průměrný počet relací je nižší než 200. Vaše zatížení může vešel do výpočetní S1. Je snadno zjistit, zda databáze vejde omezení pracovních procesů a relace. Abyste viděli, zda databáze zapadá do menší velikost výpočetního s ohledem na dělení procesoru, čtení a zápisy, počet DTU nižší vypočítat velikost podle počtu jednotek DTU vaše aktuální velikost výpočetních a výsledek vynásobit 100:

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    Výsledkem je relativní výkon rozdíl mezi těmito dvěma compute velikostí v procentech. Pokud využívání prostředků není překročí tuto hodnotu, může vaše úlohy vešel do nižší výpočetní prostředky. Ale budete muset podívejte se na všechny rozsahy hodnot použití prostředků a zjistit, v procentech, jak často by vaší úlohy databáze vešel do nižší výpočetní prostředky. Následující dotaz vypíše ve výstupu přizpůsobit procento prostředků dimenze, podle prahové hodnoty 40 procent vypočtené v tomto příkladu:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Podle vaší úrovně služby databáze, můžete rozhodnout, jestli vaše úloha zapadá do menší velikost výpočetní prostředky. Pokud je váš cíl úlohy databáze 99, 9procentní a předchozí dotaz vrací hodnoty větší než 99, 9procentní pro všechny tři prostředků dimenze, úloha pravděpodobně zapadá do nižší výpočty velikosti.

    Prohlížení přizpůsobit procento poskytuje také přehled o tom, zda byste měli přejít na další vyšší velikost výpočetní prostředky pro splnění cíle. Například userdb1 znázorňuje následující využití procesoru za minulý týden:

   | Průměrné procento CPU | Maximální procento využití procesoru |
   | --- | --- |
   | 24.5 |100.00 |

    Průměrné využití procesoru je o čtvrtletí limit velikosti výpočetní prostředky, které bude vyhovovat výpočty velikosti databáze. Ale maximální hodnota ukazuje, že databáze dosáhne limitu výpočetního prostředí. Je třeba přesunout na nejbližší větší velikost výpočetních? Podívejte se na postup v mnoha případech vaše zatížení dosáhne 100 % jeho obsahu a porovnejte je vaším cílem zatížení databáze.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Pokud tento dotaz vrací hodnotu menší než 99, 9procentní pro libovolný počet rozměrů tři prostředků zvažte přesunutí na nejbližší větší velikost výpočetních nebo používat techniky ladění aplikace ke snížení zatížení na databázi SQL.

4. V tomto cvičení také bere v úvahu vaše zvýšení plánované úlohy v budoucnu.

U elastických fondů můžete monitorovat jednotlivé databáze ve fondu pomocí technik popsaných v této části. Můžete ale také monitorovat fond jako celek. Další informace najdete v tématu [Monitorování a správa elastického fondu](sql-database-elastic-pool-manage-portal.md).

### <a name="maximum-concurrent-requests"></a>Maximální počet souběžných požadavků

Pokud chcete zobrazit počet souběžných požadavků, spusťte tento dotaz jazyka Transact-SQL v SQL database:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R;
    ```

Pokud chcete analyzovat úlohy k místní databázi SQL serveru, upravte tento dotaz pro filtrování v konkrétní databázi, že který chcete analyzovat. Pokud máte místní databázi s názvem databáze, tento dotaz jazyka Transact-SQL vrátí počet souběžných požadavků v této databázi:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase';
    ```

Toto je snímek v jednom okamžiku v čase. Pokud chcete získat lepší přehled o vašich úloh a požadavky souběžný požadavek, musíte postupně shromáždit mnoha ukázek.

### <a name="maximum-concurrent-logins"></a>Maximální souběžných přihlášení

Můžete analyzovat vzory vašich uživatelů a aplikací, kde získáte představu o četnosti přihlášení. Reálné zátěže můžete spustit také v testovacím prostředí, abyste měli jistotu, že jste nedosahují to nebo další omezení, které si popíšeme v tomto článku. Není k dispozici jeden dotaz nebo zobrazení dynamické správy (DMV), které můžete zobrazit souběžných počítá přihlášení nebo z historie.

Pokud více klientů používat stejný připojovací řetězec, služba se ověřuje každé přihlášení. Pokud 10 uživatelů současně připojit k databázi pomocí stejného uživatelského jména a hesla, by existovat 10 souběžných přihlášení. Toto omezení platí pouze pro dobu trvání přihlášení a ověření. Pokud se stejný 10 uživatelů připojit k databázi postupně, počet souběžných přihlášení by nikdy být větší než 1.

> [!NOTE]
> V současné době toto omezení se nevztahuje na databází v elastických fondech.

### <a name="maximum-sessions"></a>Maximální počet relací

Pokud chcete zobrazit počet aktuálních aktivních relací, spusťte tento dotaz jazyka Transact-SQL v SQL database:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Pokud analyzujete pracovního vytížení místní SQL Server, tento dotaz změnit a zaměřte se na konkrétní databáze. Tento dotaz vám pomůže určit, potřeb možné relace pro databázi Pokud zvažujete Přesun do Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Tyto dotazy znovu, vrátí počet bodu v čase. Budete-li shromažďovat víc ukázky v čase, budete mít nejlepší znalost relaci použít.

Pro účely analýzy SQL Database, můžete získat historické statistiky na relace pomocí dotazu [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) zobrazení a kontrola **active_session_count** sloupce.

## <a name="monitoring-query-performance"></a>Sledování výkonu dotazů

Pomalé nebo dlouho probíhající dotazy může spotřebovat významné systémových prostředků. Tato část ukazuje, jak detekovat několik běžných problémech s výkonem dotazů pomocí zobrazení dynamické správy. Odkaz na starší, ale přesto užitečné při řešení potíží, je [řešení potíží s problémy s výkonem v systému SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) článek na Microsoft TechNetu.

### <a name="finding-top-n-queries"></a>Hledání dotazy horních N

Následující příklad vrátí informace o prvních pěti dotazech seřazených podle průměrné doby využití procesoru. V tomto příkladu agreguje dotazy podle jejich hodnota hash dotazu tak, aby logicky ekvivalentní dotazy jsou seskupeny podle jejich spotřeba kumulativní prostředků.

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

### <a name="monitoring-blocked-queries"></a>Monitorování blokované dotazy

Pomalá nebo dlouho probíhající dotazy mohou přispět ke všimnete nadměrné spotřeby prostředků a možné důsledků blokované dotazy. Příčinu zablokování může být nekvalitní návrh aplikací, plány chybných dotazů, nedostatek vhodné indexy a tak dále. Zobrazení sys.dm_tran_locks můžete získat informace o aktuální uzamčení aktivity ve službě Azure SQL Database. Příklad kódu naleznete v tématu [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) v SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Monitorování plány dotazů

Plán dotazu neefektivní také může zvýšit využití procesoru. V následujícím příkladu [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) zobrazení a určit, který dotaz používá většina kumulativní využití procesoru.

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

## <a name="see-also"></a>Další informace najdete v tématech

[Úvod do služby SQL Database](sql-database-technical-overview.md)
