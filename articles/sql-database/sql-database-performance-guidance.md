---
title: Ladění pokyny Azure SQL Database výkonu | Microsoft Docs
description: Další informace o použití doporučení pro zlepšení výkonu dotazů Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: c84104ac9094980d0e6d16b535dcf13c462a645a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="tuning-performance-in-azure-sql-database"></a>Ladění výkonu v Azure SQL Database

Databáze SQL Azure poskytuje [doporučení](sql-database-advisor.md) , můžete použít ke zlepšení výkonu databáze, nebo můžete nechat Azure SQL Database [automaticky přizpůsobit do vaší aplikace](sql-database-automatic-tuning.md) a změny, zlepší výkon vašich úloh.

Ve službě nemáte žádná doporučení použít a máte problémy s výkonem, můžete použít následující metody ke zlepšení přínos:
- Zvýšení úrovně služeb v vaše [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) , vaše [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md) poskytnout další zdroje k vaší databázi.
- Ladění aplikace a použít některé osvědčené postupy, které může zlepšit výkon. 
- Optimalizujte databázi změnou indexy a dotazy efektivněji pracovat s daty.

Tyto jsou ruční metody, protože je potřeba rozhodnout se, jak [limitů prostředků na základě DTU modelu](sql-database-dtu-resource-limits.md) a [limitů prostředků na základě vCore modelu (preview)](sql-database-vcore-resource-limits.md) podle svých potřeb. Jinak potřebovali byste přepisování aplikace nebo kódu databáze a nasazení změn.

## <a name="increasing-performance-tier-of-your-database"></a>Zvýšení úrovně výkonu databáze

Databáze SQL Azure nabízí dva modely nákupu, [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md) ze kterých si můžete vybrat. Každé úrovni služeb výhradně izoluje prostředky, můžete použít SQL database a zaručuje, předvídatelný výkon této úrovně služeb. V tomto článku jsme nabízí pokyny, které vám pomohou vybrat úroveň služby pro aplikaci. Můžeme také popisují způsoby, abyste mohli vyladit aplikace k plnému využití z databáze SQL Azure.

> [!NOTE]
> Tento článek se zaměřuje na pokyny výkonu pro izolované databáze ve službě Azure SQL Database. Pokyny výkonu související s elastické fondy najdete v tématu [cenové a výkonové požadavky pro elastické fondy](sql-database-elastic-pool-guidance.md). Upozorňujeme ale, můžete použít řadu vyladění doporučení v tomto článku pro databáze v elastickém fondu a získejte výhody podobné výkonu.
> 

* **Základní**: základní služby úroveň nabízí dobrý výkon předvídatelnost pro každou databázi, hodinu přes hodinu. V databázi základní podporují dostatek prostředků dobrý výkon v malé databázi, která nemá více souběžných požadavků. Typické použití případy, kdy byste měli použít úroveň základní služby jsou:
  * **Můžete se právě Začínáme s Azure SQL Database**. Aplikace, které jsou často vývojem nepotřebují vysoce výkonné úrovně. Základní databáze jsou ideální prostředí pro vývoj databází nebo testování okamžiku nízkou cenu.
  * **Máte databázi s jedním uživatelem**. Aplikace, které uživatel s jednotným přidružit databáze obvykle nemají vysokou požadavky na souběžnost a výkon. Tyto aplikace jsou kandidáty pro úroveň služby na úrovni Basic.
* **Standardní**: ve standardu vrstvy služeb nabízí lepší výkon, předvídatelnost a poskytuje dobrý výkon u databází, které mají více souběžných žádostí, jako je pracovní skupiny a webové aplikace. Když vyberete databázi vrstvy služby na úrovni Standard, může Velikost databáze aplikace založené na předvídatelný výkon, minutu přes minutu.
  * **Databáze má více souběžných požadavků**. Aplikace, které služeb více uživatelů najednou obvykle nutné vyšší úrovně výkonu. Například pracovní skupině nebo webové aplikace, které mají nedostatek na střední vstupně-VÝSTUPNÍMI požadavky provoz podpora více souběžných dotazů jsou vhodnými kandidáty pro úroveň služby na úrovni Standard.
