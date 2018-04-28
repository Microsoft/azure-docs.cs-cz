---
title: Monitorování úlohy pomocí zobrazení dynamické správy | Microsoft Docs
description: Naučte se monitorovat pomocí zobrazení dynamické správy úlohy.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 887fa4b9f950531438986269d041189d45cdecb2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="monitor-your-workload-using-dmvs"></a>Monitorování vaší úlohy pomocí DMV
Tento článek popisuje, jak sledovat vaše úlohy pomocí zobrazení dynamické správy (zobrazení dynamické správy). To zahrnuje příčin provádění dotazů v Azure SQL Data Warehouse.

## <a name="permissions"></a>Oprávnění
Dotaz zobrazení dynamické správy v tomto článku, musíte stav zobrazení databáze nebo řízení oprávnění. Stav databáze poskytující zobrazení obvykle je upřednostňovaný oprávnění, protože to je mnohem víc omezující.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitorování připojení
Všechny přihlášení k SQL Data Warehouse jsou zaznamenány do [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Tato DMV obsahuje posledních 10 000 přihlášení.  Session_id je primární klíč a je přiřazen postupně pro každou novou přihlášení.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Při provádění dotazu monitorování
Všechny dotazy spouštěné v SQL Data Warehouse jsou zaznamenány do [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Tato DMV obsahuje posledních 10 000 dotazy spouštěné.  Request_id jednoznačně identifikuje každý dotaz a je primární klíč pro tento DMV.  Request_id postupně přiřazen při každém novém dotazu a je s předponou QID, který zastupuje ID dotazu.  Dotaz na tento DMV pro danou session_id uvedeny všechny dotazy pro danou přihlášení.

> [!NOTE]
> Uložené procedury použít víc ID požadavku.  ID požadavku přiřazené postupně. 
> 
> 

Tady jsou kroků k prozkoumání plány provádění dotazů a časy pro konkrétní dotaz.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>Krok 1: Identifikace dotaz, který chcete prozkoumat
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

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Z předchozí výsledky dotazu **si poznamenejte ID žádosti o** dotazu, který chcete prozkoumat.

Dotazy v **pozastaveno** stavu jsou zařazena do fronty z důvodu omezení souběžnosti. Tyto dotazy se zobrazí také v dotazu počká sys.dm_pdw_waits s typem UserConcurrencyResourceType. Informace o limitech souběžnosti najdete v tématu [úrovně výkonu](performance-tiers.md) nebo [třídy prostředků pro úlohy správy](resource-classes-for-workload-management.md). Dotazy můžete taky počkat z jiných důvodů, jako pro zámek objektu.  Pokud váš dotaz je čeká na prostředek, přečtěte si téma [příčin dotazy čekání na prostředky] [ Investigating queries waiting for resources] další dolů v tomto článku.

Pro zjednodušení vyhledávací dotaz v tabulce sys.dm_pdw_exec_requests, použijte [popisek] [ LABEL] přiřadit váš dotaz, který lze vyhledávat v zobrazení sys.dm_pdw_exec_requests komentář.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>Krok 2: Prozkoumat plán dotazu
Umožňuje načíst distribuované plán SQL (DSQL) dotazu z ID požadavku [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Pokud plán DSQL trvá déle, než se očekávalo, příčinou může být komplexní plán s mnoho kroků DSQL nebo jenom jeden krok trvá příliš dlouho.  Je-li plán mnoho kroků s několika operace přesunutí, zvažte optimalizaci vaše tabulky distribuce omezit přesun dat. [Distribuce tabulky] [ Table distribution] článek vysvětluje, proč k vyřešení dotazu je třeba přesunout data a vysvětluje některé distribuční strategie, chcete-li minimalizovat přesun dat.

K prozkoumání další podrobnosti o jeden krok, *operation_type* sloupec dlouho běžící krok dotazu a Poznámka **krok Index**:

* Pokračujte krok 3a pro **operace SQL**: OnOperation, RemoteOperation, ReturnOperation.
* Pokračujte krok 3b pro **operace přesunu dat**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>KROK 3a: prozkoumat na distribuované databáze SQL
Umožňuje načíst z podrobností o ID žádosti a Index krok [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], který obsahuje informace o provádění kroku dotazu na všechny distribuované databáze.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Po spuštění dotazu krok [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] slouží k načtení odhadované plánu systému SQL Server z mezipaměti plánu systému SQL Server pro krok, spuštění na konkrétní distribuční.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>Krok 3b: prozkoumat přesun dat v distribuované databáze
Použít ID žádosti a Index krok k načtení informací o krok přesun dat spuštěná v každém distribučním z [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Zkontrolujte *total_elapsed_time* zobrazíte, když konkrétní distribuční trvá výrazně delší než jiné pro přesun dat.
* Pro dlouhodobé distribuci, zkontrolujte *rows_processed* zobrazíte, pokud počet řádků přesouvaných z příslušné distribuci je podstatně větší než jiné. Pokud ano, může znamenat toto zjištění zkosení podkladová data.

Pokud dotaz běží, [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] slouží k načtení odhadované plánu systému SQL Server z mezipaměti plánu SQL serveru pro SQL kroku aktuálně spuštěného v rámci konkrétní distribuce.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitorování čekání na dotazy
Pokud zjistíte, že dotazu nepostupuje vpřed, protože se čeká na prostředek, tady je dotaz, který zobrazuje všechny prostředky, které že se čeká na dotazu.

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

Pokud dotaz aktivně čeká na prostředky z jiného dotazu, pak bude stav **AcquireResources**.  Pokud dotaz obsahuje všechny požadované prostředky, pak bude stav **udělit**.

## <a name="monitor-tempdb"></a>Databáze tempdb monitorování
Databáze tempdb vysoké využití může být hlavní příčinou nízký výkon a mimo problémy s pamětí. Vezměte v úvahu škálování datového skladu, pokud zjistíte, tempdb dosažení jeho omezení během provádění dotazu. Následující informace popisují, jak identifikovat využití databáze tempdb na jeden dotaz na každém uzlu. 

Vytvořte následující zobrazení přidružit ID odpovídající uzlu pro sys.dm_pdw_sql_requests. S ID uzlu vám umožní použít jiné průchozí zobrazení dynamické správy a zapojit tyto tabulky s sys.dm_pdw_sql_requests.

```sql
-- sys.dm_pdw_sql_requests with the correct node id
CREATE VIEW sql_requests AS
(SELECT
       sr.request_id,
       sr.step_index,
       (CASE 
              WHEN (sr.distribution_id = -1 ) THEN 
              (SELECT pdw_node_id FROM sys.dm_pdw_nodes WHERE type = 'CONTROL') 
              ELSE d.pdw_node_id END) AS pdw_node_id,
       sr.distribution_id,
       sr.status,
       sr.error_id,
       sr.start_time,
       sr.end_time,
       sr.total_elapsed_time,
       sr.row_count,
       sr.spid,
       sr.command
FROM sys.pdw_distributions AS d
RIGHT JOIN sys.dm_pdw_sql_requests AS sr ON d.distribution_id = sr.distribution_id)
```
Chcete-li monitorovat databáze tempdb, spusťte následující dotaz:

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
    INNER JOIN sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```
## <a name="monitor-memory"></a>Sledování paměti

Paměť může být hlavní příčinou nízký výkon a mimo problémy s pamětí. Zvažte, pokud zjistíte, využití paměti systému SQL Server během provádění dotazu dosažení jeho omezení škálování datového skladu.

Následující dotaz vrátí přetížení využití a paměti paměti systému SQL Server na každém uzlu:   
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
## <a name="monitor-transaction-log-size"></a>Velikost protokolu transakcí monitorování
Následující dotaz vrátí velikost protokolu transakcí v každém distribučním. Pokud jeden ze souborů protokolu dosahuje 160 GB, měli byste zvážit vertikálním navýšení kapacity instanci nebo omezíte velikost vašeho transakce. 
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
## <a name="monitor-transaction-log-rollback"></a>Monitorování odvolání transakce protokolu
Pokud vaše dotazy se nedaří nebo trvá příliš dlouho chcete-li pokračovat, můžete zkontrolovat a monitorování, pokud máte jakékoli transakce vrácení zpět.
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

## <a name="next-steps"></a>Další postup
Další informace o zobrazení dynamické správy najdete v tématu [zobrazení systému][System views].


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
