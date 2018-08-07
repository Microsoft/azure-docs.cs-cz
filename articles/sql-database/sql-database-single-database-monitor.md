---
title: Monitorování výkonu databáze ve službě Azure SQL Database | Dokumentace Microsoftu
description: Seznamte se s možnostmi monitorování vaší databáze pomocí nástrojů Azure a zobrazení dynamické správy.
keywords: monitorování databáze, výkon cloudové databáze
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 081c6210181815be079404b10e7aa4b0df8327ec
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520975"
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Monitorování výkonu databáze ve službě Azure SQL Database
Monitorování výkonu databáze SQL v Azure začíná sledováním využití prostředků relativně ke zvolené úrovni výkonu databáze. Monitorování vám pomůže určit, zda má přebytečnou kapacitu databáze, nebo má potíže, protože prostředky jsou maxed navýšení kapacity a následně se rozhodnete, zda je třeba upravit úroveň výkonu a vaší databáze v úrovních služeb [založený na DTU nákupní model](sql-database-service-tiers-dtu.md) nebo [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). Databázi můžete monitorovat pomocí grafických nástrojů na [portálu Azure](https://portal.azure.com) nebo pomocí [zobrazení dynamické správy SQL](https://msdn.microsoft.com/library/ms188754.aspx).

> [!TIP]
> Použití [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pro automatické monitorování výkonu vaší databáze. Po zjištění problému s výkonem se vygeneruje protokol diagnostiky s podrobnostmi a kořenové příčiny analýzy RCA () problému. Pokud je to možné, poskytuje doporučení pro zlepšení výkonu.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorování databází na portálu Azure
Na [portálu Azure](https://portal.azure.com/) můžete monitorovat využití izolované databáze jednoduše tak, že vyberete databázi a kliknete na graf **Monitorování**. Zobrazí se okno **Metrika**, které můžete upravit kliknutím na **Upravit graf**. Přidejte následující metriky:

* Procento CPU
* Procento DTU
* Procento datových V/V
* Procento velikosti databáze

Jakmile přidáte tyto metriky, můžete pokračovat jejich zobrazením v **monitorování** graf s dalšími informacemi o **metrika** okna. Tyto čtyři metriky uvádějí průměrné využití v procentech vzhledem k hodnotě **DTU** vaší databáze. Zobrazit [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) články pro další informace o úrovních služeb.  

![Monitorování výkonu databáze v rámci úrovně služeb](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Můžete také nastavit upozornění na výkonové metriky. Klikněte na tlačítko **Přidat upozornění** v okně **Metrika**. Nastavte upozornění podle pokynů průvodce. Můžete určit, zda chcete být upozorněni na překročení zadané prahové hodnoty, nebo naopak když metrika poklesne pod zadanou mez.

Například pokud očekáváte nárůst zatížení databáze, můžete nastavit e-mailové upozornění pro případ, že databáze překročí 80 % kterékoli výkonové metriky. Máte tak možnost předběžného varování, aby byl čas rozhodnout, zda nenastal čas přechodu na vyšší úroveň výkonu.

Metrika výkonu vám také pomůže zjistit, zda je možné úroveň výkonu snížit. Předpokládejme, že používáte databáze S2 v úrovni Standard a všechny metriky ukazují, že databáze v průměru nevyužívá více než 10 % dostupného výkonu. Je pravděpodobné, že databáze bude dobře fungovat i v úrovni Standard S1. Než se však rozhodnete ke snížení úrovně výkonu, zvažte možnost výskytu špiček nebo náhlého kolísání zátěže.

## <a name="monitor-databases-using-dmvs"></a>Monitorování databází pomocí zobrazení dynamické správy
Stejné metriky, které jsou přístupné na portálu, můžete zobrazit také prostřednictvím systémových zobrazení: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) v logické **hlavní** databázi vašeho serveru a [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) v uživatelské databázi. Pomocí zobrazení **sys.resource_stats** můžete sledovat hrubší data v delších časových obdobích. Pomocí zobrazení **sys.dm_db_resource_stats** můžete sledovat data s větším rozlišením a v kratších úsecích. Další informace najdete v tématu [Azure SQL Database – průvodce výkonem](sql-database-single-database-monitor.md#monitor-resource-use).

> [!NOTE]
> **Sys.dm_db_resource_stats** vrací prázdný výsledek při použití pro databáze s úrovněmi Web a Business, které jsou již ukončené.
>
>

### <a name="monitor-resource-use"></a>Monitorovat využití prostředků

Můžete monitorovat využití prostředků pomocí [SQL Database Query Performance Insight](sql-database-query-performance.md) a [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Můžete také sledovat využití pomocí těchto dvou zobrazení:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
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

#### <a name="sysresourcestats"></a>sys.resource_stats
[Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) zobrazit **hlavní** databáze obsahuje další informace, které vám pomohou sledovat výkon vaší databáze SQL na úrovni vrstvy a výkonu konkrétní služby. Data se shromažďují pro každých 5 minut a je zachován z důvodu přibližně 14 dnů. Toto zobrazení je užitečné pro dlouhodobější analýzu historie jak vaši službu SQL database využívá prostředky.

Následující graf ukazuje využití procesoru využití prostředků databáze úrovně Premium s úrovní výkonu P2 pro každou hodinu za týden. Tento graf začíná v pondělí, zobrazuje 5 pracovních dní a poté zobrazí víkend, když se v aplikaci stane mnohem méně.

![Využití prostředků databáze SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Z dat, tato databáze má aktuálně zatížení CPU ve špičce než 50 procent využití procesoru vzhledem k úrovni výkonu P2 (poledne úterý). Pokud využití procesoru je dominantní faktoru v profilu aplikace prostředků, můžete se rozhodnout, že P2 je úroveň výkonu správné zaručí, že zatížení vždy vyhovovat. Pokud očekáváte, že aplikace čase, je dobré mít vyrovnávací paměť dodatečný prostředek, takže aplikace nebude nikdy dosáhne limitu úroveň výkonu. Pokud zvýšíte úroveň výkonu, můžete vyhnout zákazníky viditelné chyby, které mohou nastat, když databáze nemá dostatek výkonné nástroje pro zpracování žádostí efektivně, zejména v prostředích nízkou latenci. Příkladem je databáze, která podporuje aplikace, která jsou vykreslovány webové stránky na základě výsledků volání databáze.

Ostatní typy aplikací může do stejného grafu interpretují odlišně. Například pokud se aplikace pokusí ke zpracování mezd dat každý den a má stejný graf, tento druh "úlohy služby batch" model může provádět bez problémů na úroveň výkonu P1. Úroveň výkonu P1 má 100 Dtu ve srovnání s 200 jednotek Dtu na úrovni výkonu P2. Úroveň výkonu P1 poskytuje poloviční výkon úroveň výkonu P2. Tedy 50 % využití procesoru v P2 se rovná 100 % využití procesoru v P1. Pokud aplikace nemá žádné vypršení časových limitů, nemusí je důležité, pokud úlohy trvá 2 hodiny nebo 2,5 hodin, pokud to proběhne ještě dnes. Aplikace v této kategorii pravděpodobně můžete použít úroveň výkonu P1. Můžete využít výhod vzhledem k tomu, že mají dobu během dne, kdy je využití prostředků nižší, tak, aby všechny "velký objem ve špičce" může být uložená do jedné ze žlaby později za den. Jako včasné každý den může dokončení úloh, může být vhodné pro tento typ aplikace (a ušetřit peníze), úroveň výkonu P1.

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
2. Můžete vyhodnotit, jak dobře vaše úloha vyhovuje úroveň výkonu, budete muset přejít na každý aspekt metriky prostředků: procesor, čtení, zápisu, počet pracovních procesů a počet relací. Tady je upravená dotazování pomocí **sys.resource_stats** hlášení průměrné a maximální hodnoty tyto metriky prostředků:
   
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
3. Pomocí těchto informací o průměrné a maximální hodnoty každého prostředku metriky můžete vyhodnotit, jak dobře vaše úloha zapadá do úrovní výkonu, kterou jste zvolili. Obvykle, průměrné hodnoty z **sys.resource_stats** umožňují dobré směrný plán použít proti cílovou velikost. Měla by být stonek vaše primární měření. Příklad může být použití úrovně služeb Standard s úrovní výkonu S2. Průměr použít procenta využití procesoru a vstupně-výstupních operací čtení a zápisy jsou pod 40 procent, průměrný počet pracovních procesů, které je nižší než 50 a průměrný počet relací je nižší než 200. Úlohy je možné umístit do úrovní výkonu S1. Je snadno zjistit, zda databáze vejde omezení pracovních procesů a relace. Abyste viděli, zda databáze zapadá do nižší úroveň výkonu s ohledem na využití procesoru, čtení a zápisu, počet DTU nižší úrovně výkonu podle počtu jednotek DTU vaše aktuální úroveň výkonu a výsledek vynásobit 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Výsledkem je relativní výkon rozdíl mezi dvěma výkonu úrovně v procentech. Pokud využívání prostředků není překročí tuto hodnotu, je možné umístit vašich úloh s nižší úrovní výkonu. Ale budete muset podívejte se na všechny rozsahy hodnot použití prostředků a zjistit, v procentech, jak často by přizpůsobit databázové úlohy s nižší úrovní výkonu. Následující dotaz vypíše ve výstupu přizpůsobit procento prostředků dimenze, podle prahové hodnoty 40 procent vypočtené v tomto příkladu:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Podle vaší databáze cíle na úrovni služby (SLO), můžete rozhodnout, zda vaše úloha zapadá do nižší úroveň výkonu. Pokud je vaší úlohy databáze SLO 99, 9procentní a předchozí dotaz vrátí hodnoty větší než 99, 9procentní pro všechny tři prostředků dimenze, úloha pravděpodobně zapadá do nižší úroveň výkonu.
   
    Prohlížení přizpůsobit procento poskytuje také přehled o tom, zda byste měli přejít na další vyšší úroveň výkonu pro splnění vašich cíle na úrovni služby. Například userdb1 znázorňuje následující využití procesoru za minulý týden:
   
   | Průměrné procento CPU | Maximální procento využití procesoru |
   | --- | --- |
   | 24.5 |100.00 |
   
    Průměrné využití procesoru je o čtvrtletí limit úroveň výkonu, které bude vyhovovat úrovně výkonu databáze. Ale maximální hodnota ukazuje, že databáze dosáhne limitu úroveň výkonu. Je třeba přesunout na další vyšší úroveň výkonu? Podívejte se na postup v mnoha případech vaše zatížení dosáhne 100 % jeho obsahu a pak porovnat s vaší úlohy databáze cíle na úrovni služby.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Pokud tento dotaz vrací hodnotu menší než 99, 9procentní pro libovolný počet rozměrů tři zdroje, zvažte Přesun na další vyšší úroveň výkonu nebo používat techniky ladění aplikace ke snížení zatížení na databázi SQL.
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

## <a name="next-steps"></a>Další postup

- Automaticky vyladit indexy databáze a dotazování pomocí plánům spuštění [automatické ladění Azure SQL Database](sql-database-automatic-tuning.md).
- Monitorování výkonu databáze automaticky pomocí [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md). Tato funkce poskytuje diagnostické informace a hlavní příčina analýzy problémů s výkonem.