* **Premium**: úroveň služeb Premium poskytuje předvídatelný výkon databáze sekundu přes sekundu pro každou Premium nebo kritické obchodní (preview). Pokud vyberete úroveň služeb Premium, můžete měnit velikost databáze aplikace založené na zátěž ve špičce pro tuto databázi. Plán odebere případy, ve které výkonu může způsobit odchylku malé dotazy trvá déle, než bylo očekáváno v operacích citlivý na latenci. Tento model může výrazně zjednodušit ověření cykly vývoj a produktu pro aplikace, které musí být silné prohlášení o potřebných prostředků ve špičce, odchylku výkonu nebo latence dotazu. Většina případy použití vrstvy služby Premium mít jeden nebo více z těchto vlastností:
  * **Zátěž ve špičce vysokou**. Aplikaci, která vyžaduje významné CPU, paměť či vstup/výstup (IO) k dokončení operace vyžaduje úroveň vyhrazené a vysoce výkonné. Operace databáze známé využívat několik jader procesoru po delší dobu, je třeba kandidátem na úroveň služeb Premium.
  * **Mnoha souběžnými požadavky**. Některé databáze aplikace služby mnoho souběžných žádostí, například když obsluhuje web, který má intenzivní provoz. Úrovně Basic a Standard úrovně služeb omezit počet souběžných požadavků na databázi. Aplikace, které vyžadují více připojení bude muset zvolit příslušné rezervace velikost pro zpracování maximální počet potřebných požadavků.
  * **Nízká latence**. Některé aplikace potřebují zaručit minimální včas odpověď z databáze. Pokud konkrétní uložená procedura je volána v rámci širší operace zákazníka, bude pravděpodobně potřeba mít návratový z tohoto volání v milisekundách více než 20, 99 procent času. Tento typ aplikace výhody z úroveň služeb Premium, abyste měli jistotu, že je k dispozici požadované výpočetní výkon.

Úrovně služby, které potřebujete pro vaši databázi SQL, závisí na požadavky na zatížení ve špičce Každá dimenze prostředků. Některé aplikace využívat trivial množství jediný zdroj, ale mají významný požadavky na jiné prostředky.

### <a name="service-tier-capabilities-and-limits"></a>Možnosti vrstvy služby a omezení

V jednotlivých úrovních služby nastavena úroveň výkonu, máte možnost platit pouze pro potřebnou kapacitu. Můžete [upravit kapacity](sql-database-service-tiers-dtu.md), nahoru nebo dolů, jako změny zatížení. Například pokud vaše databáze úlohy vysoké během nákupní sezóny zpět školy, můžete zvýšit úroveň výkonu pro databázi nastavit dobu, červenec prostřednictvím září. Ho může snížit, až skončí vaše sezóny ve špičce. Můžete minimalizovat platíte pomocí optimalizace cloudového prostředí, abyste sezónnosti vaší firmy. Tento model se také funguje dobře pro cykly verze softwaru produktu. Test tým může přidělit kapacity při testování spustí a poté uvolněte tuto kapacitu, při jejich dokončení testování. V žádosti o modelu kapacitu platíte kapacity ho potřebujete, a vyhnout se výdaje na vyhrazených prostředcích, které můžou používat jenom občas.

### <a name="why-service-tiers"></a>Proč úrovních služeb?
I když každé zatížení databáze se může lišit, účelem úrovně služeb je poskytnout předvídatelnost výkonu na různé úrovně výkonu. Zákazníci s požadavky na prostředky ve velkém měřítku databáze můžete pracovat ve více vyhrazené výpočetním prostředí.

