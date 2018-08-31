---
title: Monitorování úloh pomocí zobrazení dynamické správy | Dokumentace Microsoftu
description: Další informace o monitorování vaší úlohy pomocí zobrazení dynamické správy.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fe989a1693d73dbbea7ed0e3e91ed7aaf6fc37c4
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301078"
---
# <a name="monitor-your-workload-using-dmvs"></a>Monitorování vaší úlohy pomocí DMV
Tento článek popisuje, jak monitorování vaší úlohy pomocí zobrazení dynamické správy (DMV). To zahrnuje zkoumání provádění dotazů ve službě Azure SQL Data Warehouse.

## <a name="permissions"></a>Oprávnění
K dotazování zobrazení dynamické správy v tomto článku, budete potřebovat oprávnění VIEW DATABASE STATE nebo ovládací PRVEK. Obvykle muselo VIEW DATABASE STATE je upřednostňovaný oprávnění, protože to je mnohem více omezující.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitorování připojení
Všechna přihlášení do služby SQL Data Warehouse se Zaprotokolují [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Toto zobrazení dynamické správy obsahuje posledních 10 000 přihlášení.  Session_id představuje primární klíč a je přiřazena postupně pro každé nové přihlášení.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitorování provádění dotazů
Všechny dotazy spouštěné ve službě SQL Data Warehouse se Zaprotokolují [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Toto zobrazení dynamické správy obsahuje posledních 10 000 spuštěných dotazů.  Request_id jednoznačně identifikuje každý dotaz a primární klíč pro toto zobrazení dynamické správy.  Request_id přiřazena postupně pro každý nový dotaz a je předponu QID, což je zkratka pro query ID.  Dotazování na toto zobrazení dynamické správy pro daného session_id ukazuje všechny dotazy pro dané přihlášení.

> [!NOTE]
> Uložené procedury pomocí víc ID žádosti.  ID žádosti jsou přiřazeny v sekvenčním pořadí. 
> 
> 

Tady je postup, jak zjistit plánům spuštění dotazů a časy pro konkrétní dotaz.

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

Z předchozích výsledků dotazu **si poznamenejte ID žádosti** dotazu, který chcete prozkoumat.

Dotazy v **pozastaveno** stavu jsou právě ve frontě z důvodu omezení souběžnosti. Tyto dotazy se také zobrazí v dotazu čeká sys.dm_pdw_waits s typem UserConcurrencyResourceType. Informace o omezení souběžnosti, naleznete v tématu [úrovně výkonu](performance-tiers.md) nebo [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md). Dotazy můžete také počkat z jiných důvodů, jako objekt zámků.  Pokud váš dotaz je čekání na prostředek, přečtěte si téma [zkoumání dotazů čekání na prostředky] [ Investigating queries waiting for resources] další dolů v tomto článku.

Pro zjednodušení vyhledávací dotaz v tabulce sys.dm_pdw_exec_requests pomocí [popisek] [ LABEL] přiřadit dotazu, který lze vyhledávat v zobrazení sys.dm_pdw_exec_requests komentář.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>Krok 2: Prozkoumání plán dotazu
ID požadavku. použijte k načtení dotazu distribuované plánu SQL (DSQL) z [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Pokud plán DSQL trvá déle, než se očekávalo, příčinou může být komplexní plán s mnoha kroky DSQL nebo jenom jeden krok trvá příliš dlouho.  Pokud v plánu je mnoho kroků s několika operací přesunu, uvažujte o optimalizaci vaše distribuce tabulky omezit přesun dat. [Distribuce tabulky] [ Table distribution] článek vysvětluje, proč data je nutné přesunout k vyřešení dotazu a vysvětluje některé distribuční strategie pro minimalizaci přesun dat.

Dále prozkoumat podrobnosti o jeden krok *operation_type* sloupec dlouhotrvající krok dotazu a Všimněte si **Index kroku**:

* Pokračovat v kroku 3a pro **SQL operací**: OnOperation RemoteOperation, ReturnOperation.
* Krok 3b pro pokračujte **operace přesunu dat**: ShuffleMoveOperation BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>KROK 3a: prozkoumání SQL na distribuované databáze
Umožňuje načíst podrobnosti ze ID žádosti a Index kroku [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], který obsahuje informace o spuštění kroku dotazu na všechny distribuované databáze.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Při spuštění kroku dotazu [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] slouží k načtení odhadované plánu systému SQL Server z mezipaměti plánů systému SQL Server pro krok probíhá na zvláštní distribuci.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>Krok 3b: prozkoumání přesunu dat pro distribuované databáze
K načtení informací o běžící na každé distribuci z kroku přesunu dat používat ID žádosti a Index kroku [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Zkontrolujte, *total_elapsed_time* sloupec, pokud zvláštní distribuci trvá výrazně déle, než jiné pro přesun dat.
* Dlouho probíhající distribuce, zkontrolujte *rows_processed* sloupec, který chcete zjistit, zda počet řádků, který se přesouvá z tohoto distribučního je podstatně větší než jiné. Pokud ano, může znamenat toto zjištění zkosení podkladová data.

Pokud dotaz běží, [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] slouží k načtení odhadované plánu systému SQL Server ze systému SQL Server mezipaměti plánu aktuálně spuštěné SQL krok v rámci konkrétní distribuce.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitorování dotazů čekání
Pokud zjistíte, že váš dotaz není vidět pokrok, protože se čeká na prostředek, tady je dotaz, který ukazuje že dotaz čeká na všechny prostředky.

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

Pokud dotaz aktivně čeká na prostředky z jiného dotazu, pak bude mít stav **AcquireResources**.  Pokud dotaz obsahuje všechny požadované prostředky, pak bude mít stav **udělit**.

## <a name="monitor-tempdb"></a>Monitorování databáze tempdb
Využití databáze tempdb vysoké, může být hlavní příčinou nízkého výkonu a mimo problémy s pamětí. Zvažte možnost škálování vašeho datového skladu, je-li najít databázi tempdb dosažení omezení při provádění dotazu. Následující informace popisují, jak identifikovat využití databáze tempdb na dotaz na každém uzlu. 

Vytvořte následující zobrazení přidružit ID příslušný uzel sys.dm_pdw_sql_requests. Můžete používat další průchozí zobrazení dynamické správy a připojte se k těmto tabulky s sys.dm_pdw_sql_requests umožní s ID uzlu.

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
Monitorování databáze tempdb, spusťte následující dotaz:

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

Paměť může být hlavní příčinou nízkého výkonu a mimo problémy s pamětí. Zvažte možnost škálování vašeho datového skladu, pokud zjistíte, využití paměti systému SQL Server dosažení omezení při provádění dotazu.

Následující dotaz vrátí přetížení využití a paměti paměti systému SQL Server na jeden uzel:   
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
Následující dotaz vrátí velikost protokolu transakcí v každé distribuci. Pokud jeden soubor protokolu se blíží 160 GB, měli byste zvážit vertikální navýšení vaší instance nebo omezení velikost transakce. 
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
## <a name="monitor-transaction-log-rollback"></a>Monitorování protokolu odvolání transakce
Pokud vaše dotazy jsou služeb při selhání nebo trvá příliš dlouho, aby bylo možné pokračovat, můžete zkontrolovat a sledovat, pokud máte jakékoli transakce vrácení zpět.
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
Další informace o zobrazení dynamické správy najdete v tématu [systémová zobrazení][System views].


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
