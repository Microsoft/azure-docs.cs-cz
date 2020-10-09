---
title: Analýza úloh
description: Techniky pro analýzu priorit dotazů pro vaše úlohy ve službě Azure synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c547263be8c61d75491d1517b58c03b6365ef929
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85208395"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>Analýza úloh v Azure synapse Analytics

Techniky analýzy úloh SQL synapse ve službě Azure synapse Analytics. 

## <a name="resource-classes"></a>Třídy prostředků

Synapse SQL poskytuje třídy prostředků pro přiřazení systémových prostředků dotazům.  Další informace o třídách prostředků naleznete v tématu [třídy prostředků & Správa úloh](resource-classes-for-workload-management.md).  Dotazy budou čekat, pokud Třída prostředků přiřazená k dotazu potřebuje více prostředků, než je aktuálně k dispozici.

## <a name="queued-query-detection-and-other-dmvs"></a>Detekce dotazu ve frontě a další zobrazení dynamické správy

Pomocí `sys.dm_pdw_exec_requests` DMV můžete identifikovat dotazy, které čekají ve frontě souběžnosti. Dotazy, které čekají na pozici souběžnosti, mají stav **pozastaveno**.

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

Role správy úloh je možné zobrazit pomocí `sys.database_principals` .

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Následující dotaz ukazuje, ke které roli má každý uživatel přiřazený.

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

* **LocalQueriesConcurrencyResourceType**: dotazy, které sedí mimo rámec rozhraní pro sloty Concurrency. Dotazy DMV a systémové funkce, jako `SELECT @@VERSION` jsou příklady místních dotazů.
* **UserConcurrencyResourceType**: dotazy, které sedí v rámci rozhraní slotu pro souběžnost. Dotazy na tabulky koncového uživatele reprezentují příklady, které by používaly tento typ prostředku.
* **DmsConcurrencyResourceType**: čeká v důsledku operací přesunu dat.
* **BackupConcurrencyResourceType**: Tento počkat indikuje, že se databáze zálohuje. Maximální hodnota pro tento typ prostředku je 1. Pokud je v jednom okamžiku požadováno více záloh, fronta ostatní. Obecně doporučujeme minimální dobu mezi po sobě jdoucí snímky po dobu 10 minut.

`sys.dm_pdw_waits`DMV lze použít k zobrazení prostředků, na které požadavek čeká.

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

`sys.dm_pdw_resource_waits`DMV zobrazuje informace o čekání pro daný dotaz. Doba čekání prostředku měří čas na zadání prostředků. Doba čekání signálu je doba potřebná k tomu, aby základní SQL servery naplánovaly dotaz na procesor.

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

Můžete také použít `sys.dm_pdw_resource_waits` DMV vypočítat, kolik slotů souběžnosti bylo uděleno.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats`DMV se dá použít k historické analýze trendů čekání.

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

Další informace o správě uživatelů a zabezpečení databáze najdete v tématu [zabezpečení databáze v synapse SQL](sql-data-warehouse-overview-manage-security.md). Další informace o tom, jak můžou větší třídy prostředků zlepšit kvalitu clusterového indexu columnstore, najdete v tématu [sestavení indexů pro zlepšení kvality segmentů](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
