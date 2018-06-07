---
title: Monitorování databáze Azure SQL pomocí zobrazení dynamické správy | Microsoft Docs
description: Zjistěte, jak najít a diagnostikovat běžné problémy s výkonem pomocí zobrazení dynamické správy ke sledování Microsoft Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: a1333680225923a4e27f96e61a5b6530f32a9329
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647880"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Monitorování databáze Azure SQL Database pomocí zobrazení dynamické správy
Microsoft Azure SQL Database umožňuje podmnožinu zobrazení dynamické správy diagnostikovat problémy s výkonem, které mohou být způsobeny blokované nebo dlouhodobé dotazy, kritických bodů prostředků, dotaz nízký plány a tak dále. Toto téma obsahuje informace o tom, jak zjišťují běžné problémy s výkonem pomocí zobrazení dynamické správy.

Databáze SQL podporuje částečně tří kategorií zobrazení dynamické správy:

* Zobrazení dynamické správy vztahující se k databázi.
* Zobrazení dynamické správy souvisejících s provádění.
* Zobrazení dynamické správy související transakci.

Podrobné informace o zobrazení dynamické správy najdete v tématu [funkce (Transact-SQL) a zobrazení dynamické správy](https://msdn.microsoft.com/library/ms188754.aspx) v SQL Server Books Online.

## <a name="permissions"></a>Oprávnění
V databázi SQL, dotaz na zobrazení dynamické správy vyžaduje **stav databáze zobrazení** oprávnění. **Stav databáze zobrazení** oprávnění vrátí informace o všech objektech v aktuální databázi.
Udělit **stav databáze zobrazení** oprávnění pro uživatele konkrétní databáze, spusťte následující dotaz:

```GRANT VIEW DATABASE STATE TO database_user; ```

V instanci systému SQL Server, místní zobrazení dynamické správy vrátí informace o stavu serveru. V databázi SQL že budou vracet informace týkající se pouze v aktuální databázi logické.

## <a name="calculating-database-size"></a>Výpočet velikosti databáze
Následující dotaz vrátí velikost databáze (v megabajtech):

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

Následující dotaz vrátí velikost jednotlivých objektů (v megabajtech) v databázi:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Sledování připojení
Můžete použít [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) zobrazení k načtení informací o připojení ke konkrétní server Azure SQL Database a podrobnosti o každé připojení. Kromě toho [zobrazení sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) zobrazení je vhodné při načítání informací o všechna připojení aktivního uživatele a interních úlohách.
Následující dotaz načte informace o aktuální připojení:

```
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
> Při provádění **sys.dm_exec_requests** a **zobrazení zobrazení sys.dm_exec_sessions**, pokud máte **stav databáze zobrazení** oprávnění v databázi, uvidíte všechny provádění relace v databázi; jinak uvidíte pouze pro aktuální relaci.
> 
> 

## <a name="monitoring-query-performance"></a>Sledování výkonu dotazů
Pomalu nebo dlouho spuštění dotazů můžete využívat významné systémové prostředky. V této části ukazuje, jak pomocí zobrazení dynamické správy lze zjistit několik běžných potíží s výkonem dotazu. Odkaz na starší, ale stále užitečné při řešení potíží se [řešení potíží s problémy s výkonem v systému SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) článek na Microsoft TechNetu.

### <a name="finding-top-n-queries"></a>Hledání hlavních dotazy
Následující příklad vrací informace o nejvyšší pět dotazy podle Průměrná doba využití procesoru. Tento příklad agreguje dotazy podle jejich hash dotaz tak, aby logicky ekvivalentní dotazy jsou seskupené podle jejich spotřeba kumulativní prostředků.

```
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
Pomalá nebo dlouhodobé dotazy můžete přispívat k všimnete nadměrné spotřeby prostředků a být důsledek blokované dotazy. Důvod blokování může být návrh nízký aplikace, plány chybných dotazů, nedostatečná užitečné indexy a tak dále. Zobrazení sys.dm_tran_locks můžete získat informace o aktuální uzamčení aktivita ve vaší databázi SQL Azure. Příklad kódu, najdete v části [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) v SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Monitorování plány dotazů
Plán dotazu neefektivní může také zvýšit spotřeby procesoru. Následující příklad používá [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) zobrazení dotazu, který používá většina kumulativní procesoru.

```
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
[Úvod do databáze SQL](sql-database-technical-overview.md)

