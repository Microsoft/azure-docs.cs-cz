---
title: Analýza úloh ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Techniky pro stanovení priority dotazů pro vaše úlohy ve službě Azure SQL Data Warehouse analýzu.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: f470670ae3d526f3b66badf219a01a471c24db0d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242238"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Analýza úloh ve službě Azure SQL Data Warehouse

Techniky pro analýzu vašich úloh ve službě Azure SQL Data Warehouse.

## <a name="resource-classes"></a>Třídy prostředků

SQL Data Warehouse poskytuje třídy prostředků přiřadit systémové prostředky pro dotazy.  Další informace o třídách prostředků najdete v části [prostředků třídy a Správa úloh](resource-classes-for-workload-management.md).  Dotazy počká, pokud třída prostředků přiřazen dotaz potřebuje víc prostředků, než jsou aktuálně k dispozici.

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

* **LocalQueriesConcurrencyResourceType**: Dotazy, které se nacházejí mimo rámec slotu souběžnosti. Dotazy na zobrazení dynamické správy a systémové funkce, jako například `SELECT @@VERSION` jsou příklady místní dotazů.
* **UserConcurrencyResourceType**: Dotazy, které se nacházejí uvnitř rozhraní concurrency slot. Dotazy na tabulky s koncovým uživatelem představují příklady, které byste použili tento typ prostředku.
* **DmsConcurrencyResourceType**: Výsledkem operace přesunu dat čeká.
* **BackupConcurrencyResourceType**: Tato čekací označuje, že databázi je právě zálohován. Maximální hodnota pro tento typ prostředku je 1. Pokud více záloh je požadována ve stejnou dobu, jiné fronty. Obecně doporučujeme minimální čas mezi po sobě jdoucích snímků 10 minut. 

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

`sys.dm_pdw_resource_waits` Zobrazení dynamické správy obsahuje informace o čekání pro daný dotaz. Zdroj čekat časových dobu čekání na prostředky, které mají být poskytnuty. Signál čekací doba je čas potřebný pro základní SQL servery, které chcete naplánovat dotaz na CPU.

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
