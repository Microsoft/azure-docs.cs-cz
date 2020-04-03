---
title: Doporučené postupy pro SQL Analytics v Azure Synapse Analytics (dříve SQL DW)
description: Doporučení a doporučené postupy pro vývoj řešení pro SQL Analytics v Azure Synapse Analytics (dříve SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 44dbc03a41cfde94c344ae331b21d7536778050c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619101"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Doporučené postupy pro SQL Analytics v Azure Synapse Analytics (dříve SQL DW)

Tento článek je kolekce osvědčených postupů, které vám pomohou dosáhnout optimálního výkonu z nasazení [SQL Analytics.](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse)  Účelem tohoto článku je poskytnout vám některé základní pokyny a upozornit na důležité oblasti zaměření.  Každá část vás seznámí s konceptem a pak vás naukazuje na podrobnější články, které pokrývají koncept do větší hloubky. Pořadí témat je v pořadí podle důležitosti. 

## <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování

Další informace o snižování nákladů prostřednictvím pozastavování a škálování najdete v tématu [Správa výpočetních služeb](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Udržujte statistiky
Azure SQL Data Warehouse můžete nakonfigurovat tak, aby automaticky zjišťovala a vytvářela statistiky ve sloupcích.  Plány dotazů vytvořené optimalizátorem jsou pouze tak dobré jako dostupné statistiky.  Doporučujeme povolit AUTO_CREATE_STATISTICS pro vaše databáze a udržovat statistiky aktualizovány denně nebo po každém zatížení, abyste zajistili, že statistiky o sloupcích použitých ve vašich dotazech jsou vždy aktuální. 

Pokud zjistíte, že aktualizace všech statistik trvá příliš dlouho, můžete se pokusit být selektivnější ohledně toho, které sloupce vyžadují časté aktualizace statistik. Například můžete chtít denně aktualizovat sloupce s datem, do kterých se mohou přidávat nové hodnoty. **Největší užitek získáte tím, že budete mít aktualizované statistiky o sloupcích zapojených do spojení, sloupcích použitých v klauzuli WHERE a sloupcích nalezených v souborech GROUP BY.**

Viz také [Správa statistik tabulek][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS].

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Použijte zobrazení dynamických zpráv k monitorování a optimalizaci dotazů
SQL Analytics má několik DMVs, které lze použít ke sledování provádění dotazů.  Níže uvedený článek o monitorování vás provede podrobnými pokyny k tomu, jak se dívat na podrobnosti prováděného dotazu.  S rychlým vyhledáváním dotazů v těchto zobrazeních dynamických zpráv může pomoci použití možnosti LABEL v dotazech.

Viz také [Monitorování úloh pomocí zobrazení dynamických zpráv][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits].

## <a name="tune-query-performance-with-new-product-enhancements"></a>Vyladění výkonu dotazů pomocí nových vylepšení produktů
- [Ladění výkonu s využitím materializovaných zobrazení](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Ladění výkonu s využitím uspořádaného clusterovaného indexu columnstore](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Ladění výkonu s využitím ukládání sad výsledků do mezipaměti](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>Seskupujte příkazy INSERT do dávek
Pro jednorázové nahrávání do malé tabulky příkazem INSERT nebo dokonce i opakované načítání vyhledávání si můžete vystačit s příkazem jako třeba `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Pokud však v průběhu dne potřebujete načíst tisíce nebo miliony řádků, můžete zjistit, že vám příkazy INSERT s jedním prvkem jednoduše nestačí.  Místo toho vyvíjejte své procesy tak, aby zapisovaly do souboru, který bude pravidelně kontrolovat a načítat další proces.

Viz také [INSERT][INSERT].

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Použijte PolyBase k rychlému načítání a exportu dat

 SQL Analytics podporuje načítání a export dat prostřednictvím několika nástrojů, včetně Azure Data Factory, PolyBase a BCP.  Pro malá množství dat, kde není výkon tak důležitý, by vám měl stačit libovolný nástroj.  Když však načítáte nebo exportujete velká množství dat nebo vyžadujete vysoký výkon, nejlepší volbou je PolyBase.  
 
 PolyBase je navržen tak, aby využít architekturu MPP (Masivně paralelní zpracování) a bude načítat a exportovat velikosti dat rychleji než jakýkoli jiný nástroj.  Úlohy funkce PolyBase můžete spustit pomocí příkazů CTAS nebo INSERT INTO.  **Použití příkazu CTAS minimalizuje protokolování transakce a je to nejrychlejší způsob, jak načíst vaše data.** 
 
  Azure Data Factory také podporuje zatížení PolyBase a může dosáhnout podobného výkonu jako CTAS.  PolyBase podporuje řadu formátů souborů, včetně souborů GZip.  **Chcete-li maximalizovat propustnost při použití textových souborů gzip, rozdělte soubory do 60 nebo více souborů, abyste maximalizovali paralelismus zatížení.**  Pro rychlejší celkovou propustnost zvažte souběžné načítání dat.

Viz také [Načtení dat][Load data], [Průvodce pro použití PolyBase][Guide for using PolyBase], sql pool načítání vzory a [strategie][Azure SQL Data Warehouse loading patterns and strategies], [Načíst data s Azure Data Factory][Load Data with Azure Data Factory], [Přesunout data s Azure Data Factory][Move data with Azure Data Factory], vytvořit externí formát [souboru][CREATE EXTERNAL FILE FORMAT], [vytvořit tabulku jako výběr (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Načtěte a následně dotazujte externí tabulky
Přestože lze PolyBase (také označovanou jako externí tabulky) považovat za nejrychlejší způsob načítání dat, není ideální pro dotazy. Tabulky Polybase aktuálně podporují jenom soubory objektů blob Azure a úložiště Azure Data Lake. Tyto soubory nemají podporu v žádných výpočetních prostředcích.  

V důsledku toho SQL Analytics nelze převést tuto práci a proto musí číst celý soubor načtením do tempdb za účelem čtení dat.  Proto pokud máte několik dotazů, které budou tato data dotazovat, je lepší data jednou nahrát a nastavit dotazy, aby používaly místní tabulku.

Viz také [Průvodce používáním funkce PolyBase][Guide for using PolyBase].

## <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash
Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.  To umožňuje uživatelům jednoduše začít vyvářet tabulky, aniž by se museli rozhodovat, jakým způsobem se mají jejich tabulky distribuovat.  Výkon tabulek kruhového dotazování může být pro některé úlohy dostatečný, ale ve většině případů bude lépe fungovat výběr distribučního sloupce.  Nejběžnějším příkladem, kdy tabulka distribuovaná podle sloupce zdaleka překoná tabulku kruhového dotazování, je spojení dvou velkých tabulek faktů.  

Například pokud máte tabulku objednávek, která se distribuuje podle sloupce id_objednavky a tabulku transakcí, která se také distribuuje podle sloupce id_objednavky, a provedete spojení tabulky objednávek s tabulkou transakcí podle sloupce id_objednavky, z dotazu se stane předávací dotaz, to znamená, že eliminujeme operace přesunu dat.  Méně kroků znamená rychlejší dotaz.  Méně přesunů dat také přispívá ke zrychlení dotazů.  

Při načítání distribuované tabulky se ujistěte, že příchozí data nejsou seřazená podle distribučního klíče, zpomalilo by to načítání.  Další podrobnosti o tom, jak může výběr distribučního sloupce zlepšit výkon a jak definovat distribuovanou tabulku v klauzuli WITH příkazu CREATE TABLE, naleznete v následujících odkazech.

Viz také [Přehled tabulek][Table overview], [Distribuce tabulky][Table distribution], [Výběr distribuce tabulky][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů
Zatímco rozdělení dat může být efektivní pro údržbu dat prostřednictvím přepínání oddílů nebo optimalizace skenování pomocí eliminace oddílu, s příliš mnoho oddílů může zpomalit vaše dotazy.  Často vysoká rozlišovací schopnost dělení strategie, které mohou fungovat dobře na SQL Server nemusí fungovat dobře v SQL Analytics.  

Pokud máte příliš mnoho oddílů, může se také snížit efektivita clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 milion řádků.  Mějte na paměti, že na pozadí SQL Analytics rozdělí vaše data za vás do 60 databází, takže pokud vytvoříte tabulku se 100 oddíly, ve skutečnosti to má za následek 6000 oddílů.  

Každá úloha je jiná, takže nejlepší rada je, abyste s dělením experimentovali a zjistili, co je pro vaši úlohu nejvhodnější.  Zvažte použití nižší členitosti, než jaká by pro vás byla vhodná v systému SQL Server.  Například místo denního dělení zvažte použití týdenního nebo měsíčního dělení.

Viz také [Dělení tabulky][Table partitioning].

## <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí
Příkazy INSERT, UPDATE a DELETE se spouštějí v rámci transakce, a když selžou, musí se transakce odvolat.  Abyste minimalizovali potenciální dlouhé odvolávání, minimalizujte velikost transakcí kdykoli je to možné.  Můžete to provést rozdělením příkazů INSERT, UPDATE a DELETE na části.  

Například pokud máte INSERT, který očekáváte, že trvat 1 hodinu, pokud je to možné, rozdělit INSERT do čtyř částí, které budou každá spuštěna za 15 minut.  Abyste snížili riziko odvolání transakce, využijte příkazy s minimálním protokolováním, například CTAS, TRUNCATE, DROP TABLE nebo INSERT k vyprázdnění tabulek.  

Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  Například místo provedení příkazu DELETE pro odstranění všech řádků v tabulce, kde datum_objednavky bylo Říjen 2001, můžete data dělit měsíčně a pak vyměnit oddíl s daty za prázdný oddíl z jiné tabulky (viz příklady příkazu ALTER TABLE).  

Pro tabulky bez oddílů zvažte použití CTAS k zápisu dat, která chcete zachovat v tabulce, nikoli pomocí příkazu DELETE.  Pokud CTAS trvá stejné množství času, je mnohem bezpečnější operace spustit, protože má minimální protokolování transakcí a může být zrušena rychle v případě potřeby.

Viz také [Vysvětlení transakcí][Understanding transactions], [Optimalizace transakcí][Optimizing transactions], [Dělení tabulky][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [CREATE TABLE AS SELECT (CTAS)][Create table as select (CTAS)].

## <a name="reduce-query-result-sizes"></a>Zmenšení velikosti výsledků dotazu  
Tento krok vám pomůže vyhnout se problémům na straně klienta způsobeným velkým výsledkem dotazu.  Dotaz můžete upravit a snížit tak počet vrácených řádků. Některé nástroje pro generování dotazu umožňují přidat syntaxi "top N" do každého dotazu.  Můžete také CETAS výsledek dotazu do dočasné tabulky a potom použít Export PolyBase pro zpracování nižší úrovně.

## <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce
Při definování DDL, pomocí nejmenší datový typ, který bude podporovat vaše data zlepší výkon dotazu.  To je obzvlášť důležité pro sloupce typu CHAR a VARCHAR.  Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho sloupce definujte jako VARCHAR, pokud tento typ splňuje všechny požadavky, místo používání NVARCHAR.

Viz také [Přehled tabulek][Table overview], [Typy tabulkových dat][Table data types], [CREATE TABLE][CREATE TABLE].

## <a name="use-temporary-heap-tables-for-transient-data"></a>Použijte dočasné tabulky hald pro přechodná data
Když dočasně vysouváte data, můžete zjistit, že použití tabulky haldy urychlí celý proces.  Pokud nahráváte data pouze za účelem jejich přípravy před spuštěním dalších transformací, nahrání tabulky do tabulky haldy bude mnohem rychlejší, než nahrání dat do clusterované tabulky columnstore.  

Nahrání dat do dočasné tabulky bude dokonce mnohem rychlejší, než nahrání tabulky do trvalého úložiště.  Dočasné tabulky začínají "#" a jsou přístupné pouze relace, která ji vytvořila, takže mohou fungovat pouze v omezených scénářích.   Tabulky hald se definují v klauzuli WITH příkazu CREATE TABLE.  Pokud používáte dočasnou tabulku, nezapomeňte nad ní také vytvořit statistiky.

Viz také [Dočasné tabulky][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore
Clusterované indexy columnstore jsou jedním z nejúčinnějších způsobů ukládání dat v SQL Analytics.  Ve výchozím nastavení jsou tabulky v SQL Analytics vytvořeny jako Clustered ColumnStore.  Pokud chcete dosáhnout co nejlepšího výkonu dotazů na tabulky columnstore, je důležité mít kvalitní segmenty.  

Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Kvalitu segmentů lze změřit podle počtu řádků v komprimované skupině řádků.  V části [Příčiny nekvalitních indexů columnstore][Causes of poor columnstore index quality] v článku [Indexy tabulky][Table indexes] najdete podrobné pokyny k zjišťování a zlepšování kvality segmentů clusterovaných tabulek columnstore.  

Vzhledem k tomu, že vysoce kvalitní segmenty columnstore jsou důležité, je vhodné použít ID uživatelů, které jsou ve střední nebo velké třídy prostředků pro načítání dat. Použití [nižšíjednotky datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) znamená, že chcete přiřadit větší třídu prostředků pro načítacího uživatele.

Vzhledem k tomu, columnstore tabulky obecně nebude nabízená data do segmentu komprimované columnstore, dokud existuje více než 1 milion řádků na tabulku a každá tabulka SQL Analytics je rozdělena do 60 tabulek, jako pravidlo, columnstore tabulky nebude mít prospěch dotazu, pokud tabulka obsahuje více než 60 milionů řádků.  Pro tabulky s méně než 60 miliony řádků může být použití indexu columnstore zbytečné.  Ale také to nemusí vadit.  

Kromě toho, pokud svá data dělíte, pamatujte na to, že každý oddíl musí mít alespoň 1 milion řádků, abyste využili výhod clusterovaného indexu columnstore.  Pokud má tabulka 100 oddílů, bude muset mít alespoň 6 miliard řádků, abyste využili výhod clusterovaného úložiště sloupců (60 distribucí × 100 oddílů × 1 milion řádků).  

Pokud vaše tabulka v tomto příkladu neobsahuje 6 miliard řádků, buď snižte počet oddílů, nebo místo ní zvažte použití tabulky haldy.  Můžete také experimentovat, abyste zjistili, jestli pomocí tabulky haldy se sekundárními indexy dosáhnete lepšího výkonu než s tabulkou columnstore.

Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  

Viz také [Indexy tabulky][Table indexes], [Průvodce indexy columnstore][Columnstore indexes guide], [Obnovení indexů columnstore][Rebuilding columnstore indexes].

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Použijte větší třídu prostředků k vylepšení výkonu dotazu
SQL Analytics používá skupiny prostředků jako způsob, jak přidělit paměť dotazům.  Po vybalení jsou všichni uživatelé přiřazeni ke třídě malých prostředků, která uděluje 100 MB paměti na distribuci.  Protože vždy existuje 60 distribucí a každé distribuci je poskytnuto minimálně 100 MB paměti, velikost celkově přidělené paměti v systému je 6 000 MB, tedy téměř 6 GB.  

Pro určité dotazy, například velká spojení nebo nahrávání do clusterovaných tabulek columnstore, bude větší přidělení paměti přínosem.  U některých dotazů, jako jsou třeba pouhá prohledávání, se výhody neprojeví.  Na druhou stranu s využitím větší třídy prostředků snižuje souběžnost, takže budete chtít vzít tento vliv v úvahu před přesunutím všech uživatelů do velké třídy prostředků.

Viz také [Třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Použijte menší třídu prostředků pro zlepšení souběžnosti
Pokud jste si všiml, že dotazy uživatelů se zdají mít dlouhé zpoždění, může být, že vaši uživatelé jsou spuštěny ve větších tříd ách prostředků a spotřebovávají mnoho souběžnost sloty způsobuje další dotazy do fronty.  Pokud chcete zjistit, jestli jsou požadavky uživatelů ve frontě, spusťte příkaz `SELECT * FROM sys.dm_pdw_waits` a zkontrolujte, jestli se vrátí nějaké řádky.

Viz také [Třídy prostředků pro správu úloh](resource-classes-for-workload-management.md), [sys.dm_pdw_waits][sys.dm_pdw_waits].

## <a name="other-resources"></a>Další prostředky
Přečtěte si také článek [Řešení potíží][Troubleshooting], ve kterém najdete běžné problémy a jejich řešení.

Pokud jste v tomto článku nenašli to, co hledáte, zkuste pomocí "Hledat dokumenty" na levé straně této stránky a prohledat všechny dokumenty Azure Synapse.  [Fórum Azure Synapse][Azure SQL Data Warehouse MSDN Forum] je místo, kde můžete klást otázky ostatním uživatelům a skupině produktů Azure Synapse. 

Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  Pokud dáváte přednost klást otázky na přetečení zásobníku, máme také [Azure Synapse Stack Overflow Forum][Azure SQL Data Warehouse Stack Overflow Forum].

Nakonec prosím použijte stránku [Azure Synapse Feedback][Azure SQL Data Warehouse Feedback] k požadavkům na funkce.  Přidáním vlastních žádostí nebo hlasováním pro ostatní žádosti nám pomůžete určit prioritu funkcí.

<!--Image references-->

<!--Article references-->
[Create a support ticket]:sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]:sql-data-warehouse-develop-ctas.md
[Table overview]:sql-data-warehouse-tables-overview.md
[Table data types]:sql-data-warehouse-tables-data-types.md
[Table distribution]:sql-data-warehouse-tables-distribute.md
[Table indexes]:sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]:sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]:sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]:sql-data-warehouse-tables-partition.md
[Manage table statistics]:sql-data-warehouse-tables-statistics.md
[Temporary tables]:sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]:guidance-for-loading-data.md
[Load data]:design-elt-data-loading.md
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]:load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]:sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]:sql-data-warehouse-develop-transactions.md
[Optimizing transactions]:sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]:sql-data-warehouse-troubleshoot.md
[LABEL]:sql-data-warehouse-develop-label.md

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
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