## <a name="tune-your-application"></a>Vyladění aplikace
Tradiční na místním serveru SQL je proces plánování kapacity počáteční často oddělená od proces spuštění aplikace v produkčním prostředí. Hardware a produktu licence jsou nejprve zakoupit a optimalizace výkonu se provádí i později. Pokud používáte Azure SQL Database, je vhodné interweave proces spuštění aplikace a vyladění jeho. S modelem platícího kapacity na vyžádání abyste mohli vyladit aplikace k používání minimální prostředky potřebné teď místo předimenzování na hardwaru podle pokusů budoucímu růstu plány pro aplikace, které často jsou nesprávné. Někteří zákazníci mohou zvolit nepoužívání ladit aplikace a místo toho zvolíte pro overprovision hardwarové prostředky. Tento přístup může být vhodné, pokud nechcete změnit klíče aplikace během období zaneprázdněný. Ale ladění aplikace snížit požadavky na prostředky a nižší měsíční faktur při použití úrovně služeb ve službě Azure SQL Database.

### <a name="application-characteristics"></a>Vlastnosti aplikace
I když Azure SQL Database úrovně služeb jsou určeny ke zlepšení výkonu stability a předvídatelnost pro aplikaci, můžete pomocí některé z osvědčených postupů ladit aplikace lépe využít prostředky na úrovni výkonu. I když mnoho aplikací mít výrazné zvýšení výkonu jednoduše tak, že přepnutí na vyšší úroveň výkonu nebo úroveň služby, některé aplikace potřebují odladit těžit z vyšší úrovně služby. Pro zvýšení výkonu vezměte v úvahu další aplikaci ladění pro aplikace, které mají tyto charakteristiky:

* **Aplikace, které mají pomalý výkon z důvodu "chatty" chování**. Chatty aplikace provést operace přístupu k nadměrnému dat, které jsou citlivá na latenci sítě. Možná budete muset upravit tyto typy aplikací a snížit počet operací přístupu k datům databáze SQL. Například může zlepšit výkon aplikace pomocí technik jako dávkování dotazů ad-hoc nebo přesunutí dotazy na uložené procedury. Další informace najdete v tématu [dávky dotazy](#batch-queries).
* **Databáze s náročné úlohy, která nepodporují se celý jeden počítač**. Databáze, které překračují prostředky nejvyšší úrovně Premium výkonu mohou využít škálování zatížení. Další informace najdete v tématu [horizontálního dělení mezidatabázové](#cross-database-sharding) a [funkční dělení](#functional-partitioning).
* **Aplikace, které mají zhoršené dotazy**. Aplikace, zejména v datové vrstvě přístupu, která mají špatně přizpůsobená dotazy nemusí využít vyšší úroveň výkonu. To zahrnuje dotazy, které chybí klauzule WHERE, chybějící indexy nebo zastaralé statistiky. Tyto aplikace využít techniky optimalizace výkonu standardní dotazu. Další informace najdete v tématu [chybějící indexy](#identifying-and-adding-missing-indexes) a [ladění a zobrazování rad dotazů](#query-tuning-and-hinting).
* **Aplikace, které mají zhoršené data přístup k návrhu**. Aplikace, které mají vyplývajících data potíže se souběžností přístup, například vzájemné zablokování, nemusí využít vyšší úroveň výkonu. Zvažte snížení odezev proti dané databázi SQL Azure pomocí ukládání dat do mezipaměti na straně klienta pomocí služby ukládání do mezipaměti Azure nebo jiné technologie ukládání do mezipaměti. V tématu [ukládání do mezipaměti aplikace vrstvy](#application-tier-caching).

## <a name="tune-your-database"></a>Vyladění databáze
V této části se podíváme na některé techniky, které můžete použít k vyladění Azure SQL Database k získání nejlepší výkon pro vaši aplikaci a spusťte jej na nejnižší úrovni možný výkon. Některé z těchto postupů odpovídat tradiční ladění osvědčené postupy systému SQL Server, ale jiné jsou specifické pro Azure SQL Database. V některých případech můžete zkontrolovat spotřebované prostředky pro databázi najít oblasti, které další ladit a rozšířit tradiční technologie SQL Server pro práci v Azure SQL Database.

### <a name="identify-performance-issues-using-azure-portal"></a>Identifikovat problémy s výkonem pomocí portálu Azure
Následující nástroje na portálu Azure můžete analyzovat a opravit problémy s výkonem s vaší databázi SQL:

* [Query Performance Insight](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

Portál Azure obsahuje další informace o obou těchto nástrojů a jejich použití. Efektivní diagnostikovat a opravit problémy, doporučujeme, abyste si nejdříve zkusili nástroje na portálu Azure. Doporučujeme vám, že používáte metodu ručního ladění přístupy, které dál probereme chybějící indexy a dotaz optimalizace ve zvláštních případech.

Další informace o identifikaci problémů ve službě Azure SQL Database na [monitorování výkonu](sql-database-single-database-monitor.md) článku.

### <a name="identifying-and-adding-missing-indexes"></a>Identifikace a přidání chybějící indexy
Běžné potíže s výkonem databáze OLTP má vztah k návrhu fyzická databáze. Databáze schémata jsou často, určené a dodaný bez testování ve velkém měřítku, (buď v zatížení nebo datový svazek). Bohužel výkon plán dotazu může být přijatelné v malém měřítku, ale podstatně snížit pod úroveň výroby datové svazky. Nejběžnější zdroj tohoto problému je nedostatek příslušné indexy vyhovět filtry, nebo jiná omezení v dotazu. Často chybějící indexy manifesty jako tabulku kontrolovat může stačit indexu vyhledávání.

V tomto příkladu používá plán vybraný dotaz kontrolu při by stačit seek:

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

Databáze SQL Azure můžete najít a opravu běžné chybí indexu podmínky. Podívejte se na kompilace dotazu, ve kterých by indexu výrazně snížit odhadované náklady ke spuštění dotazu zobrazení dynamické správy, které jsou součástí Azure SQL Database. Během provádění dotazu SQL Database sleduje, jak často se spustí každý plán dotazu a sleduje odhadované mezery mezi provádění plán dotazu a imagined jeden kde existovaly tento index. Můžete použít tyto zobrazení dynamické správy snadno uhodnout které změny návrhu fyzická databáze může zlepšit celkové náklady na úlohy pro databázi a její skutečné pracovní vytížení.

Tento dotaz můžete použít k vyhodnocení potenciální chybějící indexy:

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

V tomto příkladu výsledkem dotazu byl tento návrh:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Po vytvoření, že stejný příkaz SELECT vybere jiný plán, který místo kontrolu používá hledání a pak efektivněji provede plánu:

![Plán dotazu s opravené indexy](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Přehled klíče je omezenější než počítač vyhrazený server kapacita v/v, sdílené, komoditním systému. Je na minimalizovat nepotřebné vstupně-výstupní operace maximální využívat systému v jednotek DTU úrovně výkonu jednotlivých úrovní služby Azure SQL Database premium. Fyzické databázi návrhu volby může výrazně zlepšit latenci pro jednotlivé dotazy zlepšit propustnost souběžných požadavků zpracovaných za jednotku škálování a minimalizovat náklady potřebných k tomu dotazu. Další informace o chybějící index zobrazení dynamické správy najdete v tématu [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Ladění dotazu a zobrazování rad
Optimalizátor dotazů v databázi SQL Azure je podobná tradičním Optimalizátor dotazů systému SQL Server. Většina osvědčených postupů pro ladění dotazy a seznámit se s odůvodněním modelu omezení pro Optimalizátor dotazů platí také pro Azure SQL Database. Pokud je optimalizace dotazů v databázi SQL Azure, může získat další výhody snižuje nároky na agregační prostředků. Aplikace může být možné spustit při nižších nákladech, než naladěného ekvivalentní, protože můžete spustit na nižší úrovni výkonu.

Příklad, který je běžné v systému SQL Server a který platí také pro Azure SQL Database je jak Optimalizátor dotazů "zachytává obsah" parametry. Optimalizátor dotazů během kompilace, vyhodnotí aktuální hodnota parametru k určení, zda může vygenerovat víc optimální plán dotazu. I když tato strategie často může vést k plán dotazu, který je mnohem rychlejší než plán kompilovat bez parametru známé hodnoty, aktuálně funguje imperfectly i v systému SQL Server a ve službě Azure SQL Database. Parametr není někdy zachycení a někdy zachycení parametr ale vygenerovaný plán je zhoršené úplnou sadu hodnot parametrů v zatížení. Microsoft zahrnuje pomocné parametry dotazu (direktivy), takže můžete zadat více úmyslně záměr a přepsat výchozí chování sledování toku dat parametr. Často Pokud používáte pomocné parametry, můžete je vyřešit případy, ve které je výchozí chování systému SQL Server nebo Azure SQL Database nedokonalé pro konkrétního zákazníka zatížení.

Další příklad ukazuje, jak může procesor dotazů Generovat plán, který je zhoršené pro výkon a požadavky na prostředky. Tento příklad také ukazuje, že pokud používáte pomocný parametr dotazu, můžete snížit požadavky na spuštění dotazu času a prostředků pro vaši databázi SQL:

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

Instalační kód vytvoří tabulku, která má nesouměrně rozdělí distribuci dat. Plán optimální dotazu se liší podle které zablokováno. Bohužel plán chování ukládání do mezipaměti není vždy znovu zkompiluje dotaz založený na nejběžnější hodnotu parametru. Ano je možné pro plán zhoršené do mezipaměti a použít pro více hodnot, i v případě, že jiný plán může být vhodnější plán v průměru. Plán dotazu pak vytvoří dvě uložené procedury, které jsou stejné, s tím rozdílem, že jeden má nápovědu speciální dotazu.

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

(Doporučujeme Počkejte minimálně 10 minut, než začnete, část 2 / příkladu, tak, aby byly výsledky jedinečná ve výsledné telemetrická data.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Jednotlivých součástí v tomto příkladu se pokusí o spuštění příkazu parametrizované insert 1 000krát (ke generování dostatečná zatížení chcete použít jako testovací sada dat). Pokud se provede uložené procedury, procesor dotazů prověří hodnotu parametru, který je předán postup během jeho první kompilace (parametr "sledování toku dat"). Procesor ukládá do mezipaměti výsledný plán a používá je pro pozdější volání i v případě, že hodnota parametru se liší. Ve všech případech se možná nepoužívají optimální plán. V některých případech budete muset Průvodce Optimalizátor a vyberte plán, který je lepší pro průměrnou případu, nikoli konkrétní případ z při první kompilace dotazu. V tomto příkladu generuje počáteční plán "kontrola" plán, který čte všechny řádky považovat každá hodnota, která odpovídá parametru:

![Dotaz na ladění pomocí plánu kontroly](./media/sql-database-performance-guidance/query_tuning_1.png)

Protože jsme postup provést pomocí hodnoty 1, výsledný plán byla optimální pro hodnotu 1, ale byla zhoršené pro všechny ostatní hodnoty v tabulce. Výsledek pravděpodobně není co vhodnější, pokud byste chtěli náhodně, vyberte každý plán, protože plán provede pomaleji a používá další prostředky.

Pokud spustíte test s `SET STATISTICS IO` nastavena na `ON`, pracovní logické kontroly v tomto příkladu se provádí na pozadí. Můžete zjistit, že jsou 1,148 čtení provádí plán (což je neefektivní, pokud průměrná případ vrácení pouze jeden řádek):

![Dotaz na ladění pomocí logické kontroly](./media/sql-database-performance-guidance/query_tuning_2.png)

Druhá část příkladu používá pomocný parametr dotazu říct Optimalizátor pomocí konkrétní hodnoty během kompilace. V takovém případě vynutí procesor dotazů ignorovat hodnotu, která se předá jako parametr, a místo toho předpokládat, že `UNKNOWN`. Označuje hodnotu, která má Průměrná frekvence v tabulce (ignorována zkosení). Na základě seek plán, který je rychlejší a využívá méně prostředků, v průměru, než plán v části 1 tohoto příkladu je výsledný plán:

![Ladění dotaz s použitím pomocného parametru dotazu](./media/sql-database-performance-guidance/query_tuning_3.png)

Uvidíte výsledky v **sys.resource_stats** tabulky (dochází ke zpoždění od okamžiku spuštění testu a když data naplní tabulky). Pro tento příklad, část 1 provést během časového okna 22:25:00 a část 2 provedená 22:35:00. Starší časový interval použít další zdroje v dané časové okno než novější verzi (z důvodu zlepšení efektivity plánu).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Ladění příklad výsledky dotazu](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> I když svazek v tomto příkladu je záměrně malý, může být účinku zhoršené parametry podstatné, zejména u velkých databází. Rozdíl ve výjimečných případech může být v rozmezí sekund pro rychlé případy a čas pro případy, pomalé.
> 
> 

Můžete zkontrolovat **sys.resource_stats** k určení, zda prostředek pro test používá více nebo méně prostředků než jiného testu. Při porovnávání dat oddělte načasování testy tak, aby se nenacházejí ve stejném okně 5 minut **sys.resource_stats** zobrazení. Cílem výkonu je minimalizovat celkový objem prostředků používá a ne minimalizovat prostředků ve špičce. Obecně platí optimalizace úsek kódu pro latenci zmenšuje spotřeby prostředků. Zajistěte, aby nutných změny provedené v aplikaci a že změny neovlivní negativně zkušeností zákazníků někomu, kdo může pomocí pomocné parametry dotazu v aplikaci.

Pokud úloha má sadu opakujících se dotazů, často má smysl k zaznamenání a ověřit optimality zvoleného plán, protože ho jednotky jednotka velikost minimální zdroje potřebné k hostování databáze. Po ověření, někdy prozkoumat plánů, které vám pomohou zajistit, že nebyly degradovaný. Další informace o [dotaz pomocné parametry (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Mezidatabázové horizontálního dělení
Vzhledem k Azure SQL Database běží na komoditním hardwaru, limity kapacity pro izolované databáze jsou nižší než v případě tradičních místních instalace SQL serveru. Někteří zákazníci využít techniky horizontálního dělení k šíření databázových operací prostřednictvím více databází, když není operace nevejde se do omezení jedné databáze ve službě Azure SQL Database. Většina zákazníkům, kteří používají horizontálního dělení techniky ve službě Azure SQL Database rozdělit do několika databází svá data na jedinou dimenzí. Tento postup musíte pochopit, že OLTP aplikace často provádění transakcí, které se vztahují pouze jeden řádek nebo pro malou skupinu řádků ve schématu.

> [!NOTE]
> Databáze SQL teď poskytuje knihovnu vám pomůže při horizontálního dělení. Další informace najdete v tématu [přehled klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md).
> 
> 

Například pokud databáze obsahuje jméno zákazníka, pořadím a podrobnosti o pořadí (např. databáze Northwind tradiční příklad, který se dodává se systémem SQL Server), je může rozdělit tato data do více databází tím, že zákazník s související pořadí a podrobnosti pořadí informace. Může zaručit, že data zákazníka zůstává v jedné databáze. Aplikace by rozdělit různých zákazníků do databáze, efektivně šíření zatížení napříč více databází. S horizontálního dělení zákazníci nejen se můžete vyhnout maximálního limitu velikosti, ale Azure SQL Database může také zpracovat úlohy, které jsou podstatně větší než omezení úrovně různých výkonu, tak dlouho, dokud každé jednotlivé databáze zapadá do jeho DTU.

I když horizontálního dělení databáze není snížit kapacitu agregační prostředků pro řešení, je vysoce efektivní v podpora velmi velké řešení, která jsou rozloženy více databází. Každou databázi můžete spustit na úrovni různých výkonu pro podporu velký, "efektivní" databáze s vysokými požadavky na prostředky.

### <a name="functional-partitioning"></a>Funkční dělení
Uživatelům systému SQL Server často kombinovat mnoho funkcí v jedné databáze. Například pokud má aplikace logiky ke správě inventáře pro úložiště, že databáze může být logiku související s inventářem, sledování nákupních objednávek, uložené procedury a indexované nebo Vyhodnocená zobrazení, které Správa vytváření sestav koncový měsíc. Tento postup je snazší spravovat databáze pro operace, jako je zálohování, ale také vyžaduje, abyste velikost hardwarem pro zpracování zátěž ve špičce mezi všechny funkce aplikace.

Pokud používáte architekturu škálování ve službě Azure SQL Database, je vhodné k rozdělení různé funkce aplikace do různých databází. Pomocí tohoto postupu nezávisle škáluje každou aplikaci. Když aplikace bude Vytíženější (a zvyšuje zatížení databáze), Správce může rozhodnout úrovně nezávislé výkonu pro každou funkci v aplikaci. V omezení, tato architektura aplikace může být větší než jeden komoditním počítač dokáže zpracovat, protože zatížení je rozdělena mezi více počítačů.

### <a name="batch-queries"></a>Batch dotazy
Pro aplikace, které přístup k datům s použitím vysoký počet časté, ad hoc dotazy, vyžadovat značné množství doba odezvy je stráví na síťovou komunikaci mezi aplikační vrstvě a vrstvy databáze SQL Azure. I v případě, že aplikace i databáze SQL Azure jsou ve stejném datovém centru, latence sítě mezi těmito dvěma může zvětšit podle velké množství dat operace přístupu. Ke snížení sítě zpátečních cest pro operací přístupu k datům, zvažte použití možnost buď dávky ad hoc dotazy, nebo jejich kompilace jako uložené procedury. Pokud jste dávky ad hoc dotazy, můžete odeslat více dotazů jako jeden velký batch v jedné cestě k databázi SQL Azure. Pokud zkompilujete ad hoc dotazy v uložené proceduře, může se jako v případě, že jste dávky je dosažení stejného výsledku. Pomocí uložené procedury poskytuje také výhodou zvyšuje šance na ukládání do mezipaměti plány dotazů v databázi SQL Azure, abyste mohli používat uloženou proceduru znovu.

Některé aplikace jsou náročné na zápis. Někdy můžete snížit celkové zatížení vstupně-výstupní operace v databázi pomocí vzhledem k tomu, jak dávky zápisu společně. Často je stejně jednoduché jako používání explicitních transakcí místo automatického potvrzení transakce v uložené procedury a ad hoc dávky. Vyhodnocení různých technik, které můžete použít, najdete v části [dávkování techniky pro aplikace, databáze SQL v Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimentujte s vlastními zatížení najít správné modelu pro dávkové zpracování. Ujistěte se, abyste pochopili, že model může mít mírně odlišný transakční konzistence záruky. Hledání správné zatížení, které snižuje využití prostředků vyžaduje hledání tu správnou kombinaci konzistencí a výkonem kompromis.

### <a name="application-tier-caching"></a>Ukládání do mezipaměti aplikační vrstvy
Některé databázových aplikací mít úlohy náročné na čtení. Ukládání do mezipaměti vrstvy může vést ke snížení zatížení databáze a může potenciálně snížit úroveň výkonu, které jsou potřebné k podpoře databáze pomocí Azure SQL Database. S [Azure Redis Cache](https://azure.microsoft.com/services/cache/), pokud máte úlohy náročné na čtení, můžete číst data jednou (nebo případně jednou za aplikační vrstvy počítače, v závislosti na tom, jak je nakonfigurovaná) a potom tato data mimo vaší databázi SQL. Toto je způsob, jak snížit zatížení databáze (procesoru a vstupů/výstupů pro čtení), ale není vliv na transakční konzistence, protože data se načten z mezipaměti může být synchronizována s daty v databázi. V mnoha aplikacích určité úrovně, ke kterému je přijatelné, není, platí pro všechny úlohy. Všechny požadavky aplikace byste měli plně rozumět před implementací strategie pro ukládání do mezipaměti aplikační vrstvy.

## <a name="next-steps"></a>Další postup
* Další informace o úrovních služeb na základě DTU najdete v tématu [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [limitů prostředků na základě DTU modelu](sql-database-dtu-resource-limits.md)
* Další informace o úrovních služeb na základě vCore najdete v tématu [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md) a [limitů prostředků na základě vCore (preview)](sql-database-vcore-resource-limits.md)
* Další informace o elastické fondy najdete v tématu [co je Azure elastickém fondu?](sql-database-elastic-pool.md)
* Informace o výkonu a Elastická fondy najdete v tématu [při vzít v úvahu fondu elastické databáze](sql-database-elastic-pool-guidance.md)

