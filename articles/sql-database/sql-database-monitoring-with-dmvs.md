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
ms.openlocfilehash: 8750670f2acc41cd712254ba11b4d2ec20aa58aa
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981832"
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

## <a name="monitor-resource-use"></a>Monitorovat využití prostředků

Můžete monitorovat využití prostředků pomocí [SQL Database Query Performance Insight](sql-database-query-performance.md) a [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Můžete také sledovat využití pomocí těchto dvou zobrazení:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Můžete použít [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) zobrazení v každé databázi SQL. **Sys.dm_db_resource_stats** zobrazení ukazuje poslední data použití prostředků relativně vzhledem k úrovni služeb. Průměrné procento CPU, datové v/v, zápisy protokolu a paměti se zaznamenávají každých 15 sekund a dobu 1 hodiny se zachovají.

Protože toto zobrazení nabízí podrobnější pohled na využití prostředků, použijte **sys.dm_db_resource_stats** první pro analýzu aktuální stav nebo řešení potíží. Například tento dotaz ukazuje průměrnou a maximální prostředky používané pro aktuální databázi za poslední hodinu:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data IO in percent',
        MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Pro další dotazy, podívejte se na příklady v [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats
[Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) zobrazit **hlavní** databáze obsahuje další informace, které vám může pomoct monitorování výkonu pro SQL database na úroveň konkrétních služby a vypočítat velikost. Data se shromažďují pro každých 5 minut a je zachován z důvodu přibližně 14 dnů. Toto zobrazení je užitečné pro dlouhodobější analýzu historie jak vaši službu SQL database využívá prostředky.

Následující graf ukazuje využití procesoru využití prostředků databáze úrovně Premium s velikostí výpočetních P2 pro každou hodinu za týden. Tento graf začíná v pondělí, zobrazuje 5 pracovních dní a poté zobrazí víkend, když se v aplikaci stane mnohem méně.

![Využití prostředků databáze SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Z dat, tato databáze má momentálně zatížení CPU ve špičce než 50 procent využití procesoru vzhledem k P2 vypočítat velikost (v poledne úterý). Pokud využití procesoru je dominantní faktoru v profilu aplikace prostředků, může rozhodnout, že P2 má velikost správných výpočetních zaručí, že zatížení vždy vyhovovat. Pokud očekáváte, že aplikace čase, je dobré mít vyrovnávací paměť dodatečný prostředek, takže aplikace nebude nikdy dosáhne limitu úroveň výkonu. Zákazník viditelné chyby, které mohou nastat, když databáze nemá dostatek výkonné nástroje pro zpracování žádostí efektivně, zejména v prostředích nízkou latenci můžete vyhnout zvýšíte výpočetního prostředí. Příkladem je databáze, která podporuje aplikace, která jsou vykreslovány webové stránky na základě výsledků volání databáze.

Ostatní typy aplikací může do stejného grafu interpretují odlišně. Například pokud se aplikace pokusí ke zpracování mezd dat každý den a má stejný graf, tento druh "úlohy služby batch" model může provádět bez problémů ve velikosti výpočetní prostředky P1. Velikost výpočetní prostředky P1 má 100 Dtu ve srovnání s 200 jednotek Dtu na P2 vypočítat velikost. Velikost výpočetní prostředky P1 poskytuje že poloviční výkonu P2 výpočetní velikost. Tedy 50 % využití procesoru v P2 se rovná 100 % využití procesoru v P1. Pokud aplikace nemá žádné vypršení časových limitů, nemusí je důležité, pokud úlohy trvá 2 hodiny nebo 2,5 hodin, pokud to proběhne ještě dnes. Aplikace v této kategorii pravděpodobně můžete použít velikost výpočetní prostředky P1. Můžete využít výhod vzhledem k tomu, že mají dobu během dne, kdy je využití prostředků nižší, tak, aby všechny "velký objem ve špičce" může být uložená do jedné ze žlaby později za den. Velikost výpočetní prostředky P1 může být vhodné pro tento typ aplikace (a ušetřit peníze) jako včasné každý den může dokončení úloh.

Azure SQL Database zpřístupňuje využívat informace o prostředku pro každou databázi aktivní **sys.resource_stats** zobrazení **hlavní** databází v každém serveru. Zobrazují se data v tabulce pro 5minutových intervalech. S úrovní služeb Basic, Standard a Premium dat může trvat více než 5 minut se zobrazí v tabulce, takže tato data je užitečnější pro historické analýzy spíše než analýzy téměř v reálném čase. Dotaz **sys.resource_stats** zobrazení nedávné historie databáze a ověřit, jestli se rezervace jste zvolili doručit výkonu chcete, aby v případě potřeby.

> [!NOTE]
> Musíte být připojeni k **hlavní** databáze logického serveru databáze SQL k dotazování **sys.resource_stats** v následujících příkladech.
> 
> 

Tento příklad ukazuje, jak vystavit data v tomto zobrazení:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Zobrazení katalogu sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

Další příklad ukazuje různé způsoby, které můžete použít **sys.resource_stats** katalogu zobrazení se získat informace o tom, jak vaši službu SQL database používá prostředky:

1. Podívat se na poslední týden prostředků použijte pro userdb1 databáze, můžete spustit tento dotaz:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Můžete vyhodnotit, jak dobře vaše úloha vyhovuje výpočetního prostředí, budete muset přejít na každý aspekt metriky prostředků: procesor, čtení, zápisu, počet pracovních procesů a počet relací. Tady je upravená dotazování pomocí **sys.resource_stats** hlášení průměrné a maximální hodnoty tyto metriky prostředků:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Pomocí těchto informací o průměrné a maximální hodnoty každého prostředku metriky můžete vyhodnotit, jak dobře vaše úloha zapadá do výpočetní velikost, kterou jste zvolili. Obvykle, průměrné hodnoty z **sys.resource_stats** umožňují dobré směrný plán použít proti cílovou velikost. Měla by být stonek vaše primární měření. Příklad může být pomocí úrovně služeb Standard S2 výpočty velikosti. Průměr použít procenta využití procesoru a vstupně-výstupních operací čtení a zápisy jsou pod 40 procent, průměrný počet pracovních procesů, které je nižší než 50 a průměrný počet relací je nižší než 200. Vaše zatížení může vešel do výpočetní S1. Je snadno zjistit, zda databáze vejde omezení pracovních procesů a relace. Abyste viděli, zda databáze zapadá do menší velikost výpočetního s ohledem na dělení procesoru, čtení a zápisy, počet DTU nižší vypočítat velikost podle počtu jednotek DTU vaše aktuální velikost výpočetních a výsledek vynásobit 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Výsledkem je relativní výkon rozdíl mezi těmito dvěma compute velikostí v procentech. Pokud využívání prostředků není překročí tuto hodnotu, může vaše úlohy vešel do nižší výpočetní prostředky. Ale budete muset podívejte se na všechny rozsahy hodnot použití prostředků a zjistit, v procentech, jak často by vaší úlohy databáze vešel do nižší výpočetní prostředky. Následující dotaz vypíše ve výstupu přizpůsobit procento prostředků dimenze, podle prahové hodnoty 40 procent vypočtené v tomto příkladu:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Podle vaší úrovně služby databáze, můžete rozhodnout, jestli vaše úloha zapadá do menší velikost výpočetní prostředky. Pokud je váš cíl úlohy databáze 99, 9procentní a předchozí dotaz vrací hodnoty větší než 99, 9procentní pro všechny tři prostředků dimenze, úloha pravděpodobně zapadá do nižší výpočty velikosti.
   
    Prohlížení přizpůsobit procento poskytuje také přehled o tom, zda byste měli přejít na další vyšší velikost výpočetní prostředky pro splnění cíle. Například userdb1 znázorňuje následující využití procesoru za minulý týden:
   
   | Průměrné procento CPU | Maximální procento využití procesoru |
   | --- | --- |
   | 24.5 |100.00 |
   
    Průměrné využití procesoru je o čtvrtletí limit velikosti výpočetní prostředky, které bude vyhovovat výpočty velikosti databáze. Ale maximální hodnota ukazuje, že databáze dosáhne limitu výpočetního prostředí. Je třeba přesunout na nejbližší větší velikost výpočetních? Podívejte se na postup v mnoha případech vaše zatížení dosáhne 100 % jeho obsahu a porovnejte je vaším cílem zatížení databáze.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Pokud tento dotaz vrací hodnotu menší než 99, 9procentní pro libovolný počet rozměrů tři prostředků zvažte přesunutí na nejbližší větší velikost výpočetních nebo používat techniky ladění aplikace ke snížení zatížení na databázi SQL.
4. V tomto cvičení také bere v úvahu vaše zvýšení plánované úlohy v budoucnu.

U elastických fondů můžete monitorovat jednotlivé databáze ve fondu pomocí technik popsaných v této části. Můžete ale také monitorovat fond jako celek. Další informace najdete v tématu [Monitorování a správa elastického fondu](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Maximální počet souběžných požadavků
Pokud chcete zobrazit počet souběžných požadavků, spusťte tento dotaz jazyka Transact-SQL v SQL database:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Pokud chcete analyzovat úlohy k místní databázi SQL serveru, upravte tento dotaz pro filtrování v konkrétní databázi, že který chcete analyzovat. Pokud máte místní databázi s názvem databáze, tento dotaz jazyka Transact-SQL vrátí počet souběžných požadavků v této databázi:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Toto je snímek v jednom okamžiku v čase. Pokud chcete získat lepší přehled o vašich úloh a požadavky souběžný požadavek, musíte postupně shromáždit mnoha ukázek.

### <a name="maximum-concurrent-logins"></a>Maximální souběžných přihlášení
Můžete analyzovat vzory vašich uživatelů a aplikací, kde získáte představu o četnosti přihlášení. Reálné zátěže můžete spustit také v testovacím prostředí, abyste měli jistotu, že jste nedosahují to nebo další omezení, které si popíšeme v tomto článku. Není k dispozici jeden dotaz nebo zobrazení dynamické správy (DMV), které můžete zobrazit souběžných počítá přihlášení nebo z historie.

Pokud více klientů používat stejný připojovací řetězec, služba se ověřuje každé přihlášení. Pokud 10 uživatelů současně připojit k databázi pomocí stejného uživatelského jména a hesla, by existovat 10 souběžných přihlášení. Toto omezení platí pouze pro dobu trvání přihlášení a ověření. Pokud se stejný 10 uživatelů připojit k databázi postupně, počet souběžných přihlášení by nikdy být větší než 1.

> [!NOTE]
> V současné době toto omezení se nevztahuje na databází v elastických fondech.
> 
> 

### <a name="maximum-sessions"></a>Maximální počet relací
Pokud chcete zobrazit počet aktuálních aktivních relací, spusťte tento dotaz jazyka Transact-SQL v SQL database:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Pokud analyzujete pracovního vytížení místní SQL Server, tento dotaz změnit a zaměřte se na konkrétní databáze. Tento dotaz vám pomůže určit, potřeb možné relace pro databázi Pokud zvažujete Přesun do Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Tyto dotazy znovu, vrátí počet bodu v čase. Budete-li shromažďovat víc ukázky v čase, budete mít nejlepší znalost relaci použít.

Pro účely analýzy SQL Database, můžete získat historické statistiky na relace pomocí dotazu [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) zobrazení a kontrola **active_session_count** sloupce. 

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

