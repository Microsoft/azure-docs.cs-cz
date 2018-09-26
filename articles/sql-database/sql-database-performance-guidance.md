---
title: Průvodce laděním výkonu Azure SQL Database | Dokumentace Microsoftu
description: Další informace o použití doporučení k vylepšení výkonu dotazů Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: 283d27e330b7e1defb34196279693b5b5a7221df
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160583"
---
# <a name="tuning-performance-in-azure-sql-database"></a>Optimalizace výkonu ve službě Azure SQL Database

Azure SQL Database poskytuje [doporučení](sql-database-advisor.md) , můžete použít ke zlepšení výkonu vaší databáze nebo můžete nechat Azure SQL Database [automaticky přizpůsobit vaší aplikace](sql-database-automatic-tuning.md) a provést změny, které pomůže zvýšit výkon vašich úloh.

V které nemáte žádná doporučení k dispozici a stále máte problémy s výkonem, můžete použít následující metody k vylepšení možnosti:
- Zvýšení úrovně služeb v vaše [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nebo [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) poskytnout další zdroje k vaší databázi.
- Ladění aplikací a použít některé osvědčené postupy, které může zlepšit výkon. 
- Optimalizujte databázi tak, že změníte indexy a dotazy, které efektivněji pracovat s daty.

Toto jsou ruční metody, protože musíte se rozhodnout množství prostředků splňují vaše požadavky. V opačném případě by potřeba revize aplikace nebo kódu databáze a změny nasazení.

## <a name="increasing-servicce-tier-of-your-database"></a>Zvýšení úrovně servicce vaší databáze

Azure SQL Database nabízí dva modely nákupu, [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) ze kterých si můžete vybrat. Každá úroveň služby výhradně izoluje prostředky, můžete použít SQL database a zajišťuje předvídatelný výkon úrovně služby. V tomto článku nabízíme pokyny, které vám můžou pomoct vybrat úroveň služby pro vaši aplikaci. Probereme také způsoby, jak vyladit aplikaci k plnému využití ze služby Azure SQL Database.

> [!NOTE]
> Tento článek se zaměřuje na pokyny k výkonu pro izolované databáze ve službě Azure SQL Database. Průvodce výkonem související s elastickými fondy, najdete v části [cenové a výkonové požadavky elastických fondů](sql-database-elastic-pool-guidance.md). Všimněte si však, že můžete použít řadu doporučení pro vyladění v tomto článku pro databáze v elastickém fondu a získat podobné výhody výkonu.
> 

* **Základní**: základní služby úroveň nabízí dobrý výkon předvídatelnost pro každou databázi, hodinu za hodinu. V databázi Basic podporují dostatek prostředků dobrý výkon v malé databáze, který nemá více souběžných požadavků. Typické případy použití když použijete úroveň služeb Basic jsou:
  * **Právě začínáte s Azure SQL Database**. Aplikace, které jsou ve vývoji často není nutné vysoce rozsáhlých výpočetních prostředků velikosti. Základní databáze jsou ideální prostředí pro vývoj databází nebo testování za nízkou cenu.
  * **Když máte databázi s jedním uživatelem**. Aplikace, které u jednoho uživatele přidružit databázi obvykle nemají vysoké požadavky na souběžnost a výkon. Tyto aplikace jsou kandidáty na úrovni služby Basic.
* **Standardní**: standardní úroveň služeb nabízí lepší výkon, předvídatelnost a poskytuje dobrý výkon u databází, které mají více souběžných žádostí, jako je pracovní skupiny a webových aplikací. Když vyberete databázi úroveň služeb Standard, můžete měnit velikost vaší databáze aplikace založená na předvídatelný výkon, minut za minutu.
  * **Vaše databáze má více souběžných požadavků**. Aplikace, které služby více než jeden uživatel obvykle současně potřebují vyšší velikostí výpočetních. Například pracovní skupině nebo webové aplikace, které nízké nároky na provoz střední vstupně-výstupní operace podporuje více současných dotazů jsou vhodnými kandidáty pro úrovně služeb Standard.
* **Premium**: úrovně služeb Premium poskytuje předvídatelný výkon, druhý za sekundu, pro každou databázi, Premium nebo pro důležité obchodní informace. Při výběru úrovně služeb Premium, můžete měnit velikost databáze aplikaci podle zatížení ve špičce pro tuto databázi. Tento plán odebere případy, ve které výkonu může způsobit variance malé dotazy trvá déle, než se očekávalo v operacích nízkou latenci. Tento model může výrazně zjednodušit vývoj a produktu cykly ověření pro aplikace, které je potřeba vytvořit silné příkazy týkající se potřebné prostředky ve špičce, odchylka výkonu nebo latence dotazu. Většina případy použití služby úrovně Premium mají jeden nebo více těchto vlastností:
  * **Vysoká vrcholového zatížení**. Aplikace, která vyžaduje podstatné CPU, paměť či vstup/výstup (IO) k dokončení jeho operace vyžaduje velikost vyhrazené, vysoce rozsáhlých výpočetních prostředků. Například databázová operace známé využívat více jader procesoru po delší dobu je Release candidate úrovně Premium.
  * **Velký počet souběžných požadavků**. Některé databáze aplikace služby velký počet souběžných požadavků, například když obsluhující web, který má vysokou návštěvností svazku. Základní a standardní úrovní služeb omezit počet souběžných požadavků na databázi. Aplikace, které vyžadují další připojení by bylo potřeba zvolte příslušné rezervace pro zpracování maximální počet potřebných požadavků.
  * **Nízká latence**. Některé aplikace potřebují k zajištění minimální časovou odezva z databáze. Pokud konkrétní uložené procedury je volána v rámci širší operace zákazníka, bude pravděpodobně potřeba mít návrat z tohoto volání ve více než 20 milisekund a 99 % času. Tento typ aplikace těží z úrovně služeb Premium, abyste měli jistotu, že je k dispozici požadované výpočetní výkon.

Úroveň služby, které potřebujete pro svou databázi závisí na požadavcích zatížení ve špičce pro jednotlivé rozměry prostředku. Některé aplikace pomocí jednoduchého dotazu množství jeden prostředek, ale mají významný požadavky pro jiné prostředky.

### <a name="service-tier-capabilities-and-limits"></a>Možnosti úrovně služby a omezení

Na jednotlivých úrovních služby nastavíte velikost výpočetního tak můžete flexibilně platíte jenom za kapacitu, kterou potřebujete. Je možné [upravit kapacitu](sql-database-service-tiers-dtu.md), směrem nahoru nebo dolů, měnící se zatížení. Například pokud vaší úlohy databáze je vysoká během nákupní sezóny zpátky do školy, může zvýšit velikost výpočetní prostředky pro databázi nastavit dobu, červenec prostřednictvím září. Ho můžete snížit, až skončí období vaší ve špičce. Můžete minimalizovat platíte díky optimalizaci vašeho cloudového prostředí pro sezónnosti vašeho podnikání. Tento model také funguje dobře pro cykly vydávání verzí produktu. Testovací tým může přidělit kapacitu během testovacích běhů a uvolněte kapacitu této po dokončení testování. V požadavku kapacitního modelu platíte za kapacitu podle potřeby ho a vyhněte se výdaje na vyhrazených prostředcích, které můžou používat jen zřídka.

### <a name="why-service-tiers"></a>Proč úrovně služeb?
I když každá úloha databáze se může lišit, k poskytování předvídatelnost výkonu při různých velikostech výpočetních je cílem úrovně služeb. Zákazníci s požadavky na prostředky ve velkém měřítku databáze můžete pracovat ve více vyhrazených výpočetního prostředí.

## <a name="tune-your-application"></a>Ladění aplikací
V tradiční místní SQL Server je proces plánování kapacity počáteční často oddělené od procesu spuštění aplikace v produkčním prostředí. Hardware a produktu licence se kupují nejprve a optimalizace výkonu je provést později. Pokud používáte Azure SQL Database, je vhodné interweave proces spuštění aplikace a jeho vylaďování. S modelem placení za kapacitu na vyžádání můžete ladit aplikace použije minimální prostředky, které potřebuje teď místo předimenzovaných na hardwaru podle pokusů plánů budoucímu růstu pro aplikace, které často nejsou správné. Zákazníci, kteří může rozhodnout optimalizovat aplikace a místo toho se rozhodnete cenu značných nákladů prostředky hardwaru. Tento přístup může být vhodné, pokud nechcete změnit klíče aplikace zaneprázdněný období. Ale ladění aplikace můžete minimalizovat nároky na prostředky a nižší faktury měsíční při použití úrovně služeb ve službě Azure SQL Database.

### <a name="application-characteristics"></a>Vlastnosti aplikace
I když úrovně služby Azure SQL Database jsou navržené ke zlepšení výkonu stabilitu a předvídatelnost pro aplikaci, můžete některé osvědčené postupy vyladit vaše aplikace lépe využívat prostředky na výpočty velikosti. I když mnohé aplikace mají výrazné zvýšení výkonu jednoduše tak, že přepnutí na vyšší výpočetní velikost nebo úroveň služby, některé aplikace potřebují další doladění, abyste využili výhod vyšší úroveň služby. Pro zvýšení výkonu vezměte v úvahu další aplikační ladění pro aplikace, které mají tyto charakteristiky:

* **Aplikace, které mají nízký výkon kvůli "příliš upovídaným" chování**. Přetížení aplikace provést operacemi přístupu k datům nadměrné, které jsou citlivé na latenci sítě. Můžete třeba upravit tyto druhy aplikací a snížit počet operací přístupu k datům do služby SQL database. Například může zlepšit výkon aplikace pomocí postupů, jako jsou dávkové zpracování dotazů ad hoc nebo při přenosech dotazy na uložené procedury. Další informace najdete v tématu [dávkové dotazy](#batch-queries).
* **Databáze s náročné úlohy, který nemůže být podporována celý jeden počítač**. Databáze, které překračují prostředky nejvyšší úrovně Premium výpočtu velikosti může mít užitek z horizontální navýšení kapacity zatížení. Další informace najdete v tématu [horizontálního dělení mezidatabázové](#cross-database-sharding) a [funkční dělení](#functional-partitioning).
* **Aplikace, které mají neoptimální dotazy**. Aplikace, zejména v vrstvy přístupu k datům, který máte špatně vyladěný dotazy nemusí těžit z vyšší velikost výpočetní prostředky. To zahrnuje dotazy, které nemají klauzule WHERE, chybí indexy nebo zastaralé statistiky. Tyto aplikace využít techniky ladění výkonu standardního dotazu. Další informace najdete v tématu [chybějící indexy](#identifying-and-adding-missing-indexes) a [dotazování ladění a zobrazování rad](#query-tuning-and-hinting).
* **Aplikace, které mají neoptimální data přístup k návrhu**. Aplikace, které mají vlastní data access souběžnosti problémy, například vzájemné zablokování, nemusí využívat vyšší velikost výpočetní prostředky. Zvažte snížení odezev databázi SQL Azure pomocí ukládání do mezipaměti dat na straně klienta pomocí služby mezipaměti Azure nebo jinou technologií ukládání do mezipaměti. Zobrazit [ukládání do mezipaměti aplikace úroveň](#application-tier-caching).

## <a name="tune-your-database"></a>Ladit vaši databázi
V této části se podíváme na některé techniky, které můžete použít k vyladění Azure SQL Database a získat lepší výkon vaší aplikace a provést spuštění na nejnižší možné výpočetní velikost. Některé z těchto postupů odpovídá tradiční ladění osvědčené postupy serveru SQL Server, ale ostatní jsou specifické pro Azure SQL Database. V některých případech můžete zkoumat spotřebovaných prostředků, aby databáze mohla najít oblastí a dále ladit a rozšíření tradičních technik systému SQL Server pro práci ve službě Azure SQL Database.

### <a name="identify-performance-issues-using-azure-portal"></a>Identifikace problémů s výkonem pomocí webu Azure portal
Následující nástroje na webu Azure Portal můžete analyzovat a opravit problémy s výkonem s využitím SQL database:

* [Query Performance Insight](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

Na webu Azure portal obsahuje další informace o obou těchto nástrojů a jejich použití. Efektivně diagnostikovat a opravit problémy, doporučujeme nejprve vyzkoušet nástroje se na webu Azure Portal. Doporučujeme použít ruční ladění přístupů, které probereme v dalším kroku chybějících indexů a optimalizaci dotazu ve zvláštních případech.

Další informace o identifikovat případné potíže ve službě Azure SQL Database na [Performance monitoring pro aplikace na webu Azure Portal](sql-database-monitor-tune-overview.md) a [monitorování databází pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md) článků.

### <a name="identifying-and-adding-missing-indexes"></a>Identifikace a přidání chybějících indexů
Běžným problémem v výkonu databáze OLTP vztahuje se k návrhu fyzická databáze. Databázová schémata jsou často navržené a dodán bez testování ve velkém měřítku (buď v načtení nebo objemu dat). Bohužel výkonu plán dotazu může být přijatelný v malém měřítku, ale podstatně snížit pod provozní úrovni datové svazky. Nejběžnější příčiny tohoto problému je chybějící vhodné indexy, které splňují filtry nebo jiná omezení v dotazu. Chybějící indexy manifesty jako tabulku skenování často, při hledání indexu může stačit.

V tomto příkladu plán vybraný dotaz používá kontrolu, když bude stačit hledání:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Plán dotazu s chybějící indexy](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database můžete najít a opravit běžné chybí indexu podmínky. Podívejte se na dotaz kompilace, ve kterých by indexu výrazně snížit odhadované náklady na spuštění dotazu zobrazení dynamické správy, které jsou integrované do Azure SQL Database. Při provádění dotazu SQL Database sleduje jak často spouští každý plán dotazu a sleduje odhadované mezery mezi plán provádění dotazu a imagined kde indexu existoval. Vám pomůže tyto zobrazení dynamické správy rychle odhadnout změny do svého návrhu fyzická databáze může zlepšit celkové náklady na úlohy pro databázi a její skutečné pracovní vytížení.

Vyhodnotit potenciální chybějících indexů, můžete pomocí tohoto dotazu:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

V tomto příkladu je výsledkem dotazu tohoto návrhu:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Po jeho vytvoření, vybere tento stejný příkaz SELECT jiný plán, který používá hledání namísto kontroly a efektivněji pak provede plánu:

![Plán dotazu s opravený indexy](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Klíčový přehled je, že je kapacita v/v systému sdílené, komoditním omezenější než počítač vyhrazený server. Není na minimalizovat zbytečné vstupně-výstupních operací maximální využívat systém v DTU jednotlivých velikost výpočetní úrovně služby Azure SQL Database na úrovni premium. Návrh fyzické databázi volby může výrazně zlepšit latenci pro jednotlivé dotazy, zlepšit propustnost souběžných požadavků zpracovaných za škálovací jednotku a minimalizovat náklady nutné k uspokojení dotazu. Další informace o chybějící index zobrazení dynamické správy najdete v tématu [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Optimalizace dotazů a Rady
Optimalizace dotazů ve službě Azure SQL Database je podobný tradiční optimalizátoru dotazů systému SQL Server. Většina osvědčených postupů pro optimalizaci dotazů a pochopení důvody, proč modelu omezení optimalizátoru dotazů platí také pro Azure SQL Database. Pokud je optimalizace dotazů ve službě Azure SQL Database, může se zobrazit navíc můžete využít snižuje požadavky na prostředky agregace. Vaše aplikace může být schopni spustit s nižšími náklady než naladěného ekvivalentní, protože může probíhat na nižší výpočetní velikost.

Příklad, který je běžné v systému SQL Server, a to platí i pro Azure SQL Database je způsob optimalizace dotazů "zachytává obsah" parametry. Během kompilace optimalizace dotazů vyhodnotí jako aktuální hodnoty parametru k určení, zda můžete generovat více optimální plán dotazu. I když tato strategie často může mít za následek plán dotazu, který je výrazně rychlejší než plán zkompiloval bez parametru známé hodnoty, aktuálně funguje imperfectly i v systému SQL Server a ve službě Azure SQL Database. Někdy není parametr zachycení a někdy zachycení parametr ale vygenerovaný plán je neoptimální pro úplnou sadu hodnot parametrů v zatížení. Microsoft obsahuje pomocné parametry dotazu (direktivy), takže můžete určit záměr více záměrně a přepsat výchozí chování pro analýzu sítě parametru. Často Pokud používáte pomocné parametry, můžete vyřešit případy, ve kterých je výchozí chování systému SQL Server nebo Azure SQL Database dokonalé pro konkrétního zákazníka zatížení.

Následující příklad ukazuje, jak procesor dotazů můžete generovat plán, který je neoptimální pro výkon a požadavky na prostředky. Tento příklad také ukazuje, že pokud používáte pomocný parametr dotazu, můžete snížit požadavky na spuštění dotazu času a prostředků pro vaši databázi SQL:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

Instalační kód vytvoří tabulku, která se má zkosený distribuci dat. Plán dotazu optimální liší v závislosti na vybrané který parametr. Bohužel plán chování ukládání do mezipaměti není vždy znovu zkompilovat na základě nejčastěji používané hodnoty parametru dotazu. Ano je možné neoptimální plánu v mezipaměti a použít pro více hodnot, i v případě, že jiný plán může být vhodnější použít plán v průměru. Vytvoří plán dotazu dvě uložené procedury, které jsou identické, s tím rozdílem, že jedna má speciální dotaz nápovědu.

**Příklad, část 1**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Příklad, část 2**

(Doporučujeme Počkejte minimálně 10 minut, než začnete, část 2 příkladu tak, že výsledky jsou jedinečná ve výsledné telemetrická data.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Každá část v tomto příkladu se pokusí o spuštění příkazu parametrizované insert 1000krát (ke generování dostatečné zatížení chcete použít jako sada testovacích dat.). Při provádění uložené procedury, procesor dotazů zkontroluje hodnotu parametru, který je předán do procedury během jeho prvního kompilace (parametr "pro analýzu sítě"). Procesor výsledný plán ukládá do mezipaměti a použije ho pro pozdější volání, i v případě, že hodnota parametru se liší. Ve všech případech nemusí použít optimální plán. Někdy potřebujete průvodce Optimalizátor vybrat plán, který je lepší pro průměrný případ spíše než konkrétní případ od kdy byl dotaz nejprve zkompilován. V tomto příkladu vygeneruje počáteční plán "kontrola" plán, který načte všechny řádky k vyhledání všech hodnot, které odpovídá parametru:

![Dotazování ladění s použitím plánu kontroly](./media/sql-database-performance-guidance/query_tuning_1.png)

Protože jsme spouštěli postup pomocí hodnoty 1, výsledný plán bylo ideální pro hodnotu 1, ale byla neoptimální pro všechny ostatní hodnoty v tabulce. Výsledek pravděpodobně není co vhodné, pokud byste chtěli vyberte každý plán náhodně, protože v plánu provádí pomaleji a využívá více prostředků.

Pokud spustíte test s `SET STATISTICS IO` nastavena na `ON`, logického prohledávání práce v tomto příkladu se provádí na pozadí. Uvidíte, že jsou 1,148 čtení provádí plánu (což je neefektivní, pokud průměrná jsou vrátit jen jeden řádek):

![Dotazování s použitím logického prohledávání ladění](./media/sql-database-performance-guidance/query_tuning_2.png)

Druhá část v příkladu používá pomocný parametr dotazu říct Optimalizátor, aby použil konkrétní hodnoty během kompilace. V tomto případě vynutí procesor dotazů ignorovat hodnotu, která je předána jako parametr, a místo toho předpokládat, že `UNKNOWN`. To se vztahuje na hodnotu, která má průměrnou četnost v tabulce (ignorování Nerovnoměrná distribuce). Hledání podle plánu, který je rychlejší a používá méně prostředků v průměru, než plán v části 1 tohoto příkladu je výsledný plán:

![Vyladění dotazů s použitím pomocného parametru dotazu](./media/sql-database-performance-guidance/query_tuning_3.png)

Zobrazí se tento efekt v **sys.resource_stats** tabulky (dochází ke zpoždění od okamžiku spuštění testu a kdy data naplní tabulky). Pro tento příklad, část 1 provést během časového intervalu 22:25:00 a 2. část provést ve 22:35:00. Starší časový interval použít další prostředky v tomto časovém intervalu než novější verze (z důvodu zlepšení efektivity plán).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Ladění Příklad výsledků dotazu](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> I když svazek v tomto příkladu je záměrně malý, může být značné, zejména u větších databází efekt neoptimální parametry. Rozdíl v extrémních případech může být v rozmezí sekund pro rychlý případy a hodiny pro případy, pomalé.
> 
> 

Můžete prozkoumat **sys.resource_stats** k určení, zda prostředek pro test používá více nebo méně prostředků než jiného testu. Při porovnávání dat oddělte časování testů tak, aby se nenacházejí ve stejném okně 5 minut **sys.resource_stats** zobrazení. Cílem výkonu je minimalizovat celkové množství použité prostředky a není minimalizovat prostředků ve špičce. Obecně platí optimalizace část kódu pro latenci také snižuje spotřebu prostředků. Ujistěte se, že změny provedené v aplikaci jsou nezbytné a že změny nemají vliv na negativní zkušenosti někomu, kdo může používat pomocné parametry dotazu v aplikaci.

Pokud úloha obsahuje sadu opakujících se dotazů, často má smysl pro zachycení a ověřit optimality vaše volby plánu, protože řídí velikost jednotky minimální prostředků vyžaduje k hostování databáze. Po ověření, čas od času prozkoumat plánů, které vám pomohou zajistit, že nebyly degradovaný. Další informace o [dotazování pomocné parametry (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Mezidatabázové horizontálního dělení
Protože Azure SQL Database běží na komoditním hardwaru, limity kapacity pro izolované databáze jsou nižší než tradiční místní instalaci systému SQL Server. Někteří zákazníci databázových operací rozdělena více databází, když operace nehodí uvnitř omezení u jediné databáze ve službě Azure SQL Database pomocí technik horizontálního dělení. Většina zákazníků, kteří používají postupů horizontálního dělení ve službě Azure SQL Database svá data na jednom rozměru rozdělit mezi několik databází. U tohoto přístupu je potřeba pochopit, že aplikace s online zpracováním transakcí často provádění transakcí, které se týkají pouze jeden řádek nebo pro malou skupinu řádků ve schématu.

> [!NOTE]
> SQL Database teď poskytuje knihovnu pro účely pomoci s horizontálního dělení. Další informace najdete v tématu [přehled klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md).
> 
> 

Například pokud databáze obsahuje jméno zákazníka, pořadí a podrobnosti objednávky (například tradiční ukázkové databázi Northwind, která je dodávána s SQL serverem), můžete rozdělit data do více databází seskupením zákazník s související objednávky a detaily objednávky informace. Můžete zajistit, že zákaznická data zůstanou v izolované databáze. Aplikace by rozdělení různých zákazníků napříč databázemi, efektivně rozložení zátěže mezi několika databázemi. Horizontální dělení zákazníci nejen se můžete vyhnout maximálního limitu velikosti, ale Azure SQL Database může také zpracovávat úlohy, které jsou podstatně větší než omezení různých velikostech výpočetních prostředků, tak dlouho, dokud každé jednotlivé databáze zapadá do jeho DTU.

I když horizontální dělení databází nedojde k omezení kapacity agregační prostředků pro řešení, je velmi efektivní při podpoře velmi rozsáhlých řešeních, která jsou rozdělené do několika databází. Každou databázi můžete spouštět v různých výpočetních velikosti pro podporu velmi velké, "efektivní" databází s vysokými požadavky na prostředky.

### <a name="functional-partitioning"></a>Funkční dělení
SQL Server uživatelé často kombinovat mnoho funkcí v jediné databázi. Například pokud má aplikace logiky ke správě inventáře pro úložiště, této databáze může mít logiku spojovanou s inventářem, sledování nákupních objednávek, uložených procedur a indexovaných nebo materializovaná zobrazení, které Správa vytváření sestav konci měsíce. Tato technika usnadňuje správu operací, jako je zálohování, ale také vyžaduje, abyste pro nastavení velikosti hardware pro zvládání zatížení ve špičce přes všechny funkce aplikace.

Pokud používáte architekturu škálování ve službě Azure SQL Database, je vhodné pro rozdělení různých funkcí aplikace do jiné databáze. Tímto způsobem, každá aplikace může škálovat nezávisle na. Aplikace se stane Vytíženější (a zvyšuje zatížení databáze), správce můžete velikostí výpočetních nezávisle pro každou funkci v aplikaci. Na hranici s touto architekturou aplikace může být větší než komoditních jeden počítač dokáže zpracovat, protože zatížení je rozdělena mezi více počítačů.

### <a name="batch-queries"></a>Dávkové dotazy
Pro aplikace, které přístup k datům pomocí velkého objemu časté, dotazování ad hoc, vyžadovat značné množství doba odezvy se tráví síťové komunikace mezi aplikační vrstva a vrstva Azure SQL Database. I v případě, že aplikace a Azure SQL Database jsou ve stejném datovém centru, latence sítě mezi těmito dvěma může zvětšit podle velké množství dat operací přístupu k. Ke snížení síti kruhové cest pro operacemi přístupu k datům, zvažte možnost použít možnost buď dávkové dotazy ad hoc nebo zkompilovat jako uložené procedury. Pokud je dávka ad hoc dotazy, můžete odeslat více dotazů jako jeden velký batch v jedné cesty ke službě Azure SQL Database. Pokud kompilujete ad hoc dotazy v uložené proceduře, může dosažení stejného výsledku, jako kdyby jejich batch. Pomocí uložené procedury nabízí také výhody zvyšuje šance na ukládání do mezipaměti plány dotazů ve službě Azure SQL Database, abyste mohli používat uloženou proceduru znovu.

Některé aplikace jsou náročné na zápis. Někdy můžete snížit celkové zatížení vstupně-výstupních operací na databázi o tom, jak společně zpracovány v dávce zápisy. To často, je snadné – stačí pomocí explicitní transakce místo automatického potvrzení transakce v uložených procedurách a ad hoc dávky. Vyhodnocení různých technik, které můžete použít, najdete v části [dávkování techniky pro aplikace SQL Database v Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Můžete experimentovat s vlastní úlohu najít správný model pro dávkové zpracování. Nezapomeňte si uvědomit, že model může mít záruky mírně odlišné transakční konzistence. Vyhledání správné zatížení, který minimalizuje využití prostředků vyžaduje vyhledání správné kombinace konzistencí a výkonem kompromisy.

### <a name="application-tier-caching"></a>Ukládání do mezipaměti aplikační vrstvy
Některé databáze aplikace mají úlohy náročné na čtení. Ukládání do mezipaměti vrstvy můžou snížit zatížení na databázi a může potenciálně snížili množství výpočetních velikost požadovanou pro podporu databázi s využitím Azure SQL Database. S [Azure Redis Cache](https://azure.microsoft.com/services/cache/), pokud máte nějaké úlohy náročné na čtení, můžete tato data čtete jednou (nebo třeba jednou za počítač aplikační vrstvy, v závislosti na tom, jak je nakonfigurovaný), pak tato data a ukládat mimo vaši službu SQL database. Toto je způsob, jak snížit zatížení databáze (CPU a čtení vstupně-výstupní operace), ale není vliv na konzistenci transakcí, protože se data načtou z mezipaměti může být synchronizována s daty v databázi. V mnoha aplikacích je přijatelné určitou úroveň nekonzistence, to není true pro všechny úlohy. Všechny požadavky aplikace byste měli plně rozumět před implementací strategie ukládání do mezipaměti aplikační vrstvy.

## <a name="next-steps"></a>Další postup
* Další informace o úrovních služeb na základě DTU najdete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md).
* Další informace o úrovních služeb založený na virtuálních jádrech najdete v tématu [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).
* Další informace o elastických fondech najdete v tématu [co je elastický fond Azure?](sql-database-elastic-pool.md)
* Informace o výkonu a elastické fondy najdete v tématu [kdy zvažovat použití elastického fondu](sql-database-elastic-pool-guidance.md)

