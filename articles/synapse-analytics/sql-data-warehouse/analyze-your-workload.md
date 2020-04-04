---
title: Analýza úloh
description: Techniky pro analýzu priorit dotazů pro vaše úlohy v Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 6a38fe65b4aedf4f594531f5e9cd8cf9b5dfaac7
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631239"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>Analýza úloh v Azure Synapse Analytics

Techniky pro analýzu úlohy Synapse SQL v Azure Synapse Analytics.

## <a name="resource-classes"></a>Třídy prostředků

Synapse SQL poskytuje třídy prostředků pro přiřazení systémových prostředků k dotazům.  Další informace o třídách prostředků naleznete v [tématu Třídy prostředků & správa pracovního vytížení](resource-classes-for-workload-management.md).  Dotazy budou čekat, pokud třída prostředků přiřazená k dotazu potřebuje více prostředků, než je aktuálně k dispozici.

## <a name="queued-query-detection-and-other-dmvs"></a>Zjišťování dotazů ve frontě a další dmv

DMV `sys.dm_pdw_exec_requests` můžete použít k identifikaci dotazů, které čekají ve frontě souběžnosti. Dotazy čekající na souběžnou pozici mají stav **pozastaveno**.

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

Role správy úloh lze `sys.database_principals`zobrazit pomocí aplikace .

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Následující dotaz ukazuje, ke které roli je každý uživatel přiřazen.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

Synapse SQL má následující typy čekání:

* **LocalQueriesConcurrencyResourceType**: Dotazy, které jsou mimo rámec souběžnosti slot. DMV dotazy a systémové `SELECT @@VERSION` funkce, jako jsou příklady místních dotazů.
* **UserConcurrencyResourceType**: Dotazy, které jsou v rámci rozhraní slotu souběžnosti. Dotazy proti tabulkám koncových uživatelů představují příklady, které by tento typ prostředku používaly.
* **DmsConcurrencyResourceType**: Čeká vyplývající z operací přesunu dat.
* **BackupConcurrencyResourceType**: Toto čekání označuje, že je zálohována databáze. Maximální hodnota pro tento typ prostředku je 1. Pokud bylo požadováno více záloh současně, ostatní fronty. Obecně doporučujeme minimální čas mezi po sobě jdoucísnímky 10 minut.

`sys.dm_pdw_waits` DMV lze zobrazit prostředky, na které požadavek čeká.

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

DMV `sys.dm_pdw_resource_waits` zobrazuje informace o čekání pro daný dotaz. Doba čekání prostředků měří dobu čekání na prostředky, které mají být poskytnuty. Doba čekání signálu je doba, kterou základní servery SQL naplánují dotaz na procesor.

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

Můžete také použít `sys.dm_pdw_resource_waits` DMV vypočítat, kolik souběžnost sloty byly uděleny.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV lze použít pro analýzu historického trendu čekání.

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

## <a name="next-steps"></a>Další kroky

Další informace o správě uživatelů databáze a zabezpečení naleznete [v tématu Zabezpečení databáze v synapse SQL](sql-data-warehouse-overview-manage-security.md). Další informace o tom, jak větší třídy prostředků může zlepšit kvalitu indexu clusterované columnstore, naleznete v [tématu Opětovné sestavení indexů ke zlepšení kvality segmentu](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
