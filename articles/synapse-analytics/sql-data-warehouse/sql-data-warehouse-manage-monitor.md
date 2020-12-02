---
title: Monitorování vyhrazené úlohy fondu SQL pomocí zobrazení dynamické správy
description: Naučte se monitorovat úlohy vyhrazeného fondu SQL ve službě Azure synapse Analytics a provádění dotazů pomocí zobrazení dynamické správy.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 1992c3d525fc1f5a098e1969887a752233d47990
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453804"
---
# <a name="monitor-your-azure-synapse-analytics-dedicated-sql-pool-workload-using-dmvs"></a>Monitorování vyhrazené úlohy fondu SQL ve službě Azure synapse Analytics pomocí zobrazení dynamické správy

Tento článek popisuje, jak pomocí zobrazení dynamické správy (zobrazení dynamické správy) monitorovat vaše úlohy, včetně zkoumání provádění dotazů ve fondu SQL.

## <a name="permissions"></a>Oprávnění

K dotazování na zobrazení dynamické správy v tomto článku potřebujete buď oprávnění **Zobrazit stav databáze** nebo **řídit** . Obecně platí, že přidělení **stavu databáze zobrazení** je preferované oprávnění, protože je mnohem více omezující.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitorování připojení

Všechna přihlášení k vašemu datovému skladu se zaprotokolují do [Sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Tento DMV obsahuje poslední 10 000 přihlášení.  Session_id je primární klíč a pro každé nové přihlášení se přiřadí sekvenčně.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitorovat provádění dotazů

Všechny dotazy spouštěné ve fondu SQL jsou protokolovány do [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Tento DMV obsahuje poslední spuštěné dotazy 10 000.  Request_id jedinečně identifikuje každý dotaz a je primárním klíčem pro tento DMV.  Request_id se každému novému dotazu přiřadí sekvenčně a je předponou QID, která představuje ID dotazu.  Dotaz na tento DMV pro daný session_id zobrazuje všechny dotazy pro dané přihlášení.

> [!NOTE]
> Uložené procedury používají více ID žádostí.  ID žádostí se přiřazují v sekvenčním pořadí.

Tady je postup pro prošetření plánů provádění dotazů a časů pro konkrétní dotaz.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>Krok 1: určení dotazu, který chcete prozkoumat

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

Z předchozích výsledků dotazu **si poznamenejte ID žádosti** o dotaz, který chcete prozkoumat.

Dotazy v **pozastaveném** stavu lze zařadit do fronty z důvodu velkého počtu aktivních spuštěných dotazů. Tyto dotazy se také zobrazí v [Sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) čekají na dotaz s typem UserConcurrencyResourceType. Informace o omezeních souběžnosti najdete v tématu [limity paměti a souběžnosti](memory-concurrency-limits.md) nebo [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md). Dotazy mohou také čekat na jiné důvody, například na zámky objektů.  Pokud dotaz čeká na prostředek, prostudujte si další informace v tomto článku v tématu [zkoumání dotazů, které čekají na prostředky](#monitor-waiting-queries) .

Chcete-li zjednodušit vyhledávání dotazů v tabulce [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , použijte [popisek](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k přiřazení komentáře k dotazu, který lze vyhledat v zobrazení sys.dm_pdw_exec_requests.

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

### <a name="step-2-investigate-the-query-plan"></a>Krok 2: prozkoumání plánu dotazů

Pomocí ID žádosti načtený plán SQL (DSQL) dotazu z [Sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Pokud plán DSQL trvá déle, než se čekalo, může být příčinou složitý plán s mnoha kroky DSQL nebo pouze jedním krokem trvat dlouhou dobu.  Pokud má plán mnoho kroků s několika operacemi přesunutí, zvažte optimalizaci distribuce vašich tabulek, aby se snížil pohyb dat. Článek [distribuce tabulek](sql-data-warehouse-tables-distribute.md) vysvětluje, proč je nutné přesunout data, aby bylo možné dotaz vyřešit. Článek také vysvětluje některé strategie distribuce pro minimalizaci přesunu dat.

Chcete-li prozkoumat další podrobnosti o jednom kroku, sloupec *operation_type* dlouhého kroku dotazu a poznamenejte si **Krok index**:

* Pokračujte krokem 3 pro **operace SQL**: operace s RemoteOperation, ReturnOperation.
* Pokračujte krokem 4 pro **operace přesunu dat**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>Krok 3: Prozkoumejte SQL pro distribuované databáze

K načtení podrobností z [Sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), které obsahují informace o spuštění kroku dotazu ve všech distribuovaných databázích, použijte ID žádosti a krokový index.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Když je spuštěný krok dotazu, můžete použít [příkaz DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení SQL Server odhadovaného plánu z mezipaměti plánu SQL Server pro krok spuštěný v konkrétní distribuci.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>Krok 4: prozkoumání přesunu dat v distribuovaných databázích

K načtení informací o kroku přesunu dat běžícímu na každé distribuci z [Sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)použijte ID žádosti a krokový index.

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Zkontrolujte sloupec *total_elapsed_time* , abyste viděli, jestli konkrétní distribuce trvá podstatně déle než jiné pro pohyb dat.
* V případě dlouhotrvající distribuce zkontrolujte sloupec *rows_processed* a zjistěte, zda je počet přesouvaných řádků z této distribuce významně větší než ostatní. Pokud ano, může toto hledání znamenat zkosení vašich podkladových dat. Jedna z příčin zkosení dat je distribuována na sloupec s mnoha hodnotami NULL (jejichž řádky budou všechny na stejné distribuci). Zabraňte pomalým dotazům tím, že se vyhnete distribuci těchto typů sloupců nebo filtrujete dotaz tak, aby v případě možné hodnoty NULL byly odstraněny. 

Pokud je dotaz spuštěný, můžete použít [příkaz DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení SQL Server odhadovaného plánu z mezipaměti plánu SQL Server pro aktuálně běžící krok SQL v rámci určité distribuce.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitorování čekajících dotazů

Pokud zjistíte, že váš dotaz neprovádí postup, protože čeká na prostředek, tady je dotaz, který zobrazuje všechny prostředky, na které dotaz čeká.

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

Pokud dotaz aktivně čeká na prostředky z jiného dotazu, bude stav **AcquireResources**.  Pokud dotaz obsahuje všechny požadované prostředky, bude **udělen** stav.

## <a name="monitor-tempdb"></a>Monitorování databáze tempdb

Databáze tempdb slouží k uchovávání průběžných výsledků během provádění dotazu. Vysoké využití databáze tempdb může vést k zpomalení výkonu dotazů. Pro každý nakonfigurovaný DW100c se přidělí 399 GB místa v databázi tempdb (DW1000c by měl 3,99 TB z celkového místa v databázi tempdb).  Níže jsou uvedené tipy pro monitorování využití databáze tempdb a snížení využití databáze tempdb ve vašich dotazech.

### <a name="monitoring-tempdb-with-views"></a>Monitorování databáze tempdb pomocí zobrazení

Chcete-li monitorovat využití databáze tempdb, nejprve nainstalujte [Microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) zobrazení z [fondu Microsoft Toolkit pro SQL](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Pak můžete spustit následující dotaz a zobrazit využití databáze tempdb na uzel pro všechny spuštěné dotazy:

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

Pokud máte dotaz, který spotřebovává velké množství paměti nebo obdržel chybovou zprávu týkající se přidělení databáze tempdb, může to být způsobeno velmi velkým [Create Table jako Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) nebo příkaz [INSERT Select](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , který se v konečné operaci přesunu dat nezdařil. To může být obvykle identifikováno jako operace ShuffleMove v plánu distribuovaného dotazu přímo před konečným výběrem vložení.  Pomocí [Sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) můžete monitorovat operace ShuffleMove.

Nejběžnějším rizikem je přerušení CTAS nebo vložení příkazu SELECT do více příkazů Load, aby datový svazek nepřesáhl počet 1 TB na uzel tempdb. Cluster můžete také škálovat na větší velikost, která bude rozšiřovat velikost databáze tempdb na více uzlech, čímž se zmenší databáze tempdb na každém jednotlivém uzlu.

Kromě CTAS a vkládání příkazů SELECT můžou velké a komplexní dotazy běžící s nedostatečnou pamětí přesahovat do databáze tempdb, což způsobuje selhání dotazů.  Zvažte spuštění s větší [třídou prostředků](resource-classes-for-workload-management.md) , abyste se vyhnuli zalití do tempdb.

## <a name="monitor-memory"></a>Monitorovat paměť

Paměť může být hlavní příčinou pomalého výkonu a potíží při nedostatku paměti. Pokud SQL Server zjistíte, že využití paměti při provádění dotazu dosáhlo svého limitu, zvažte škálování datového skladu.

Následující dotaz vrátí SQL Server využití paměti a tlak paměti na uzel:

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

## <a name="monitor-transaction-log-size"></a>Monitorování velikosti transakčního protokolu

Následující dotaz vrátí velikost transakčního protokolu při každé distribuci. Pokud jeden ze souborů protokolu dosáhne 160 GB, měli byste zvážit horizontální navýšení kapacity instance nebo omezení velikosti transakcí.

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

## <a name="monitor-transaction-log-rollback"></a>Sledování odvolání protokolu transakcí

Pokud budete pokračovat v dotazech nebo trváte delší dobu, můžete kontrolovat a monitorovat, pokud se nějaké transakce vrací zpět.

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

## <a name="monitor-polybase-load"></a>Monitorovat základní zatížení

Následující dotaz poskytuje přibližný odhad průběhu zátěže. Dotaz zobrazuje pouze soubory, které jsou právě zpracovávány.

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

Další informace o zobrazení dynamické správy najdete v tématu [Systémová zobrazení](../sql/reference-tsql-system-views.md).
