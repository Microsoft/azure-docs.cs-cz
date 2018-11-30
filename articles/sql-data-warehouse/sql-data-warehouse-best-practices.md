---
title: Osvědčené postupy pro službu Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Doporučení a osvědčené postupy, které byste měli znát, když budete vyvíjet řešení pro službu Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 11/26/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0324a6f71a0a30fc9f3005a041b4c5413e6af8da
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317298"
---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Osvědčené postupy pro službu Azure SQL Data Warehouse
Tento článek je kolekce doporučené postupy vám pomohou dosáhnout optimálního výkonu služby Azure SQL Data Warehouse.  Některé koncepty v tomto článku jsou základní a snadno se vysvětlují, další koncepty jsou pokročilejší a v tomto článku se jich jenom lehce dotýkáme.  Účelem tohoto článku je poskytnout vám základní pokyny a zvýšit povědomí o důležitých oblastech, na které byste se měli zaměřit, když budete sestavovat svůj datový sklad.  Každá část vám představí nějaký koncept a odkáže vás na podrobnější články, které se danému konceptu věnují více do hloubky.

Pokud se službou Azure SQL Data Warehouse teprve začínáte, nenechte se tímto článkem zahltit.  Témata jsou uspořádaná především podle důležitosti.  Pokud se zpočátku zaměříte na prvních pár konceptů, bude vám to úplně stačit.  Až budete o službě SQL Data Warehouse vědět víc a budete si jistější, vraťte se a prozkoumejte pár dalších konceptů.  Nebude to trvat dlouho a všechno začne dávat smysl.

