---
title: Monitorování databáze Azure SQL Database pomocí zobrazení dynamické správy | Dokumentace Microsoftu
description: Zjistěte, jak detekovat a diagnostikovat běžné problémy s výkonem pomocí zobrazení dynamické správy monitorovat Microsoft Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: carlrab
ms.openlocfilehash: c4d1170bd2fe4acb135c88191b447f734e312723
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715953"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Monitorování databáze Azure SQL Database pomocí zobrazení dynamické správy
Microsoft Azure SQL Database umožňuje podmnožinu zobrazení dynamické správy pro diagnostiku problémů s výkonem, které mohou být způsobeny zablokování nebo dlouho probíhající dotazy, kritických bodů prostředků, plány nízký dotazů a tak dále. Toto téma obsahuje informace o tom, jak detekovat běžné problémy s výkonem pomocí zobrazení dynamické správy.

SQL Database podporuje částečně tři kategorie zobrazení dynamické správy:

* Zobrazení dynamické správy vztahující se k databázi.
* Zobrazení dynamické správy týkající se provádění.
* Zobrazení dynamické správy související s transakcí.

Podrobné informace o zobrazení dynamické správy, naleznete v tématu [funkcí (Transact-SQL) a zobrazení dynamické správy](https://msdn.microsoft.com/library/ms188754.aspx) v SQL Server Books Online.

## <a name="permissions"></a>Oprávnění
Ve službě SQL Database, dotazování zobrazení dynamické správy vyžaduje **VIEW DATABASE STATE** oprávnění. **VIEW DATABASE STATE** oprávnění vrátí informace o všech objektech v aktuální databázi.
Chcete-li udělit **VIEW DATABASE STATE** oprávnění ke konkrétnímu uživateli databáze, spusťte následující dotaz:

```GRANT VIEW DATABASE STATE TO database_user; ```

Zobrazení dynamické správy v instanci systému SQL Server v místním, vrátí informace o stavu serveru. Vrátí informace týkající se pouze v aktuální databázi logické ve službě SQL Database.

## <a name="calculating-database-size"></a>Výpočet velikosti databáze
Následující dotaz vrátí velikost databáze (v megabajtech):

```
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
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

## <a name="monitoring-connections"></a>Monitorování připojení
Můžete použít [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) zobrazení k načtení informací o připojení pro konkrétní server Azure SQL Database a podrobnosti o každé připojení. Kromě toho [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) zobrazení je užitečné při načítání informací o všech aktivních uživatelských připojení a interních úlohách.
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
> Při provádění **sys.dm_exec_requests** a **zobrazení sys.dm_exec_sessions**, pokud máte **VIEW DATABASE STATE** oprávnění v databázi, se zobrazí všechna spuštění relace v databázi. v opačném případě se zobrazí pouze pro aktuální relaci.
> 
> 

## <a name="monitoring-query-performance"></a>Sledování výkonu dotazů
Pomalé nebo dlouho probíhající dotazy může spotřebovat významné systémových prostředků. Tato část ukazuje, jak detekovat několik běžných problémech s výkonem dotazů pomocí zobrazení dynamické správy. Odkaz na starší, ale přesto užitečné při řešení potíží, je [řešení potíží s problémy s výkonem v systému SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) článek na Microsoft TechNetu.

### <a name="finding-top-n-queries"></a>Hledání dotazy horních N
Následující příklad vrátí informace o prvních pěti dotazech seřazených podle průměrné doby využití procesoru. V tomto příkladu agreguje dotazy podle jejich hodnota hash dotazu tak, aby logicky ekvivalentní dotazy jsou seskupeny podle jejich spotřeba kumulativní prostředků.

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
Pomalá nebo dlouho probíhající dotazy mohou přispět ke všimnete nadměrné spotřeby prostředků a možné důsledků blokované dotazy. Příčinu zablokování může být nekvalitní návrh aplikací, plány chybných dotazů, nedostatek vhodné indexy a tak dále. Zobrazení sys.dm_tran_locks můžete získat informace o aktuální uzamčení aktivity ve službě Azure SQL Database. Příklad kódu naleznete v tématu [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) v SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Monitorování plány dotazů
Plán dotazu neefektivní také může zvýšit využití procesoru. V následujícím příkladu [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) zobrazení a určit, který dotaz používá většina kumulativní využití procesoru.

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
[Úvod do služby SQL Database](sql-database-technical-overview.md)

