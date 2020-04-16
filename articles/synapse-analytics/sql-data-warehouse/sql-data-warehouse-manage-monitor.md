---
title: Sledování úlohy fondu SQL pomocí zařízení DMV
description: Zjistěte, jak sledovat úlohu fondu SQL Azure Synapse Analytics a spouštění dotazů pomocí dmv.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 5360d91a17a7eee2dd0373ac311c79d22e085939
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416086"
---
# <a name="monitor-your-azure-synapse-analytics-sql-pool-workload-using-dmvs"></a>Sledování úlohy fondu SQL Azure Synapse Analytics pomocí digitálních vozidl

Tento článek popisuje, jak pomocí zobrazení dynamické správy (DMVs) sledovat vaše úlohy, včetně zkoumání provádění dotazů ve fondu SQL.

## <a name="permissions"></a>Oprávnění

Chcete-li dotaz DMVs v tomto článku, budete potřebovat **buď ZOBRAZIT STAV DATABÁZE** nebo **ŘÍZENÍ** oprávnění. Udělení stavu **databáze zobrazení** je obvykle upřednostňované oprávnění, protože je mnohem více omezující.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Sledování připojení

Všechna přihlášení do datového skladu jsou zaznamenána na [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Toto DMV obsahuje posledních 10 000 přihlášení.  Session_id je primární klíč a je přiřazen postupně pro každé nové přihlášení.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Sledování provádění dotazů

Všechny dotazy spuštěné ve fondu SQL jsou protokolovány do [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Tento DMV obsahuje posledních 10 000 provedených dotazů.  Request_id jednoznačně identifikuje každý dotaz a je primární klíč pro tento DMV.  request_id je přiřazena postupně pro každý nový dotaz a je předponou qid, což znamená ID dotazu.  Dotazování tohoto DMV pro danou session_id zobrazí všechny dotazy pro dané přihlášení.

> [!NOTE]
> Uložené procedury používají více ID požadavků.  ID požadavku jsou přiřazovány v sekvenčním pořadí.

Zde jsou kroky, které je třeba postupovat pro zkoumání plánů spuštění dotazu a časů pro konkrétní dotaz.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>KROK 1: Identifikujte dotaz, který chcete prozkoumat

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

Z předchozích výsledků dotazu **si poznamenejte ID požadavku** dotazu, který chcete prozkoumat.

Dotazy v **pozastaveném** stavu mohou být zařazeny do fronty z důvodu velkého počtu aktivních spuštěných dotazů. Tyto dotazy se také zobrazí v [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) čeká dotaz s typem UserConcurrencyResourceType. Informace o omezení souběžnosti naleznete v [tématu Omezení paměti a souběžnosti](memory-concurrency-limits.md) nebo [Třídy prostředků pro správu úloh](resource-classes-for-workload-management.md). Dotazy můžete také čekat na jiné důvody, například pro uzamčení objektu.  Pokud váš dotaz čeká na prostředek, naleznete [v tématu Zkoumání dotazů čekání na prostředky](#monitor-waiting-queries) dále v tomto článku.

Chcete-li zjednodušit vyhledávání dotazu v tabulce [sys.dm_pdw_exec_requests,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [LABEL](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) přiřaďte k dotazu komentář, který lze vyhledat v zobrazení sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>KROK 2: Prozkoumání plánu dotazů

ID požadavku použijte k načtení plánu distribuovaného SQL (DSQL) dotazu ze [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Když plán DSQL trvá déle, než se očekávalo, příčinou může být komplexní plán s mnoha kroky DSQL nebo jen jeden krok, který trvá dlouhou dobu.  Pokud je plán mnoho kroků s několika operacemi přesunutí, zvažte optimalizaci rozložení tabulek, abyste snížili přesun dat. Článek [distribuce tabulky](sql-data-warehouse-tables-distribute.md) vysvětluje, proč musí být data přesunuta k vyřešení dotazu. Článek také vysvětluje některé distribuční strategie pro minimalizaci pohybu dat.

Chcete-li prozkoumat další podrobnosti o jednom kroku, *sloupec operation_type* dlouho běžící ho kroku dotazu a poznamenejte si **index kroku**:

* Pokračujte krokem 3a pro **operace SQL:** OnOperation, RemoteOperation, ReturnOperation.
* Pokračujte krokem 3b pro **operace přesunu dat**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>KROK 3: Prozkoumání sql v distribuovaných databázích

Pomocí ID požadavku a indexu kroku načtěte podrobnosti ze [souboru sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), který obsahuje informace o spuštění kroku dotazu ve všech distribuovaných databázích.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Při spuštění kroku dotazu [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) lze použít k načtení plánu odhadovaný sql server z mezipaměti plánu serveru SQL Server pro krok spuštěný na konkrétní distribuci.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>KROK 4: Prozkoumání pohybu dat v distribuovaných databázích

Pomocí ID požadavku a indexu kroku načtěte informace o kroku přesunu dat spuštěných v každé distribuci ze [souboru sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Zkontrolujte *sloupec total_elapsed_time* a zjistěte, zda určitý rozdělení trvá výrazně déle než ostatní pro přesun dat.
* Pro dlouhotrvající distribuce zkontrolujte *sloupec rows_processed* a zjistěte, zda je počet řádků přesunutých z této distribuce výrazně větší než ostatní. Pokud ano, toto zjištění může znamenat zkosení podkladových dat.

Pokud je dotaz spuštěn, můžete použít [dbcc PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) načíst plán odhadovaný sql server z mezipaměti plánu serveru SQL Server pro aktuálně spuštěný krok SQL v rámci určité distribuce.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Sledovat čekající dotazy

Pokud zjistíte, že dotaz nepostupuje, protože čeká na prostředek, je zde dotaz, který zobrazuje všechny prostředky, na které dotaz čeká.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Pokud dotaz aktivně čeká na prostředky z jiného dotazu, pak stav bude **AcquireResources**.  Pokud dotaz obsahuje všechny požadované prostředky, bude stav **udělen**.

## <a name="monitor-tempdb"></a>Monitor tempdb

Tempdb se používá k uložení průběžné výsledky během provádění dotazu. Vysoké využití databáze tempdb může vést ke snížení výkonu dotazu. Pro každý dw100c nakonfigurován, 399 GB prostoru tempdb je přidělena (DW1000c bude mít 3,99 TB celkového prostoru tempdb).  Níže jsou uvedeny tipy pro sledování využití tempdb a pro snížení využití tempdb ve vašich dotazech.

### <a name="monitoring-tempdb-with-views"></a>Sledování databáze tempdb pomocí zobrazení

Chcete-li sledovat využití databáze tempdb, nainstalujte nejprve zobrazení [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) z [fondu nástrojů microsoft toolkit pro sql](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Potom můžete spustit následující dotaz zobrazíte využití tempdb na uzel pro všechny spuštěné dotazy:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Pokud máte dotaz, který spotřebovává velké množství paměti nebo obdrželi chybovou zprávu týkající se přidělení tempdb, může to být způsobeno velmi velké [vytvořit tabulku jako SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) nebo INSERT [SELECT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) příkaz spuštěn, který selhává v konečné operace přesundat dat. To lze obvykle identifikovat jako operaci ShuffleMove v plánu distribuovaných dotazů těsně před konečným INSERT SELECT.  Ke sledování operací ShuffleMove použijte [sys.dm_pdw_request_steps.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Nejběžnější zmírnění je přerušit ctas nebo INSERT SELECT prohlášení do více příkazů zatížení, takže svazek dat nepřekročí limit tempdb 1TB na uzel. Můžete také škálovat clusteru na větší velikost, která bude šířit velikost tempdb přes více uzlů snížení tempdb na každém jednotlivém uzlu.

Kromě ctas a INSERT SELECT příkazy velké, složité dotazy spuštěné s nedostatkem paměti může přelít do tempdb způsobuje dotazy k selhání.  Zvažte spuštění s větší [třídou prostředků,](resource-classes-for-workload-management.md) abyste se vyhnuli rozlití do databáze tempdb.

## <a name="monitor-memory"></a>Paměť monitoru

Paměť může být hlavní příčinou pro pomalý výkon a problémy s pamětí. Zvažte škálování datového skladu, pokud zjistíte, že využití paměti serveru SQL Server dosahuje svých limitů během provádění dotazu.

Následující dotaz vrátí využití paměti serveru SQL Server a tlak paměti na uzel:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>Sledování velikosti transakčního protokolu

Následující dotaz vrátí velikost transakčního protokolu v každé distribuci. Pokud jeden ze souborů protokolu dosahuje 160 GB, měli byste zvážit škálování na výšinou velikost instance nebo omezení velikosti transakce.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Sledování vrácení protokolu transakcí

Pokud vaše dotazy selhávají nebo trvá dlouhou dobu pokračovat, můžete zkontrolovat a sledovat, zda máte všechny transakce vrácení zpět.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>Monitor Ovat zatížení PolyBase

Následující dotaz poskytuje přibližný odhad průběhu zatížení. Dotaz zobrazuje pouze aktuálně zpracovávané soubory.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Další kroky

Další informace o dmvs, naleznete [v tématu systémová zobrazení](../sql/reference-tsql-system-views.md).
