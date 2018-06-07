---
title: Monitorování výkonu databáze ve službě Azure SQL Database | Dokumentace Microsoftu
description: Seznamte se s možnostmi monitorování vaší databáze pomocí nástrojů Azure a zobrazení dynamické správy.
keywords: monitorování databáze, výkon cloudové databáze
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 44d68d69a7034e80846fb44f3ae26c0d73c61f28
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648305"
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Monitorování výkonu databáze ve službě Azure SQL Database
Monitorování výkonu databáze SQL v Azure začíná sledováním využití prostředků relativně ke zvolené úrovni výkonu databáze. Monitorování vám pomůže určit, zda vaše databáze má přebytečnou kapacitou, nebo má potíže s, protože prostředky jsou podle toho, se a pak rozhodnout, zda je čas změnit úroveň výkonu a vaše databáze v úrovních služeb [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) nebo [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md). Databázi můžete monitorovat pomocí grafických nástrojů na [portálu Azure](https://portal.azure.com) nebo pomocí [zobrazení dynamické správy SQL](https://msdn.microsoft.com/library/ms188754.aspx).

> [!TIP]
> Použití [inteligentního Statistika SQL Azure](sql-database-intelligent-insights.md) pro automatické monitorování výkon databáze. Jakmile se zjistí problém s výkonem, diagnostiky protokol je generovaný s podrobnostmi a kořenové příčina Analysis (RCA) problému. Doporučení pro zlepšení výkonu je zadat, pokud je to možné.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorování databází na portálu Azure
Na [portálu Azure](https://portal.azure.com/) můžete monitorovat využití izolované databáze jednoduše tak, že vyberete databázi a kliknete na graf **Monitorování**. Zobrazí se okno **Metrika**, které můžete upravit kliknutím na **Upravit graf**. Přidejte následující metriky:

* Procento CPU
* Procento DTU
* Procento datových V/V
* Procento velikosti databáze

Jakmile přidáte tyto metriky, můžete pokračovat jejich zobrazením v **monitorování** grafu s dalšími informacemi na **metrika** okno. Tyto čtyři metriky uvádějí průměrné využití v procentech vzhledem k hodnotě **DTU** vaší databáze. Najdete v článku [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md) články pro další informace o úrovních služeb.  

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

### <a name="monitor-resource-use"></a>Sledování využití prostředků

Můžete monitorovat využití prostředků pomocí [SQL databáze Query Performance Insight](sql-database-query-performance.md) a [úložiště dotazů](https://msdn.microsoft.com/library/dn817826.aspx).

Můžete také sledovat využití pomocí těchto dvou zobrazení:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Můžete použít [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) zobrazení v každé databázi SQL. **Sys.dm_db_resource_stats** zobrazení ukazuje poslední data použití prostředků relativně k vrstvě služby. Průměrná procenta využití procesoru, vstupně-výstupní data, zápisy protokolu a paměti se zaznamenávají každých 15 sekund a jsou uchovávány 1 hodina.

Protože toto zobrazení nabízí podrobnější pohled na využití prostředků, použijte **sys.dm_db_resource_stats** první pro nějakou analýzu aktuální stav nebo řešení potíží. Například tento dotaz zobrazí průměrnou a maximální prostředky používané pro aktuální databázi přes poslední hodinu:

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

Pro jiné dotazy, podívejte se na příklady v [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

#### <a name="sysresourcestats"></a>sys.resource_stats
[Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) zobrazit v **hlavní** databáze obsahuje další informace, které můžete sledovat výkon vaší databázi SQL na úrovni konkrétní službu a výkonu. Data se shromažďují pro každých 5 minut a bude zachována pro účely přibližně 14 dnů. Toto zobrazení je užitečné pro dlouhodobější analýzu historie používání prostředků vaší databázi SQL.

Následující graf ukazuje procesoru využití prostředků pro databáze Premium P2 úrovní výkonu pro každou hodinu v týdnu. Tento graf začíná v pondělí, zobrazuje 5 pracovních dní a poté zobrazí víkendu, když se stane mnohem méně na aplikaci.

![Využití prostředků databáze SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Z dat, má tato databáze aktuálně zatížení procesoru ve špičce právě víc než 50 % využití procesoru relativně k úroveň výkonu P2 (poledne úterý). Pokud procesor dominantní hrají roli v profilu aplikace prostředků, může rozhodnout, že P2 je na úroveň výkonu správné zaručit, že zatížení vždy vyhovuje. Pokud očekáváte aplikace v čase, je vhodné mít vyrovnávací paměť navíc prostředků tak, aby aplikace nebude nikdy dosáhne omezení úroveň výkonu. Pokud zvýšíte úroveň výkonu, můžete pomoct vyhnout zákazníka viditelné chybách, ke kterým může dojít, když databáze nemá dostatek power zpracovávat požadavky efektivně, zejména v prostředích citlivý na latenci. Příkladem je databáze, která podporuje aplikace, která vybarví webové stránky, na základě výsledků volání databáze.

Jinými typy aplikací odlišně může přeložit do stejného grafu. Například pokud aplikace pokusí zpracovat data mzdy každý den a má stejné grafu, tento druh modelu "dávkovou úlohu" může provádět bez problémů na úroveň výkonu P1. Úroveň výkonu P1 má 100 Dtu ve srovnání s 200 Dtu na úrovni výkonu P2. Úroveň výkonu P1 poskytuje poloviční výkon P2 úroveň výkonu. Ano 50 procent hodnoty využití procesoru v P2 rovná 100 procent využití procesoru v P1. Pokud aplikace nemá vypršení časových limitů, nemusí je důležité, pokud úloha trvá 2 hodiny nebo 2,5 hodin, pokud získá dnes Hotovo. Aplikace v této kategorii pravděpodobně můžete použít úroveň výkonu P1. Můžete využít výhod skutečnost, že jsou dobu během dne, kdy je využití prostředků nižší, tak, aby všechny "velký ve špičce" může distribuována do jedné žlaby později v den. Úroveň výkonu P1 může být vhodné pro tento typ aplikace (a uložte peníze), tak dlouho, dokud na čas každý den může dokončení úlohy.

Azure SQL Database zpřístupňuje využívat informace o prostředcích pro každou aktivní databáze v **sys.resource_stats** zobrazení **hlavní** databází v každém serveru. Data v tabulce se shromažďují pro 5 minutách. S úrovně služeb Basic, Standard a Premium dat může trvat déle než 5 minut, než se objeví v tabulce, tak, aby tato data užitečnější pro historické analýzy, nikoli analysis téměř v reálném čase. Dotaz **sys.resource_stats** zobrazení zobrazíte nejnovější historii databáze a k ověření, jestli rezervace zvolíte doručit výkonu, které chcete v případě potřeby.

> [!NOTE]
> Musíte být připojeni k **hlavní** databáze logické databáze serveru SQL pro dotaz **sys.resource_stats** v následujících příkladech.
> 
> 

Tento příklad ukazuje, jak je vystaven data v tomto zobrazení:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Zobrazení katalogu sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

Další příklad ukazuje, různé způsoby, které můžete použít **sys.resource_stats** katalogu zobrazení a získat informace o používání prostředků vaší databázi SQL:

1. Podívat se na minulého týdne prostředků použít pro userdb1 databáze, můžete spustit tento dotaz:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Pokud chcete vyhodnotit, jak dobře vaše úlohy vyhovuje úroveň výkonu, je potřeba k podrobnostem každý aspekt metrika prostředků: procesoru, čtení, zápisu, počet pracovních procesů a počet relací. Tady je revidované dotazování pomocí **sys.resource_stats** nahlásit průměrný a maximální hodnoty metrik těchto prostředků:
   
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
3. Tyto informace o průměrnou a maximální hodnoty každého prostředku metriky a můžete vyhodnotit, jak dobře vaše úlohy zapadá do úroveň výkonu, které jste zvolili. Obvykle, průměrná hodnoty z **sys.resource_stats** poskytují dobrý směrného plánu používat pro cílovou velikost. Mělo by být váš primární měření Flash disk. Příklad by mohla využívat vrstvě služby na úrovni Standard S2 úroveň výkonu. Průměr pomocí procenta pro procesor a vstupně-výstupní operace čtení a zápisů jsou pod 40 procent, průměrný počet pracovních procesů je menší než 50 a průměrný počet relací, které je nižší než 200. Vaše zatížení může začlenit do úrovní výkonu S1. Je snadno zjistit, jestli vaše databáze se vejde limity pracovního procesu a relace. Pokud chcete zobrazit, zda databáze zapadá do nižší úroveň výkonu s ohledem na využití procesoru, čte a zápisu a dělit počet jednotek DTU na nižší úroveň výkonu podle počtu jednotek DTU vaše aktuální úroveň výkonu a výsledek vynásobit 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Výsledkem je relativní výkon rozdíl mezi úrovněmi dvě výkonu v procentech. Pokud vaše využití prostředků nepřekročí toto množství, může vaše úlohy začlenit do nižší úroveň výkonu. Však musíte vyhledat všechny rozsahy hodnot použití prostředků a zjistit, v procentech, jak často by se vešla vaše databáze úlohy s nižší úrovní výkonu. Následující dotaz vypíše shody procento na dimenzi prostředků, podle prahové hodnoty 40 procent vypočtené v tomto příkladu:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Podle vaší databáze cíle na úrovni služby (SLO), můžete rozhodnout, zda vyhovuje vaše úlohy s nižší úrovní výkonu. Pokud vaše databáze úlohy SLO je 99,9 % a předchozí dotaz vrátí hodnoty vyšší než 99,9 % pro všechny tři prostředků dimenze, vaše úlohy pravděpodobně zapadá do nižší úroveň výkonu.
   
    Prohlížení shody procento také získáte přehled o tom, jestli měli byste přejít na další vyšší úroveň výkonu na splňují vaše SLO. Například userdb1 uvádí následující využití procesoru za uplynulý týden:
   
   | Průměrné využití procesoru v procentech | Maximální procento využití procesoru |
   | --- | --- |
   | 24.5 |100.00 |
   
    Průměrné využití procesoru je o čtvrtletí omezení úrovně výkonu, které by se vešla do úroveň výkonu databáze. Ale maximální hodnota ukazuje, že databáze dosáhne limitu úrovně výkonu. Je třeba přesunout do další vyšší úroveň výkonu? Podívejte se na to, jak tolikrát, kolikrát vaše úlohy dosáhnou 100 procent a porovnejte je s vaše databáze úlohy SLO.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Pokud tento dotaz vrací hodnotu menší než 99,9 % pro některá z dimenzí tři prostředků, zvažte přechod na další vyšší úroveň výkonu nebo snížení zatížení databáze SQL pomocí techniky optimalizace pro aplikace.
4. Tento postup také zvažuje zvýšení vaší předpokládané úlohy v budoucnu.

U elastických fondů můžete monitorovat jednotlivé databáze ve fondu pomocí technik popsaných v této části. Můžete ale také monitorovat fond jako celek. Další informace najdete v tématu [Monitorování a správa elastického fondu](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Maximální souběžných požadavků
Pokud chcete zjistit počet souběžných požadavků, spusťte tento dotaz jazyka Transact-SQL ve vaší databázi SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Chcete-li analyzovat zatížení místní databáze systému SQL Server, upravte tento dotaz pro filtrování v konkrétní databázi, že které chcete analyzovat. Například pokud máte místní databázi s názvem databáze, tento dotaz jazyka Transact-SQL vrátí počet souběžných požadavků v databázi:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Toto je právě snímku na jednom místě v čase. Chcete-li získat lepší pochopení zatížení a požadavky na počtu souběžných požadavků, budete muset shromažďovat mnoha ukázek v čase.

### <a name="maximum-concurrent-logins"></a>Maximální souběžných přihlášení
Můžete analyzovat vašim vzorům uživatelů a aplikací, kde získáte představu četnost přihlášení. Skutečné zatížení taky můžete spustit v testovacím prostředí, abyste měli jistotu, že nejsou stiskne to nebo další omezení, které v tomto článku probereme. Není k dispozici jeden dotaz nebo zobrazení dynamické správy (DMV), která umožňuje zobrazit souběžných, že počty přihlášení nebo historie.

V případě, že více klientů používat stejný připojovací řetězec, ověří služba každé přihlášení. Pokud 10 uživatelů najednou připojit k databázi pomocí stejné uživatelské jméno a heslo, by 10 souběžných přihlášení. Toto omezení se vztahuje pouze na dobu trvání přihlášení a ověřování. Pokud stejný 10 uživatelé připojit k databázi postupně, počet souběžných přihlášení by být nikdy větší než 1.

> [!NOTE]
> V současné době toto omezení se nevztahuje k databázím v elastické fondy.
> 
> 

### <a name="maximum-sessions"></a>Maximální počet relací
Pokud chcete zjistit počet aktuální aktivních relací, spusťte tento dotaz jazyka Transact-SQL ve vaší databázi SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Pokud při analýze pracovního vytížení místní systém SQL Server, změňte dotaz a zaměřit se na konkrétní databáze. Tento dotaz vám pomůže určit, je možné relace pro databázi Pokud uvažujete o ani ji přesunout do Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Tyto dotazy znovu, vrátí počet bodu v čase. Pokud shromažďujete více ukázky v čase, budete mít nejlepší Principy relace použít.

Pro analýzu databáze SQL, můžete získat historická statistiky u relací pomocí dotazu [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) zobrazení a kontrola **active_session_count** sloupce. 

## <a name="next-steps"></a>Další postup

- Automaticky optimalizovat indexy databáze a spuštění plány pomocí dotazu [Azure SQL Database automatické ladění](sql-database-automatic-tuning.md).
- Monitorování výkonu databáze automaticky pomocí [inteligentního Statistika SQL Azure](sql-database-intelligent-insights.md). Tato funkce poskytuje diagnostické informace a příčina analýzu problémy s výkonem.
