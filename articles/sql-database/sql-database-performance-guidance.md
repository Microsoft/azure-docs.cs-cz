---
title: Pokyny pro ladění výkonu pro aplikace a databáze
description: Další informace o ladění databázových aplikací a databází pro výkon v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab; jrasnick
ms.date: 03/10/2020
ms.openlocfilehash: 4f30ebe39d86db7076baa8c29b2a5cf060b07bf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255948"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database"></a>Vyladění aplikací a databází na výkon v Azure SQL Database

Jakmile jste zjistili problém s výkonem, kterému čelíte s databází SQL, tento článek je navržen tak, aby vám pomohl:

- Vylaďte aplikaci a použijte některé osvědčené postupy, které mohou zlepšit výkon.
- Vylaďte databázi změnou indexů a dotazů tak, aby efektivněji pracovaly s daty.

Tento článek předpokládá, že jste již pracovali prostřednictvím [doporučení databáze](sql-database-advisor.md) Databáze Azure SQL databáze a [doporučení automatického ladění](sql-database-automatic-tuning.md)Azure SQL Database . Také předpokládá, že jste zkontrolovali [Přehled monitorování a ladění](sql-database-monitor-tune-overview.md) a související články týkající se řešení problémů s výkonem. Kromě toho tento článek předpokládá, že nemáte prostředky procesoru, spuštěný problém s výkonem, který lze vyřešit zvýšením výpočetní velikosti nebo úrovně služby poskytnout další prostředky do databáze.

## <a name="tune-your-application"></a>Vyladění aplikace

V tradiční místní SQL Server proces plánování počáteční kapacity je často oddělenod procesu spuštění aplikace v produkčním prostředí. Nejprve se nakupují licence hardwaru a produktů a ladění výkonu se provádí později. Při použití Azure SQL Database, je vhodné propojit proces spuštění aplikace a ladění. S modelem placení kapacity na vyžádání můžete vyladit aplikaci tak, aby používala minimální prostředky potřebné nyní, namísto nadměrného zřizování hardwaru na základě dohadů o budoucích plánech růstu pro aplikaci, které jsou často nesprávné. Někteří zákazníci se mohou rozhodnout, že nebudou vyladit aplikaci, a místo toho se rozhodnou přezřivat hardwarové prostředky. Tento přístup může být vhodné, pokud nechcete změnit klíčové aplikace během rušného období. Ale optimalizace aplikace můžete minimalizovat požadavky na prostředky a nižší měsíční účty při použití úrovně služeb v Azure SQL Database.

### <a name="application-characteristics"></a>Charakteristiky aplikace

Přestože vrstvy služby Azure SQL Database jsou navržené ke zlepšení stability výkonu a předvídatelnosti pro aplikaci, některé osvědčené postupy vám můžou pomoct vyladit vaši aplikaci tak, aby lépe využívala prostředky ve výpočetní velikosti. Přestože mnoho aplikací mají významné zvýšení výkonu jednoduše přepnutím na vyšší výpočetní velikost nebo úroveň služby, některé aplikace potřebují další ladění těžit z vyšší úrovně služby. Pro zvýšení výkonu zvažte další ladění aplikací pro aplikace, které mají tyto vlastnosti:

- **Aplikace, které mají pomalý výkon z důvodu "upovídaný" chování**

  Chattá aplikace provádět nadměrné operace přístupu k datům, které jsou citlivé na latenci sítě. Možná budete muset upravit tyto druhy aplikací snížit počet operací přístupu k datům do databáze SQL. Můžete například zlepšit výkon aplikace pomocí technik, jako je dávkování dotazů ad hoc nebo přesunutí dotazů na uložené procedury. Další informace naleznete v [tématu Dávkové dotazy](#batch-queries).

- **Databáze s intenzivním zatížením, které nemůže být podporováno celým jedním počítačem**

   Databáze, které překračují prostředky nejvyšší velikosti výpočetních prostředků Premium může mít prospěch z horizontálnínavýšení kapacity úlohy. Další informace naleznete [v tématu Dělení](#cross-database-sharding) mezi databázemi a funkční [dělení](#functional-partitioning).

- **Aplikace, které mají neoptimální dotazy**

  Aplikace, zejména ve vrstvě přístupu k datům, které mají špatně vyladěné dotazy nemusí mít prospěch z vyšší výpočetní velikosti. To zahrnuje dotazy, které postrádají klauzuli WHERE, mají chybějící indexy nebo mají zastaralé statistiky. Tyto aplikace využívají standardní techniky ladění výkonu dotazu. Další informace naleznete [v tématu Chybějící indexy](#identifying-and-adding-missing-indexes) a [optimalizace dotazů a rady při psaní rad](#query-tuning-and-hinting).

- **Aplikace, které mají neoptimální návrh přístupu k datům**

   Aplikace, které mají vlastní problémy souběžnosti přístupu k datům, například zablokování, nemusí mít prospěch z vyšší výpočetní velikosti. Zvažte snížení zpáteční chod ů proti Azure SQL Database ukládáním dat na straně klienta pomocí služby Azure Caching nebo jiné technologie ukládání do mezipaměti. Viz [Ukládání do mezipaměti aplikační vrstvy](#application-tier-caching).

## <a name="tune-your-database"></a>Vyladění databáze

V této části se podíváme na některé techniky, které můžete použít k optimalizaci Azure SQL Database získat nejlepší výkon pro vaši aplikaci a spustit ji na nejnižší možnou výpočetní velikost. Některé z těchto technik odpovídají tradičním osvědčeným postupům ladění serveru SQL Server, ale jiné jsou specifické pro Azure SQL Database. V některých případech můžete prozkoumat spotřebované prostředky pro databázi najít oblasti pro další ladění a rozšíření tradiční SQL Server techniky pro práci v Azure SQL Database.

### <a name="identifying-and-adding-missing-indexes"></a>Identifikace a přidání chybějících indexů

Běžný problém ve výkonu databáze OLTP se týká návrhu fyzické databáze. Často jsou schémata databáze navržena a dodávána bez testování ve velkém měřítku (buď při zatížení, nebo ve svazku dat). Bohužel výkon plánu dotazů může být přijatelné v malém měřítku, ale podstatně snížit v rámci objemu dat na úrovni výroby. Nejběžnějším zdrojem tohoto problému je nedostatek vhodných indexů pro splnění filtrů nebo jiných omezení v dotazu. Často chybějící indexy manifesty jako prohledávač tabulky při hledání indexu může stačit.

V tomto příkladu používá vybraný plán dotazů prohledávanost, když by stačilo hledání:

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

![Plán dotazů s chybějícími indexy](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database vám může pomoct najít a opravit běžné chybějící podmínky indexu. DMVs, které jsou integrovány do Azure SQL Database podívejte se na kompilace dotazů, ve kterém index by výrazně snížit odhadované náklady na spuštění dotazu. Během provádění dotazu databáze SQL sleduje, jak často je každý plán dotazu spuštěn a sleduje odhadovanou mezeru mezi spuštěním plánu dotazů a imaginárním plánem, kde tento index existoval. Pomocí těchto zařízení DMV můžete rychle odhadnout, které změny návrhu fyzické databáze mohou zlepšit celkové náklady na pracovní vytížení databáze a její skutečné zatížení.

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

V tomto příkladu dotaz vyústil v tento návrh:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Po jeho vytvoření stejný příkaz SELECT vybere jiný plán, který používá hledání namísto skenování a pak provede plán efektivněji:

![Plán dotazů s opravenými indexy](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Klíčovým poznatkem je, že kapacita vi sdílených komoditních systémů je omezenější než kapacita dedikovaného serverového stroje. Je tu prémie na minimalizaci zbytečné vstupně-up využít maximální využití systému v DTU každé výpočetní velikosti úrovně služby Azure SQL Database. Vhodné možnosti návrhu fyzické databáze mohou výrazně zlepšit latenci pro jednotlivé dotazy, zlepšit propustnost souběžných požadavků zpracovaných na jednotku škálování a minimalizovat náklady potřebné ke splnění dotazu. Další informace o chybějící index DMVs naleznete [v sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Optimalizace dotazu a rady při psaní rad

Optimalizátor dotazů v Azure SQL Database je podobný tradičnímu optimalizátoru dotazů sql serveru. Většina osvědčených postupů pro ladění dotazů a pochopení omezení modelu uvažování pro optimalizaci dotazů platí také pro Azure SQL Database. Pokud vyladíte dotazy v Azure SQL Database, můžete získat další výhodu snížení agregační chprostředků požadavky. Vaše aplikace může být možné spustit s nižší náklady než un-tuned ekvivalent, protože může běžet s nižší výpočetní velikost.

Příkladem, který je běžný v SQL Serveru a který platí také pro Azure SQL Database je, jak parametry optimalizace dotazů "sniffs". Během kompilace optimalizátor dotazů vyhodnotí aktuální hodnotu parametru k určení, zda může generovat optimální plán dotazu. I když tato strategie často může vést k plánu dotazů, který je výrazně rychlejší než plán zkompilovaný bez známých hodnot parametrů, v současné době funguje nedokonale jak v SQL Serveru, tak v Azure SQL Database. Někdy parametr není sniffed a někdy je parametr sniffed, ale generovaný plán je suboptimální pro úplnou sadu hodnot parametrů v zatížení. Společnost Microsoft obsahuje rady při psaní dotazu (direktivy), takže můžete určit záměr více záměrně a přepsat výchozí chování parametru čichání. Často pokud používáte rady, můžete opravit případy, ve kterých výchozí SQL Server nebo Azure SQL Database chování je nedokonalé pro konkrétní zatížení zákazníka.

Další příklad ukazuje, jak procesor dotazu můžete generovat plán, který je neoptimální pro požadavky na výkon a prostředky. Tento příklad také ukazuje, že pokud použijete nápovědu k dotazu, můžete snížit čas běhu dotazu a požadavky na prostředky pro databázi SQL:

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

Kód nastavení vytvoří tabulku, která má zkosené rozdělení dat. Optimální plán dotazu se liší podle toho, který parametr je vybrán. Bohužel chování ukládání do mezipaměti plánu není vždy překompilovat dotaz na základě nejběžnější hodnoty parametru. Takže je možné, že neoptimální plán bude uložen do mezipaměti a použit pro mnoho hodnot, i když jiný plán může být v průměru lepší volbou plánu. Potom plán dotazu vytvoří dvě uložené procedury, které jsou identické, s tím rozdílem, že jeden má zvláštní nápovědu dotazu.

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

Doporučujeme počkat alespoň 10 minut před zahájením části 2 příkladu tak, aby výsledky jsou odlišné ve výsledných telemetrických dat.

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

Každá část tohoto příkladu se pokusí spustit parametrizovaný příkaz insert 1 000krát (pro generování dostatečného zatížení pro použití jako sada testovacích dat). Při spuštění uložené procedury, procesor dotazu zkontroluje hodnotu parametru, který je předán do procedury během jeho první kompilace (parametr "čichání"). Procesor uloží výsledný plán do mezipaměti a použije jej pro pozdější vyvolání, i když se hodnota parametru liší. Optimální plán nemusí být použit ve všech případech. Někdy je třeba provést optimalizátor vybrat plán, který je lepší pro průměrný případ, spíše než konkrétní případ z kdy byl dotaz poprvé zkompilován. V tomto příkladu počáteční plán generuje plán "prohledávat", který přečte všechny řádky a vyhledá každou hodnotu, která odpovídá parametru:

![Optimalizace dotazů pomocí plánu skenování](./media/sql-database-performance-guidance/query_tuning_1.png)

Protože jsme provedli postup pomocí hodnoty 1, výsledný plán byl optimální pro hodnotu 1, ale byl sub-optimální pro všechny ostatní hodnoty v tabulce. Výsledek pravděpodobně není to, co byste chtěli, kdybyste si vybrali každý plán náhodně, protože plán provádí pomaleji a používá více prostředků.

Pokud spustíte test `SET STATISTICS IO` s `ON`nastavenou na , logická prohledávací práce v tomto příkladu se provádí na pozadí. Můžete vidět, že existuje 1 148 čtení provádí plán (což je neefektivní, pokud je průměrný případ vrátit pouze jeden řádek):

![Optimalizace dotazů pomocí logického prohledávání](./media/sql-database-performance-guidance/query_tuning_2.png)

Druhá část příkladu používá nápovědu k dotazu sdělit optimalizátoru použít určitou hodnotu během procesu kompilace. V tomto případě vynutí procesor dotazu ignorovat hodnotu, která je `UNKNOWN`předána jako parametr a místo toho převzít . To se týká hodnoty, která má průměrnou frekvenci v tabulce (ignorování zkosení). Výsledný plán je plán založený na hledání, který je rychlejší a používá v průměru méně prostředků než plán v části 1 tohoto příkladu:

![Optimalizace dotazu pomocí nápovědy k dotazu](./media/sql-database-performance-guidance/query_tuning_3.png)

Efekt se zobrazí v tabulce **sys.resource_stats** (dochází ke zpoždění od okamžiku, kdy test provedete, a při naplnění dat v tabulce). V tomto příkladu část 1 proveden během časového okna 22:25:00 a část 2 byla spuštěna ve 22:35:00. Dřívější časové okno používá více prostředků v tomto časovém okně než pozdější (z důvodu zlepšení efektivity plánu).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Příklad y optimalizace dotazu](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Přestože je svazek v tomto příkladu záměrně malý, účinek neoptimálních parametrů může být značný, zejména na větších databázích. Rozdíl, v extrémních případech, může být mezi sekund pro rychlé případy a hodiny pro pomalé případy.

Můžete prozkoumat **sys.resource_stats** k určení, zda prostředek pro test používá více nebo méně prostředků než jiný test. Při porovnávání dat oddělte časování testů tak, aby nebyly ve stejném 5minutovém okně v zobrazení **sys.resource_stats.** Cílem cvičení je minimalizovat celkové množství použitých prostředků a nikoli minimalizovat zdroje ve špičce. Obecně platí optimalizace část kódu pro latenci také snižuje spotřebu prostředků. Ujistěte se, že změny provedené v aplikaci jsou nezbytné a že změny nemají negativní vliv na prostředí zákazníka pro někoho, kdo může používat rady při psaní dotazu v aplikaci.

Pokud má úloha sadu opakujících se dotazů, často má smysl zachytit a ověřit optimalitu voleb plánu, protože řídí jednotku s minimální velikostí prostředků potřebnou k hostování databáze. Po ověření, občas znovu prozkoumat plány, které vám pomohou ujistěte se, že nebyly degradovány. Další informace o tipech na [dotazy (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="very-large-database-architectures"></a>Velmi velké databázové architektury

Před vydáním úrovně služeb [Hyperscale](sql-database-service-tier-hyperscale.md) pro jednotlivé databáze v Azure SQL Database zákazníci, kteří se používají k přístupu na omezení kapacity pro jednotlivé databáze. Tato omezení kapacity stále existují pro sdružené databáze v elastických fondech a databázi instancí ve spravovaných instancích. Následující dvě části popisují dvě možnosti řešení problémů s velmi rozsáhlými databázemi v Azure SQL Database, když nemůžete použít úroveň služby Hyperscale.

### <a name="cross-database-sharding"></a>Sníst mezi databázemi

Vzhledem k tomu, že Azure SQL Database běží na komoditní hardware, omezení kapacity pro jednotlivé databáze jsou nižší než pro tradiční místní instalace SQL Serveru. Někteří zákazníci používají techniky syní k rozložení databázových operací na více databází, když se operace nevejdou do limitů jednotlivé databáze v Azure SQL Database. Většina zákazníků, kteří používají techniky rozdělení serážování v Azure SQL Database rozdělit svá data na jednu dimenzi na více databází. Pro tento přístup je třeba pochopit, že aplikace OLTP často provádět transakce, které se vztahují pouze na jeden řádek nebo na malou skupinu řádků ve schématu.

> [!NOTE]
> SQL Database nyní poskytuje knihovnu, která pomáhá s říšnictvím. Další informace naleznete v [tématu Přehled klientské knihovny elastické databáze](sql-database-elastic-database-client-library.md).

Pokud například databáze obsahuje jméno zákazníka, objednávku a podrobnosti objednávky (například tradiční příklad databáze Northwind, která je dodávána se serverem SQL Server), můžete tato data rozdělit do více databází seskupením zákazníka se souvisejícími podrobnostmi objednávky a objednávky. Informace. Můžete zaručit, že data zákazníka zůstanou v individuální databázi. Aplikace by rozdělit různé zákazníky mezi databázemi, efektivně rozprostření zatížení mezi více databází. Díky rozdělení na všechny oddíly se zákazníci nejen mohou vyhnout maximálnímu limitu velikosti databáze, ale Azure SQL Database také může zpracovávat úlohy, které jsou výrazně větší než limity různých velikostí výpočetních prostředků, pokud se každá jednotlivá databáze vejde do jeho DTU.

Přestože je selhání databáze nesnižuje agregotní kapacitu prostředků pro řešení, je vysoce efektivní při podpoře velmi velkých řešení, která jsou rozložena do více databází. Každá databáze může běžet s jinou velikostí výpočetních prostředků pro podporu velmi velkých, "efektivní" databáze s vysokými požadavky na prostředky.

#### <a name="functional-partitioning"></a>Funkční dělení

Uživatelé serveru SQL Server často kombinují mnoho funkcí v jednotlivých databázích. Pokud má například aplikace logiku pro správu zásob pro úložiště, může mít tato databáze logiku přidruženou k zásobám, sledování nákupních objednávek, uloženým procedurám a indexovaným nebo materializovaným zobrazením, která spravují sestavy na konci měsíce. Tato technika usnadňuje správu databáze pro operace, jako je zálohování, ale také vyžaduje velikost hardwaru pro zpracování zatížení ve špičce ve všech funkcích aplikace.

Pokud používáte architekturu horizontálnínavýšení kapacity v Azure SQL Database, je vhodné rozdělit různé funkce aplikace do různých databází. Pomocí této techniky se každá aplikace škáluje nezávisle. Jako aplikace se stává rušnější (a zatížení databáze zvyšuje), správce můžete zvolit nezávislé výpočetní velikosti pro každou funkci v aplikaci. Na limit, s touto architekturou aplikace může být větší než jeden komoditní počítač může zpracovat, protože zatížení je rozloženo do více počítačů.

### <a name="batch-queries"></a>Dávkové dotazy

U aplikací, které přistupují k datům pomocí vysokoobjemového, častého dotazování ad hoc, se značná doba odezvy stráví na síťové komunikaci mezi aplikační vrstvou a vrstvou Azure SQL Database. I v případě, že aplikace a Azure SQL Database jsou ve stejném datovém centru, latence sítě mezi těmito dvěma může být zvětšena velkým počtem operací přístupu k datům. Chcete-li snížit síťové zpáteční cesty pro operace přístupu k datům, zvažte použití možnosti buď dávkové dotazy ad hoc, nebo je zkompilovat jako uložené procedury. Pokud dávkově ad hoc dotazy, můžete odeslat více dotazů jako jednu velkou dávku v jedné cestě do Azure SQL Database. Pokud kompilujete dotazy ad hoc v uložené proceduře, můžete dosáhnout stejného výsledku, jako kdybyste je dávkově. Použití uložené procedury také poskytuje výhodu zvýšení pravděpodobnosti ukládání do mezipaměti plány dotazů v Azure SQL Database, takže můžete použít uloženou proceduru znovu.

Některé aplikace jsou náročné na zápis. Někdy můžete snížit celkové zatížení vi v databázi tím, že zvažuje, jak dávkové zápisy dohromady. Často je to stejně jednoduché jako použití explicitní transakce namísto transakce automaticképotvrzení v uložené procedury a ad hoc dávky. Vyhodnocení různých technik, které můžete použít, najdete v článku [Dávkování techniky pro aplikace sql database v Azure](sql-database-use-batching-to-improve-performance.md). Experimentujte s vlastním zatížením a najděte správný model pro dávkování. Ujistěte se, že pochopit, že model může mít mírně odlišné záruky transakční konzistence. Nalezení správné úlohy, která minimalizuje využití prostředků, vyžaduje nalezení správné kombinace konzistence a kompromisů v oblasti výkonu.

### <a name="application-tier-caching"></a>Ukládání do mezipaměti aplikační vrstvy

Některé databázové aplikace mají úlohy náročné na čtení. Ukládání do mezipaměti vrstvy může snížit zatížení databáze a může potenciálně snížit výpočetní velikost potřebnou pro podporu databáze pomocí Azure SQL Database. S [Azure Cache for Redis](https://azure.microsoft.com/services/cache/), pokud máte zatížení náročné na čtení, můžete číst data jednou (nebo možná jednou na počítač aplikační vrstvy, v závislosti na tom, jak je nakonfigurován) a pak uložit data mimo databázi SQL. Toto je způsob, jak snížit zatížení databáze (PROCESOR a číst vi), ale je vliv na transakční konzistenci, protože data čtená z mezipaměti může být synchronizována s daty v databázi. I když v mnoha aplikacích je přijatelná určitá úroveň nekonzistence, to neplatí pro všechny úlohy. Před implementací strategie ukládání do mezipaměti aplikační vrstvy byste měli plně pochopit všechny požadavky na aplikaci.

## <a name="next-steps"></a>Další kroky

- Další informace o úrovních služeb založených na DTU naleznete v [tématu nákupní model založený na DTU](sql-database-service-tiers-dtu.md).
- Další informace o úrovních služeb založených na virtuálních jádrech naleznete v [nákupním modelu založeném na virtuálních jádrech](sql-database-service-tiers-vcore.md).
- Další informace o elastických fondech najdete [v tématu Co je elastický fond Azure?](sql-database-elastic-pool.md)
- Informace o výkonu a elastické fondy, viz [Kdy zvážit elastický fond](sql-database-elastic-pool-guidance.md)
