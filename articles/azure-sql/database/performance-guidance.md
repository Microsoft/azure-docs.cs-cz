---
title: Pokyny k ladění výkonu pro aplikace a databáze
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Přečtěte si, jak vyladit databázové aplikace a databáze pro výkon v Azure SQL Database a v Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/10/2020
ms.openlocfilehash: 40657ad2f3b69d62e0e0d9c7d9e0f0be7343547b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490597"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Ladění aplikací a databází pro výkon v Azure SQL Database a spravované instanci Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Po zjištění problému s výkonem, na který jste se dostavili Azure SQL Database a Azure SQL Managed instance, je tento článek navržený tak, aby vám pomohly:

- Optimalizujte svou aplikaci a použijte některé osvědčené postupy, které můžou zlepšit výkon.
- Vyladění databáze tak, že změníte indexy a dotazy pro efektivnější práci s daty.

V tomto článku se předpokládá, že už jste pracovali s [doporučeními Azure SQL Database Database Advisoru](database-advisor-implement-performance-recommendations.md) a Azure SQL Database [automaticky vyladit doporučení](automatic-tuning-overview.md), pokud jsou k dispozici. Také předpokládá, že jste si přečetli [Přehled monitorování a optimalizace](monitor-tune-overview.md) a související články týkající se řešení potíží s výkonem. V tomto článku se navíc předpokládá, že nemáte prostředky procesoru, a s tím spojené problémy s výkonem, které se dají vyřešit zvýšením velikosti výpočetní kapacity nebo úrovně služby, aby bylo možné získat více prostředků do vaší databáze.

## <a name="tune-your-application"></a>Optimalizace aplikace

V tradičních místních SQL Server proces prvotního plánování kapacity je často oddělený od procesu spuštění aplikace v produkčním prostředí. Nejprve se kupují hardwarové a produktové licence a optimalizace výkonu se provádí později. Pokud používáte Azure SQL, je vhodné, abyste propravili proces spuštění aplikace a její optimalizaci. Díky modelu uplatnění kapacity na vyžádání můžete svou aplikaci vyladit tak, aby místo nadměrného zřizování na hardwaru používala minimální prostředky, které jsou potřeba k tomu, aby se prováděly nepřesné požadavky na hardware, a to na základě odhadu budoucích plánů růstu pro aplikaci, které jsou často nesprávné. Někteří zákazníci si můžou zvolit, že nemají ladit aplikaci, a místo toho se rozhodnou prostředky hardwaru. Tento přístup může být dobrý nápad, pokud nechcete během zaneprázdněného období měnit klíčovou aplikaci. Ladění aplikace ale může minimalizovat požadavky na prostředky a snížit měsíční faktury při používání úrovní služeb v Azure SQL Database a Azure SQL Managed instance.

### <a name="application-characteristics"></a>Vlastnosti aplikace

I když jsou úrovně služby Azure SQL Database a Azure SQL Managed instance navržené tak, aby se zlepšila stabilita výkonu a předvídatelnost pro aplikaci, některé osvědčené postupy vám můžou pomoci s optimalizací vaší aplikace, aby lépe využily výhod prostředků na výpočetní úrovni. I když mnoho aplikací má výrazný nárůst výkonu jednoduše tím, že přejdete na vyšší výpočetní velikost nebo úroveň služby, některé aplikace potřebují další ladění, abyste využili vyšší úroveň služby. Pro zvýšení výkonu zvažte další ladění aplikací pro aplikace, které mají tyto vlastnosti:

- **Aplikace s nízkým výkonem z důvodu chování "chaty"**

  Aplikace v konverzaci využívají nadměrné operace přístupu k datům, které jsou citlivé na latenci sítě. Je možné, že budete muset upravit tyto typy aplikací a snížit tak počet operací přístupu k datům do databáze. Například můžete zlepšit výkon aplikace pomocí technik, jako jsou dávkové zpracování dotazů ad hoc nebo přesunutí dotazů do uložených procedur. Další informace najdete v tématu [dávkové dotazy](#batch-queries).

- **Databáze s náročnými úlohami, které nemůžou podporovat celý jeden počítač**

   Databáze, které překračují prostředky nejvyšší výpočetní velikosti Premium, můžou využívat horizontální škálování zatížení. Další informace najdete v tématech [mezidatabázová horizontálního dělení](#cross-database-sharding) a [funkční dělení](#functional-partitioning).

- **Aplikace, které mají dílčí optimální dotazy**

  Aplikace, zejména ty ve vrstvě pro přístup k datům, které mají špatně vyladěné dotazy, nemůžou mít větší výpočetní velikost. To zahrnuje dotazy, u kterých chybí klauzule WHERE, chybějící indexy nebo zastaralé statistiky. Tyto aplikace využívají standardní techniky vyladění výkonu dotazů. Další informace najdete v tématu [chybějící indexy](#identifying-and-adding-missing-indexes) a [optimalizace dotazů a hinty](#query-tuning-and-hinting).

- **Aplikace, které mají návrh pro přístup k datům v dílčím ideálním případě**

   Aplikace, které mají podstatný problém souběžnosti přístupu k datům, například zablokování, nemusí využívat vyšší výpočetní velikost. Zvažte zmenšení zpátečních cest k databázi uložením dat na straně klienta do mezipaměti pomocí služby Azure Caching nebo jiné technologie ukládání do mezipaměti. Viz [ukládání aplikační vrstvy do mezipaměti](#application-tier-caching).

## <a name="tune-your-database"></a>Vyladění databáze

V této části se podíváme na některé postupy, které můžete použít k vyladění databáze a získat tak nejlepší výkon pro vaši aplikaci a spustit ji s nejnižší možnou výpočetní velikostí. Některé z těchto postupů odpovídají tradičním SQL Server optimalizaci osvědčených postupů, ale jiné jsou specifické pro Azure SQL Database a Azure SQL Managed instance. V některých případech můžete prozkoumat spotřebované prostředky pro databázi a vyhledat oblasti pro další vyladění a rozšířit tradiční SQL Server techniky pro práci v Azure SQL Database a spravované instanci Azure SQL.

### <a name="identifying-and-adding-missing-indexes"></a>Identifikace a přidání chybějících indexů

Běžný problém ve výkonu databáze OLTP se týká návrhu fyzické databáze. Databázová schémata se často navrhují a dodávají bez testování ve velkém měřítku (buď v zatížení nebo v datovém svazku). Výkon plánu dotazů bohužel může být přijatelný v malém měřítku, ale v podstatě je na úrovni produkčních objemů dat. Nejběžnějším zdrojem tohoto problému je nedostatek vhodných indexů, které by vyhovovaly filtrům nebo jiným omezením v dotazu. V případě, že by hledání v indexu nemohlo být, často chybějí indexy manifesty jako prohledávání tabulky.

V tomto příkladu vybraný plán dotazu používá kontrolu, pokud by měl stačit hledání:

```sql
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
```

![Plán dotazu s chybějícími indexy](./media/performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database a Azure SQL Managed instance vám pomůžou najít a opravit běžné chybějící podmínky indexu. Zobrazení dynamické správy, které jsou integrované do Azure SQL Database a Azure SQL Managed instance, se podíváme na kompilace dotazů, ve kterých by index významně snižoval odhadované náklady na spuštění dotazu. Při provádění dotazu databázový stroj sleduje, jak často se jednotlivé plány dotazů spouštějí, a sleduje odhadovanou mezeru mezi vydaným plánem dotazů a představem, který tento index existoval. Tyto zobrazení dynamické správy můžete použít k rychlému odhadu, které změny v návrhu fyzické databáze můžou zlepšit celkové náklady na úlohy pro databázi a její reálnou úlohu.

Tento dotaz můžete použít k vyhodnocení potenciálních chybějících indexů:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

V tomto příkladu je výsledkem dotazu tento návrh:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Po vytvoření stejný příkaz SELECT vybere jiný plán, který místo kontroly používá hledání, a pak plán provede efektivněji:

![Plán dotazů s opravenými indexy](./media/performance-guidance/query_plan_corrected_indexes.png)

Klíčovým přehledem je, že kapacita vstupně-výstupních operací sdíleného a komoditního systému je více omezená než na vyhrazeném serverovém počítači. K minimalizaci zbytečných vstupně-výstupních operací pro maximální využití systému v prostředcích všech výpočetních velikostí úrovní služeb se využívá Premium. Vhodné volby pro návrh fyzické databáze můžou výrazně zlepšit latenci pro jednotlivé dotazy, zlepšit propustnost souběžných požadavků zpracovaných na jednotku škálování a snížit náklady potřebné k uspokojení dotazu. Další informace o chybějícím indexu zobrazení dynamické správy naleznete v tématu [Sys.dm_db_missing_index_details](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql).

### <a name="query-tuning-and-hinting"></a>Ladění a hinty dotazů

Optimalizátor dotazů v Azure SQL Database a Azure SQL Managed instance se podobá tradičnímu optimalizátoru dotazů SQL Server. Většina osvědčených postupů pro optimalizaci dotazů a porozumění omezením modelu pro Optimalizátor dotazů platí také pro Azure SQL Database a Azure SQL Managed instance. Pokud budete ladit dotazy v Azure SQL Database a Azure SQL Managed instance, můžete získat další výhody omezení agregovaných požadavků na prostředky. Vaše aplikace může být schopná běžet za nižší náklady než vyladěný ekvivalent, protože může běžet s nižší výpočetní velikostí.

Příkladem, který je běžný v SQL Server a který platí také pro Azure SQL Database a Azure SQL Managed instance, je způsob, jakým jsou "" optimalizátory dotaz "". Během kompilace vyhodnocuje Optimalizátor dotazů aktuální hodnotu parametru a určí, zda může generovat lépe optimální plán dotazu. I když tato strategie často vede k plánu dotazů, který je výrazně rychlejší než plán kompilovaný bez známých hodnot parametrů, v současné době funguje nedokonalostně v SQL Server, v Azure SQL Database a ve spravované instanci Azure SQL. V některých případech je parametr nefiltrované a někdy je parametr uveden jako takový, ale vygenerovaný plán je pro úplnou sadu hodnot parametrů v úloze odvozený. Společnost Microsoft obsahuje pomocný parametr dotazu (direktivy), aby bylo možné určit účel záměrně a přepsat výchozí chování při sledování parametrů. Pokud používáte Rady, můžete často opravovat případy, kdy se výchozí SQL Server, Azure SQL Database a chování spravované instance SQL Azure dokonalé pro konkrétní pracovní vytížení zákazníka.

Následující příklad ukazuje, jak může procesor dotazů vygenerovat plán, který je pro požadavky na výkon i prostředky optimální. Tento příklad také ukazuje, že pokud použijete pomocný parametr dotazu, můžete zkrátit dobu běhu dotazu a požadavky na prostředky pro vaši databázi:

```sql
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
```

Kód instalace vytvoří tabulku, která má šikmou distribuci dat. Optimální plán dotazu se liší v závislosti na tom, který parametr je vybrán. Bohužel chování při ukládání plánu do mezipaměti nikdy znovu nekompiluje dotaz na základě nejběžnější hodnoty parametru. Proto je možné, že pro dílčí optimální plán se má ukládat do mezipaměti a používat pro spoustu hodnot, a to i v případě, že jiný plán může být pro průměrnou volbou lepší plán. Plán dotazu pak vytvoří dva uložené procedury, které jsou identické, s výjimkou toho, že jeden má zvláštní pomocný parametr dotazu.

```sql
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
```

Doporučujeme, abyste počkali alespoň 10 minut, než začnete s částí 2 tohoto příkladu, takže výsledky budou jedinečné ve výsledných datech telemetrie.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Každá část tohoto příkladu se pokusí spustit parametrizovaný příkaz INSERT 1 000 krát (pro generování dostatečného zatížení pro použití jako testovací sady dat). Při provádění uložených procedur Procesor dotazů prověřuje hodnotu parametru, která je předána proceduře během první kompilace (parametr "sledování"). Procesor ukládá výsledný plán do mezipaměti a používá ho pro pozdější vyvolání, a to i v případě, že se hodnota parametru liší. Optimální plán se nemusí používat ve všech případech. V některých případech je potřeba Průvodce optimalizací vybrat pro výběr plánu, který je vhodnější pro průměrnou velikost písmen, nikoli z konkrétního případu z při prvním kompilování dotazu. V tomto příkladu počáteční plán vygeneruje "skenovací" plán, který načte všechny řádky a vyhledá všechny hodnoty, které odpovídají parametru:

![Vyladění dotazů pomocí plánu skenování](./media/performance-guidance/query_tuning_1.png)

Vzhledem k tomu, že jsme provedli postup s použitím hodnoty 1, byl výsledný plán optimální pro hodnotu 1, ale byl pro všechny ostatní hodnoty v tabulce tento. Výsledek pravděpodobně není to, co byste chtěli, pokud byste si museli každý plán vybrat náhodně, protože plán pomaleji a využívá více prostředků.

Pokud spustíte test s `SET STATISTICS IO` nastavením na `ON` , funguje logická kontrola v tomto příkladu na pozadí. Vidíte, že plán obsahuje 1 148 čtení (což je neefektivní, pokud průměrná velikost písmen vrátí pouze jeden řádek):

![Vyladění dotazu pomocí logického prohledávání](./media/performance-guidance/query_tuning_2.png)

Druhá část příkladu používá pomocný parametr dotazu k oznámení, aby Optimalizátor používal konkrétní hodnotu během procesu kompilace. V takovém případě vynutí Procesor dotazů ignorovat hodnotu, která je předána jako parametr, a místo toho předpokládat `UNKNOWN` . To odkazuje na hodnotu, která má průměrnou frekvenci v tabulce (ignoruje se zešikmení). Výsledný plán je plán založený na hledání, který je rychlejší a používá méně prostředků v průměru, než je plán v části 1 tohoto příkladu:

![Vyladění dotazů pomocí pomocného parametru dotazu](./media/performance-guidance/query_tuning_3.png)

Můžete zobrazit efekt v tabulce **Sys.resource_stats** (nastane zpoždění od času, kdy spouštíte test a když data naplní tabulku). V tomto příkladu se část 1 spustila během časového intervalu 22:25:00.2. část byla provedena v 22:35:00. Předchozí časové okno v tomto časovém intervalu používalo více prostředků než novější (kvůli vylepšením efektivity plánování).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Příklady výsledků ladění dotazů](./media/performance-guidance/query_tuning_4.png)

> [!NOTE]
> I když je svazek v tomto příkladu záměrně malý, účinek dílčích optimálních parametrů může být zásadní, zejména u větších databází. Rozdíl v extrémních případech může být v rozmezí sekund pro rychlé případy a hodiny pro pomalé případy.

Můžete zkontrolovat **Sys.resource_stats** , abyste zjistili, zda prostředek pro test používá více nebo méně prostředků než jiný test. Když porovnáte data, oddělte časování testů tak, aby se v zobrazení **Sys.resource_stats** navzájemly v nestejném intervalu 5 minut. Cílem tohoto cvičení je minimalizovat celkový objem využitých prostředků, a ne minimalizovat prostředky ve špičce. Obecně optimalizuje část kódu pro latenci také snižuje spotřebu prostředků. Ujistěte se, že jsou změny, které provedete v aplikaci, nezbytné a že změny nemají negativní vliv na uživatelské prostředí pro někoho, kdo může v aplikaci použít pomocný parametr dotazu.

Pokud má úloha sadu opakujících se dotazů, často má smysl zachytit a ověřit optimální možnosti plánu, protože jednotkou je minimální jednotka velikosti prostředků, která je vyžadována pro hostování databáze. Po ověření si občas prověříte plány, které vám pomohou zajistit, že nebudou omezené. Další informace o [pokynech pro dotazy najdete v jazyce Transact-SQL](/sql/t-sql/queries/hints-transact-sql-query).

### <a name="very-large-database-architectures"></a>Velmi velké architektury databáze

Před vydáním vrstvy služby s jednoduchým [škálováním](service-tier-hyperscale.md) pro izolované databáze v Azure SQL Database zákazníci použili k dosažení limitů kapacity pro jednotlivé databáze. Tato omezení kapacity stále existují pro databáze ve fondu v Azure SQL Database elastické fondy a databáze instancí ve spravovaných instancích Azure SQL. Následující dvě části projednávají dvě možnosti řešení problémů s velmi rozsáhlými databázemi v Azure SQL Database a Azure SQL Managed instance, když nemůžete použít úroveň služby škálování na úrovni služeb.

### <a name="cross-database-sharding"></a>Horizontálního dělení mezi databázemi

Vzhledem k tomu, že se Azure SQL Database a Azure SQL Managed instance spouští na komoditním hardwaru, jsou limity kapacity pro jednotlivé databáze nižší než pro tradiční místní instalaci SQL Server. Někteří zákazníci využívají techniky horizontálního dělení k šíření databázových operací v několika databázích, když se operace nevejdou do limitů jednotlivých databází v Azure SQL Database a v Azure SQL Managed instance. Většina zákazníků, kteří používají techniky horizontálního dělení v Azure SQL Database a Azure SQL Managed instance, rozdělují svá data na jednu dimenzi napříč více databázemi. Pro účely tohoto přístupu potřebujete pochopit, že aplikace OLTP často provádějí transakce, které se vztahují pouze na jeden řádek nebo na malou skupinu řádků ve schématu.

> [!NOTE]
> Azure SQL Database teď poskytuje knihovnu, která vám pomůže s horizontálního dělení. Další informace najdete v tématu [Přehled klientské knihovny elastic Database](elastic-database-client-library.md).

Pokud má databáze například Podrobnosti o jménu zákazníka, objednávce a objednávce (jako je tradiční příklad databáze Northwind, která je dodávána s SQL Server), mohli byste tato data rozdělit do více databází seskupením zákazníka se souvisejícími podrobnými informacemi o objednávce a objednávce. Můžete zaručit, aby data zákazníka zůstala v individuální databázi. Aplikace by rozdělila různé zákazníky napříč databázemi a efektivně tak rozprostře zatížení napříč více databázemi. Díky horizontálního dělení se zákazníci nemůžou vyhnout maximální velikosti databáze, ale Azure SQL Database a Azure SQL Managed instance taky můžou zpracovat úlohy, které jsou výrazně větší než limity různých výpočetních velikostí, pokud se každá z jednotlivých databází vejde do svého omezení úrovně služby.

I když databáze horizontálního dělení neomezuje agregovanou kapacitu prostředků pro řešení, je vysoce efektivní při podpoře velmi rozsáhlých řešení, která jsou rozdělená do více databází. Každá databáze může běžet na jiné výpočetní úrovni, aby podporovala velmi rozsáhlou a "efektivní" databáze s vysokými nároky na prostředky.

#### <a name="functional-partitioning"></a>Funkční dělení

Uživatelé často kombinují mnoho funkcí v individuální databázi. Například pokud má aplikace logiku pro správu inventáře pro obchod, může mít tato databáze logiku přidruženou k inventarizaci, sledování nákupních objednávek, uložených procedur a indexovaných nebo materializovaná zobrazení, která spravují vytváření sestav od konce měsíce. Tato technika usnadňuje správu databáze pro operace, jako je zálohování, ale také vyžaduje, abyste změnili velikost hardwaru pro zpracování nejvyšší zátěže napříč všemi funkcemi aplikace.

Pokud používáte architekturu se škálováním na více instancí v Azure SQL Database a Azure SQL Managed instance, je vhodné rozdělit různé funkce aplikace do různých databází. Pomocí této techniky se každá aplikace škáluje nezávisle. V důsledku toho, že se aplikace změní na vytíženější (a zvýší se zatížení databáze), může správce zvolit nezávislé výpočetní velikosti pro každou funkci v aplikaci. V rámci této architektury může být aplikace větší, než jeden komoditový počítač může zpracovat, protože zatížení je rozdělené mezi několik počítačů.

### <a name="batch-queries"></a>Dávkové dotazy

U aplikací, které přistupují k datům s využitím vysokého objemu, častého dotazování ad hoc, se v síťové komunikaci mezi aplikační vrstvou a databázovou vrstvou stráví značná doba odezvy. I v případě, že je aplikace i databáze ve stejném datovém centru, latence sítě mezi těmito dvěma místy může být zvětšena velkým počtem operací přístupu k datům. Chcete-li snížit síťové odezvy pro operace přístupu k datům, zvažte použití možnosti pro dávkování dotazů ad hoc nebo jejich kompilování jako uložených procedur. Pokud vytváříte dávkování dotazů ad hoc, můžete do databáze odeslat více dotazů jako jednu velkou dávku v rámci jedné cesty. Pokud kompilujete ad hoc dotazy v uložené proceduře, můžete dosáhnout stejného výsledku, jako kdybyste je vyřadíte do dávky. Použití uložené procedury vám také umožní zvýšit pravděpodobnost ukládání plánů dotazů do mezipaměti v databázi, abyste mohli znovu použít uloženou proceduru.

Některé aplikace jsou náročné na zápis. V některých případech můžete snížit celkový počet vstupně-výstupních operací v databázi tím, že se posuzujete, jak dávkové zápisy. Často je to jednoduché jako použití explicitních transakcí namísto transakcí automatického potvrzení v uložených procedurách a dávkách ad hoc. Pro vyhodnocení různých technik, které můžete použít, najdete informace v tématu [techniky dávkování pro databázové aplikace v Azure](../performance-improve-use-batching.md). Vyzkoušejte si vlastní úlohu a Najděte si správný model pro dávkování. Nezapomeňte pochopit, že model může mít mírně odlišné záruky konzistence transakcí. Vyhledání správné úlohy, která minimalizuje využití prostředků, vyžaduje vyhledání správné kombinace kompromisů v konzistenci a výkonu.

### <a name="application-tier-caching"></a>Ukládání do mezipaměti aplikační vrstvy

Některé databázové aplikace mají zatížení náročné na čtení. Ukládání vrstev do mezipaměti může snížit zatížení databáze a může potenciálně snížit výpočetní velikost potřebnou k podpoře databáze pomocí Azure SQL Database a spravované instance Azure SQL. Pokud máte službu [Azure cache pro Redis](https://azure.microsoft.com/services/cache/), můžete v případě, že máte k dispozici velmi náročné úlohy, číst data jednou (nebo případně jednou na stroj na úrovni aplikace v závislosti na tom, jak je nakonfigurované), a pak tato data ukládat mimo vaši databázi. To je způsob, jak snížit zatížení databáze (CPU a čtení v/v), ale má vliv na konzistenci transakcí, protože data čtená z mezipaměti nemusí být synchronizovaná s daty v databázi. I když v mnoha aplikacích je určitá úroveň nekonzistence přijatelná, to pro všechny úlohy není pravdivé. Před implementací strategie ukládání do mezipaměti na úrovni aplikace byste měli plně pochopit všechny požadavky na aplikace.

## <a name="next-steps"></a>Další kroky

- Další informace o úrovních služeb založených na DTU najdete v tématu [nákupní model založený na DTU](service-tiers-dtu.md).
- Další informace o úrovních služeb založených na vCore najdete v tématu [nákupní model založený na Vcore](service-tiers-vcore.md).
- Další informace o elastických fondech najdete v tématu [co je elastický fond Azure?](elastic-pool-overview.md)
- Informace o výkonu a elastických fondech najdete v tématu [kdy zvážit elastický fond](elastic-pool-overview.md) .