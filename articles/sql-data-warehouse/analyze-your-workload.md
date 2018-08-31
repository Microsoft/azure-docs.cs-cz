---
title: Analýza úloh – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Techniky pro stanovení priority dotazů pro vaše úlohy ve službě Azure SQL Data Warehouse analýzu.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 4ce84e9714b580bcc243285dc1da5ae24a27e8e5
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248089"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Analýza úloh ve službě Azure SQL Data Warehouse
Techniky pro stanovení priority dotazů pro vaše úlohy ve službě Azure SQL Data Warehouse analýzu.

## <a name="workload-groups"></a>Skupiny úloh 
SQL Data Warehouse implementuje třídy prostředků pomocí skupiny úloh. Existují celkem osm skupiny úloh, které řídí chování třídy prostředků v různých velikostí DWU. Pro všechny DWU SQL Data Warehouse používá pouze čtyři skupiny osm úloh. Tento přístup smysl vzhledem k tomu, že každá skupina úloh je přiřazena k jedné ze čtyř tříd prostředků: smallrc mediumrc, largerc, nebo xlargerc. Důležitost Principy skupin úloh je, že některé z těchto skupin úloh jsou nastaveny na vyšší *význam*. Důležitost se používá pro procesor plánování. Dotazy spouštět s vysokou důležitostí získat třikrát více cykly procesoru než dotazy spouštět se střední důležitostí. Proto souběžnosti slotu mapování také určit, priorita procesoru. Když dotaz využívá 16 nebo víc sloty, poběží brána jako vysokou důležitostí.

Následující tabulka uvádí mapování důležitosti pro každou skupinu úloh.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapování skupin úloh a význam slotů souběžnosti

| Skupiny úloh | Mapování slotu souběžnosti | MB / distribuce (Elasticitu) | MB / distribuce (Compute) | Důležitost mapování |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | Střednědobé používání             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | Střednědobé používání             |
| SloDWGroupC02   | 4                        |    400                         | 1000                        | Střednědobé používání             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | Střednědobé používání             |
| SloDWGroupC04   | 16                       |  1,600                         | 4000                        | Vysoký               |
| SloDWGroupC05   | 32                       |  3,200                         | 8000                        | Vysoký               |
| SloDWGroupC06   | 64                       |  6,400                         | 16,000                      | Vysoký               |
| SloDWGroupC07   | 128                      | 12,800                         | 32,000                      | Vysoký               |
| SloDWGroupC08   | 256                      | 25,600                         | 64,000                      | Vysoký               |

<!-- where are the allocation and consumption of concurrency slots charts? --> **Přidělení a využití množství slotů souběžnosti** graf ukazuje DW500 používá 1, 4, 8 nebo 16 souběžnosti sloty pro smallrc, mediumrc, largerc nebo xlargerc, v uvedeném pořadí. K vyhledání důležitosti pro každou třídu prostředků, můžete vyhledat tyto hodnoty v předchozím grafu.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500 mapování třídy prostředků na důležitosti
| Třída prostředků | Skupinu úloh | Použít slotů souběžnosti | MB / distribuce | Důležitost |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | Střednědobé používání     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | Střednědobé používání     |
| largerc        | SloDWGroupC03  | 8                      | 800               | Střednědobé používání     |
| xlargerc       | SloDWGroupC04  | 16                     | 1,600             | Vysoký       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | Střednědobé používání     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | Střednědobé používání     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | Střednědobé používání     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | Střednědobé používání     |
| staticrc50     | SloDWGroupC03  | 16                     | 1,600             | Vysoký       |
| staticrc60     | SloDWGroupC03  | 16                     | 1,600             | Vysoký       |
| staticrc70     | SloDWGroupC03  | 16                     | 1,600             | Vysoký       |
| staticrc80     | SloDWGroupC03  | 16                     | 1,600             | Vysoký       |

## <a name="view-workload-groups"></a>Zobrazení skupiny úloh
Následující dotaz zobrazí podrobnosti o přidělování prostředků paměti z pohledu správce zdrojů. To je užitečné pro analýzu aktivní a historické využití skupiny úloh, při řešení potíží.

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>Zjišťování ve frontě dotazů a dalších zobrazení dynamické správy
Můžete použít `sys.dm_pdw_exec_requests` a identifikovat dotazy, které čekají na přiřazení ve frontě souběžnosti. Čekání pro sloty souběžnosti dotazy jsou ve stavu **pozastaveno**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Role pro správu úloh lze zobrazit pomocí `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Následující dotaz ukazuje, jaké role je přiřazená každému uživateli.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Data Warehouse má následující typy čekání:

* **LocalQueriesConcurrencyResourceType**: dotazy, které se nacházejí mimo rámec slotu souběžnosti. Dotazy na zobrazení dynamické správy a systémové funkce, jako například `SELECT @@VERSION` jsou příklady místní dotazů.
* **UserConcurrencyResourceType**: dotazy, které se nacházejí uvnitř rozhraní concurrency slot. Dotazy na tabulky s koncovým uživatelem představují příklady, které byste použili tento typ prostředku.
* **DmsConcurrencyResourceType**: plynoucí z operace přesunu dat čeká.
* **BackupConcurrencyResourceType**: Tato čekací označuje, že databáze je právě zálohován. Maximální hodnota pro tento typ prostředku je 1. Pokud více záloh je požadována ve stejnou dobu, jiné fronty.

`sys.dm_pdw_waits` Zobrazení dynamické správy je možné zobrazit žádost čeká na prostředky.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits` DMV zobrazuje pouze ta čeká prostředků používané daný dotaz. Doba čekání prostředků pouze měří čas čekání na prostředky, které mají být poskytnuty, na rozdíl od doba čekání signál, který je čas potřebný pro základní SQL servery, které chcete naplánovat dotaz na CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
Můžete také použít `sys.dm_pdw_resource_waits` DMV vypočítat počet slotů souběžnosti byl udělen.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` Zobrazení dynamické správy lze použít k analýze historických trendů čeká.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Další postup
Další informace o správě uživatelů a zabezpečení najdete v tématu [zabezpečit databázi ve službě SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md). Další informace o tom, jak větší třídy prostředků může zlepšit kvalitu indexu columnstore clusteru, najdete v části [nové sestavení indexů ke zlepšení kvality segmentů](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).