Pokyny k načítání najdete v tématu [Doprovodné materiály k načítání dat](guidance-for-loading-data.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování
Další informace o snižování nákladů prostřednictvím pozastavování a škálování najdete v tématu [Správa výpočetních služeb](sql-data-warehouse-manage-compute-overview.md). 


## <a name="maintain-statistics"></a>Udržujte statistiky
Na rozdíl od systému SQL Server, který automaticky detekuje a vytváří nebo aktualizuje statistiky nad sloupci, služba SQL Data Warehouse vyžaduje ruční údržbu statistik.  I když to do budoucna plánujeme změnit, zatím si budete muset udržovat statistiky pro zajištění optimalizace plánů služby SQL Data Warehouse.  Plány vytvořené optimalizátorem jsou jenom tak dobré jako dostupné statistiky.  **Vytvoření vzorkové statistiky nad každým sloupcem je snadný způsob, jak začít se statistikami.**  Stejně důležité je aktualizovat statistiky, když dojde k významným změnám vašich dat.  Konzervativní přístup může být denní aktualizace statistik nebo aktualizace po každém načtení.  Vždy existují kompromisy mezi výkonem a náklady na vytvoření a aktualizaci statistik. Pokud si myslíte, že údržba všech vašich statistik trvá příliš dlouho, možná byste měli pečlivěji vybírat sloupce, které mají statistiky, nebo sloupce, které vyžadují časté aktualizace.  Například můžete chtít denně aktualizovat sloupce s datem, do kterých se mohou přidávat nové hodnoty. **Nejvíce výhod získáte tak, že budete mít statistiky nad sloupci používanými ve spojeních, sloupci používanými v klauzuli WHERE a sloupci používanými v příkazu GROUP BY.**

Viz také [Správa statistik tabulek][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS].

## <a name="group-insert-statements-into-batches"></a>Seskupujte příkazy INSERT do dávek
Pro jednorázové nahrávání do malé tabulky příkazem INSERT nebo dokonce i opakované načítání vyhledávání si můžete vystačit s příkazem jako třeba `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Pokud však v průběhu dne potřebujete načíst tisíce nebo miliony řádků, můžete zjistit, že vám příkazy INSERT s jedním prvkem jednoduše nestačí.  Místo toho vyvíjejte své procesy tak, aby zapisovaly do souboru, který bude pravidelně kontrolovat a načítat další proces.

Viz také [INSERT][INSERT].

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Použijte PolyBase k rychlému načítání a exportu dat
SQL Data Warehouse podporuje načítání a export dat prostřednictvím různých nástrojů, včetně Azure Data Factory, PolyBase a BCP.  Pro malá množství dat, kde není výkon tak důležitý, by vám měl stačit libovolný nástroj.  Když však načítáte nebo exportujete velká množství dat nebo vyžadujete vysoký výkon, nejlepší volbou je PolyBase.  PolyBase je navržena pro využití architektury MPP (Massively Parallel Processing) služby SQL Data Warehouse a proto bude načítat a exportovat data řádově rychleji, než jakýkoli jiný nástroj.  Úlohy funkce PolyBase můžete spustit pomocí příkazů CTAS nebo INSERT INTO.  **Použití příkazu CTAS minimalizuje protokolování transakce a je to nejrychlejší způsob, jak načíst vaše data.**  Azure Data Factory také podporuje načítání PolyBase a dosáhnout podobné výkonu jako CTAS.  PolyBase podporuje řadu formátů souborů, včetně souborů GZip.  **Pro maximalizaci propustnosti při použití textových souborů GZip rozdělte soubory na 60 nebo více souborů, čímž maximalizujete paralelismus vaší úlohy.**  Pro rychlejší celkovou propustnost zvažte souběžné načítání dat.

Viz také [Načtení dat][Load data], [Průvodce používáním funkce PolyBase][Guide for using PolyBase], [Vzory a strategie načítání služby Azure SQL Data Warehouse][Azure SQL Data Warehouse loading patterns and strategies], [Načtení dat pomocí služby Azure Data Factory][Load Data with Azure Data Factory], [Přesun dat pomocí služby Azure Data Factory][Move data with Azure Data Factory], [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT], [CREATE TABLE AS SELECT (CTAS)][Create table as select (CTAS)].

## <a name="load-then-query-external-tables"></a>Načtěte a následně dotazujte externí tabulky
Přestože lze PolyBase (také označovanou jako externí tabulky) považovat za nejrychlejší způsob načítání dat, není ideální pro dotazy. Tabulky PolyBase služby SQL Data Warehouse aktuálně podporují pouze soubory Azure blob a úložiště Azure Data Lake. Tyto soubory nemají podporu v žádných výpočetních prostředcích.  Z toho důvodu služba SQL Data Warehouse nemůže tuto práci přesměrovat a proto musí číst celý soubor. Aby mohla data číst, načte celý soubor do dočasné tabulky tepmdb.  Proto pokud máte několik dotazů, které budou tato data dotazovat, je lepší data jednou nahrát a nastavit dotazy, aby používaly místní tabulku.

Viz také [Průvodce používáním funkce PolyBase][Guide for using PolyBase].

## <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash
Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.  To umožňuje uživatelům jednoduše začít vyvářet tabulky, aniž by se museli rozhodovat, jakým způsobem se mají jejich tabulky distribuovat.  Výkon tabulek kruhového dotazování může být pro některé úlohy dostatečný, ale ve většině případů bude lépe fungovat výběr distribučního sloupce.  Nejběžnějším příkladem, kdy tabulka distribuovaná podle sloupce zdaleka překoná tabulku kruhového dotazování, je spojení dvou velkých tabulek faktů.  Například pokud máte tabulku objednávek, která se distribuuje podle sloupce id_objednavky a tabulku transakcí, která se také distribuuje podle sloupce id_objednavky, a provedete spojení tabulky objednávek s tabulkou transakcí podle sloupce id_objednavky, z dotazu se stane předávací dotaz, to znamená, že eliminujeme operace přesunu dat.  Méně kroků znamená rychlejší dotaz.  Méně přesunů dat také přispívá ke zrychlení dotazů.  Pouze ji zjednodušené toto vysvětlení. Při načítání distribuované tabulky se ujistěte, že příchozí data nejsou seřazená podle distribučního klíče, zpomalilo by to načítání.  Níže najdete odkazy na mnohem podrobnější informace o tom, jak výběr distribučního sloupce může zvýšit výkon, a jak nadefinovat distribuovanou tabulku v klauzuli WITH příkazu CREATE TABLES.

Viz také [Přehled tabulek][Table overview], [Distribuce tabulky][Table distribution], [Výběr distribuce tabulky][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů
Přestože dělení dat může být velice efektivní pro udržování dat prostřednictvím přepínání oddílů nebo optimalizace prohledávání pomocí eliminace oddílů, příliš mnoho oddílů může zpomalit vaše dotazy.  Strategie vysoce členitého dělení, která může dobře fungovat v systému SQL Server, často nemusí fungovat dobře ve službě SQL Data Warehouse.  Pokud máte příliš mnoho oddílů, může se také snížit efektivita clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 milion řádků.  Mějte na paměti, že služba SQL Data Warehouse za vás na pozadí dělí data do 60 databází, takže pokud vytvoříte tabulku se 100 oddíly, výsledkem bude ve skutečnosti 6000 oddílů.  Každá úloha je jiná, takže nejlepší rada je, abyste s dělením experimentovali a zjistili, co je pro vaši úlohu nejvhodnější.  Zvažte použití nižší členitosti, než jaká by pro vás byla vhodná v systému SQL Server.  Například místo denního dělení zvažte použití týdenního nebo měsíčního dělení.

Viz také [Dělení tabulky][Table partitioning].

## <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí
Příkazy INSERT, UPDATE a DELETE se spouštějí v rámci transakce, a když selžou, musí se transakce odvolat.  Abyste minimalizovali potenciální dlouhé odvolávání, minimalizujte velikost transakcí kdykoli je to možné.  Můžete to provést rozdělením příkazů INSERT, UPDATE a DELETE na části.  Například pokud máte příkaz INSERT a očekáváte, že se bude vykonávat 1 hodinu, tak pokud je to možné, rozdělte příkaz INSERT na 4 části, z nichž každá bude trvat 15 minut.  Abyste snížili riziko odvolání transakce, využijte příkazy s minimálním protokolováním, například CTAS, TRUNCATE, DROP TABLE nebo INSERT k vyprázdnění tabulek.  Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  Například místo provedení příkazu DELETE pro odstranění všech řádků v tabulce, kde datum_objednavky bylo Říjen 2001, můžete data dělit měsíčně a pak vyměnit oddíl s daty za prázdný oddíl z jiné tabulky (viz příklady příkazu ALTER TABLE).  U tabulek bez oddílů zvažte místo příkazu DELETE použití příkazu CTAS k zápisu dat, která chcete v tabulce uchovat.  Pokud provedení příkazu CTAS trvá stejně dlouho, stále je to mnohem bezpečnější operace, protože zahrnuje minimální protokolování transakce a v případě potřeby ji lze rychle zrušit.

Viz také [Vysvětlení transakcí][Understanding transactions], [Optimalizace transakcí][Optimizing transactions], [Dělení tabulky][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [CREATE TABLE AS SELECT (CTAS)][Create table as select (CTAS)].

## <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce
Při definování DDL můžete použitím nejmenšího datového typu, který podporuje vaše data, zvýšit výkon dotazu.  To je obzvlášť důležité pro sloupce typu CHAR a VARCHAR.  Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho sloupce definujte jako VARCHAR, pokud tento typ splňuje všechny požadavky, místo používání NVARCHAR.

Viz také [Přehled tabulek][Table overview], [Typy tabulkových dat][Table data types], [CREATE TABLE][CREATE TABLE].

## <a name="use-temporary-heap-tables-for-transient-data"></a>Použijte dočasné tabulky hald pro přechodná data
Když dočasně umisťujete data ve službě SQL Data Warehouse, možná zjistíte, že použitím tabulky haldy se celý proces zrychlí.  Pokud nahráváte data pouze za účelem jejich přípravy před spuštěním dalších transformací, nahrání tabulky do tabulky haldy bude mnohem rychlejší, než nahrání dat do clusterované tabulky columnstore.  Nahrání dat do dočasné tabulky bude dokonce mnohem rychlejší, než nahrání tabulky do trvalého úložiště.  Dočasné tabulky začínají znakem „#“ a přístup k nim má pouze relace, která je vytvořila, proto mohou fungovat pouze v omezených scénářích.   Tabulky hald se definují v klauzuli WITH příkazu CREATE TABLE.  Pokud používáte dočasnou tabulku, nezapomeňte nad ní také vytvořit statistiky.

Viz také [Dočasné tabulky][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore
Clusterované indexy columnstore jsou jedním z nejefektivnějších způsobů ukládání dat ve službě SQL Data Warehouse.  Vy výchozím nastavení se tabulky ve službě SQL Data Warehouse vytváří jako clusterované columnstore.  Pokud chcete dosáhnout co nejlepšího výkonu dotazů na tabulky columnstore, je důležité mít kvalitní segmenty.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Kvalitu segmentů lze změřit podle počtu řádků v komprimované skupině řádků.  V části [Příčiny nekvalitních indexů columnstore][Causes of poor columnstore index quality] v článku [Indexy tabulky][Table indexes] najdete podrobné pokyny k zjišťování a zlepšování kvality segmentů clusterovaných tabulek columnstore.  Protože columnstore vysoce kvalitní segmenty, které jsou důležité, je vhodné použít ID, které jsou v střední nebo velké třídy prostředků pro načítání dat uživatele. Použití nižší [jednotkách datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) znamená, že chcete přiřadit větší třídu prostředků do vašeho uživatele načítání.

Protože tabulky columnstore zpravidla nebudou vkládat data do komprimovaného segmentu columnstore, dokud v něm nebude více než 1 milion řádků na tabulku, a každá tabulka služby SQL Data Warehouse je rozdělená na 60 tabulek, obecně platí, že tabulky columnstore nepřináší dotazům žádné výhody, pokud tabulka nemá alespoň 60 milionů řádků.  Pro tabulky s méně než 60 miliony řádků může být použití indexu columnstore zbytečné.  Ale také to nemusí vadit.  Kromě toho, pokud svá data dělíte, pamatujte na to, že každý oddíl musí mít alespoň 1 milion řádků, abyste využili výhod clusterovaného indexu columnstore.  Pokud má tabulka 100 oddílů, bude muset mít alespoň 6 miliard řádků, abyste využili výhod clusterovaného úložiště sloupců (60 distribucí × 100 oddílů × 1 milion řádků).  Pokud vaše tabulka v tomto příkladu neobsahuje 6 miliard řádků, buď snižte počet oddílů, nebo místo ní zvažte použití tabulky haldy.  Můžete také experimentovat, abyste zjistili, jestli pomocí tabulky haldy se sekundárními indexy dosáhnete lepšího výkonu než s tabulkou columnstore.

Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  

Viz také [Indexy tabulky][Table indexes], [Průvodce indexy columnstore][Columnstore indexes guide], [Obnovení indexů columnstore][Rebuilding columnstore indexes].

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Použijte větší třídu prostředků k vylepšení výkonu dotazu
SQL Data Warehouse používá skupiny prostředků jako způsob přidělení paměti pro dotazy.  Standardně jsou všichni uživatelé přiřazeni k malé třídě prostředků, která poskytuje 100 MB paměti na distribuci.  Protože vždy existuje 60 distribucí a každé distribuci je poskytnuto minimálně 100 MB paměti, velikost celkově přidělené paměti v systému je 6 000 MB, tedy téměř 6 GB.  Pro určité dotazy, například velká spojení nebo nahrávání do clusterovaných tabulek columnstore, bude větší přidělení paměti přínosem.  U některých dotazů, jako jsou třeba pouhá prohledávání, se výhody neprojeví.  Na druhou stranu využívání velkých tříd prostředků ovlivňuje souběžnost, což byste měli zvážit před přesunutím všech svých uživatelů do velké třídy prostředků.

Viz také [Třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Použijte menší třídu prostředků pro zlepšení souběžnosti
Pokud se vám zdá, že dotazy uživatelů mají dlouhé zpoždění, může to být tím, že jsou vaši uživatelé spuštění ve větší třídě prostředků a spotřebovávají velké množství slotů souběžnosti. To způsobuje, že se ostatní dotazy řadí do fronty.  Pokud chcete zjistit, jestli jsou požadavky uživatelů ve frontě, spusťte příkaz `SELECT * FROM sys.dm_pdw_waits` a zkontrolujte, jestli se vrátí nějaké řádky.

Viz také [Třídy prostředků pro správu úloh](resource-classes-for-workload-management.md), [sys.dm_pdw_waits][sys.dm_pdw_waits].

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Použijte zobrazení dynamických zpráv k monitorování a optimalizaci dotazů
SQL Data Warehouse obsahuje různá zobrazení dynamických zpráv, která mohou sloužit k monitorování provádění dotazů.  Níže uvedený článek o monitorování vás provede podrobnými pokyny k tomu, jak se dívat na podrobnosti prováděného dotazu.  S rychlým vyhledáváním dotazů v těchto zobrazeních dynamických zpráv může pomoci použití možnosti LABEL v dotazech.

Viz také [Monitorování úloh pomocí zobrazení dynamických zpráv][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Další prostředky
Přečtěte si také článek [Řešení potíží][Troubleshooting], ve kterém najdete běžné problémy a jejich řešení.

Pokud jste v tomto článku nenašli, co jste hledali, zkuste použít „Hledání dokumentů“ v levé části této stránky k vyhledávání ve všech dokumentech služby Azure SQL Data Warehouse.  [Fórum pro Azure SQL Data Warehouse] [ Azure SQL Data Warehouse MSDN Forum] je místo, kde můžete pokládat otázky ostatním uživatelům a do skupiny produktů SQL Data Warehouse.  Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  Pokud dáváte přednost pokládání otázek na Stack Overflow, máme také [Fórum pro Azure SQL Data Warehouse na Stack Overflow][Azure SQL Data Warehouse Stack Overflow Forum].

Nakonec můžete použít stránku [Zpětná vazba k službě Azure SQL Data Warehouse][Azure SQL Data Warehouse Feedback] k podání žádostí o funkce.  Přidáním vlastních žádostí nebo hlasováním pro ostatní žádosti nám pomůžete určit prioritu funkcí.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/2015/08/11/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  http://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/
