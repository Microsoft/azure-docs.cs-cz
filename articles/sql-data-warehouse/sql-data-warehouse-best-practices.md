---
title: Osvědčené postupy pro SQL Analytics ve službě Azure synapse Analytics (dřív SQL DW) | Microsoft Docs
description: Doporučení a osvědčené postupy pro vývoj řešení pro SQL Analytics ve službě Azure synapse Analytics (dřív SQL DW).
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: bafa4088ee413e28ec6adee3df20f253aeebb861
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499837"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Osvědčené postupy pro SQL Analytics ve službě Azure synapse Analytics (dřív SQL DW)

Tento článek je kolekcí osvědčených postupů, které vám pomůžou dosáhnout optimálního výkonu při nasazení [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) .  Účelem tohoto článku je poskytnout vám základní pokyny a zvýraznit důležité oblasti, které se zaměřují.  V každé části se seznámíte s konceptem a potom odkazujete na podrobnější články, které se vztahují k konceptu podrobněji. Pořadí témat je v pořadí podle důležitosti. 

## <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování

Další informace o snižování nákladů prostřednictvím pozastavování a škálování najdete v tématu [Správa výpočetních služeb](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Udržujte statistiky
Azure SQL Data Warehouse lze nakonfigurovat tak, aby automaticky zjišťoval a vytvořil statistiku pro sloupce.  Plány dotazů vytvořené optimalizátorem jsou dostupné jenom jako dostupné statistiky.  Doporučujeme, abyste pro své databáze povolili AUTO_CREATE_STATISTICS a zachovali aktualizované údaje denně nebo po každém zatížení, abyste zajistili, že statistiky pro sloupce používané ve vašich dotazech jsou vždycky aktuální. 

Pokud zjistíte, že aktualizace všech statistik trvá příliš dlouho, můžete se pokusit o to, které sloupce potřebují časté aktualizace statistiky. Například můžete chtít denně aktualizovat sloupce s datem, do kterých se mohou přidávat nové hodnoty. **Nejvíc výhod získáte tak, že budete mít aktualizované statistiky pro sloupce, které jsou součástí spojení, sloupce používané v klauzuli WHERE a sloupce nalezené v GROUP BY.**

Viz také [Správa statistik tabulek][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS].

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Použijte zobrazení dynamických zpráv k monitorování a optimalizaci dotazů
SQL Analytics má několik zobrazení dynamické správyů, které se dají použít k monitorování provádění dotazů.  Níže uvedený článek o monitorování vás provede podrobnými pokyny k tomu, jak se dívat na podrobnosti prováděného dotazu.  S rychlým vyhledáváním dotazů v těchto zobrazeních dynamických zpráv může pomoci použití možnosti LABEL v dotazech.

Viz také [Monitorování úloh pomocí zobrazení dynamických zpráv][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits].

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ladění výkonu dotazů s využitím nových vylepšení produktu
- [Ladění výkonu pomocí materializovaná zobrazení](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Ladění výkonu pomocí seřazeného clusterovaného indexu columnstore](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Ladění výkonu s využitím mezipaměti sady výsledků](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>Seskupujte příkazy INSERT do dávek
Pro jednorázové nahrávání do malé tabulky příkazem INSERT nebo dokonce i opakované načítání vyhledávání si můžete vystačit s příkazem jako třeba `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Pokud však v průběhu dne potřebujete načíst tisíce nebo miliony řádků, můžete zjistit, že vám příkazy INSERT s jedním prvkem jednoduše nestačí.  Místo toho vyvíjejte své procesy tak, aby zapisovaly do souboru, který bude pravidelně kontrolovat a načítat další proces.

Viz také [INSERT][INSERT].

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Použijte PolyBase k rychlému načítání a exportu dat

 SQL Analytics podporuje načítání a export dat prostřednictvím několika nástrojů, včetně Azure Data Factory, základů a BCP.  Pro malá množství dat, kde není výkon tak důležitý, by vám měl stačit libovolný nástroj.  Když však načítáte nebo exportujete velká množství dat nebo vyžadujete vysoký výkon, nejlepší volbou je PolyBase.  
 
 Základna je navržená tak, aby využila architekturu MPP (hromadně paralelní zpracování) a načetla a využívala velikost dat rychleji než jakýkoli jiný nástroj.  Úlohy funkce PolyBase můžete spustit pomocí příkazů CTAS nebo INSERT INTO.  **Použití příkazu CTAS minimalizuje protokolování transakce a je to nejrychlejší způsob, jak načíst vaše data.** 
 
  Azure Data Factory podporuje také základní zátěže a může dosáhnout podobného výkonu jako CTAS.  PolyBase podporuje řadu formátů souborů, včetně souborů GZip.  **Chcete-li maximalizovat propustnost při použití textových souborů gzip, rozdělte soubory do 60 nebo více souborů, čímž maximalizujete paralelismus zátěže.**  Pro rychlejší celkovou propustnost zvažte souběžné načítání dat.

Viz také [načtení dat][Load data], [Průvodce používáním základny][Guide for using PolyBase], [vzorů a strategií načítání fondů SQL][Azure SQL Data Warehouse loading patterns and strategies], [načítání dat pomocí Azure Data Factory][Load Data with Azure Data Factory], [přesun dat pomocí Azure Data Factory][Move data with Azure Data Factory], [Vytvoření formátu externího souboru][CREATE EXTERNAL FILE FORMAT], [Vytvoření tabulka jako Select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Načtěte a následně dotazujte externí tabulky
Přestože lze PolyBase (také označovanou jako externí tabulky) považovat za nejrychlejší způsob načítání dat, není ideální pro dotazy. Základní tabulky v současné době podporují pouze soubory objektů BLOB v Azure a Azure Data Lake úložiště. Tyto soubory nemají podporu v žádných výpočetních prostředcích.  

V důsledku toho služba SQL Analytics nemůže přesměrovat tuto práci, a proto musí číst celý soubor tak, že ho načte do databáze tempdb, aby bylo možné data číst.  Proto pokud máte několik dotazů, které budou tato data dotazovat, je lepší data jednou nahrát a nastavit dotazy, aby používaly místní tabulku.

Viz také [Průvodce používáním funkce PolyBase][Guide for using PolyBase].

## <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash
Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.  To umožňuje uživatelům jednoduše začít vyvářet tabulky, aniž by se museli rozhodovat, jakým způsobem se mají jejich tabulky distribuovat.  Výkon tabulek kruhového dotazování může být pro některé úlohy dostatečný, ale ve většině případů bude lépe fungovat výběr distribučního sloupce.  Nejběžnějším příkladem, kdy tabulka distribuovaná podle sloupce zdaleka překoná tabulku kruhového dotazování, je spojení dvou velkých tabulek faktů.  

Například pokud máte tabulku objednávek, která se distribuuje podle sloupce id_objednavky a tabulku transakcí, která se také distribuuje podle sloupce id_objednavky, a provedete spojení tabulky objednávek s tabulkou transakcí podle sloupce id_objednavky, z dotazu se stane předávací dotaz, to znamená, že eliminujeme operace přesunu dat.  Méně kroků znamená rychlejší dotaz.  Méně přesunů dat také přispívá ke zrychlení dotazů.  

Při načítání distribuované tabulky se ujistěte, že příchozí data nejsou seřazená podle distribučního klíče, zpomalilo by to načítání.  Další informace o tom, jak vybrat distribuční sloupec, může zvýšit výkon a jak definovat distribuovanou tabulku v klauzuli WITH příkazu CREATE TABLE, najdete na následujících odkazech.

Viz také [Přehled tabulek][Table overview], [Distribuce tabulky][Table distribution], [Výběr distribuce tabulky][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů
I když je možné rozdělit data na oddíly při údržbě dat prostřednictvím přepínání oddílů nebo optimalizací kontrol pomocí eliminace oddílu, může vaše dotazy zpomalit příliš mnoho oddílů.  Často se strategie dělení vysoké členitosti, která může fungovat dobře, SQL Server nemusí dobře fungovat v SQL Analytics.  

Pokud máte příliš mnoho oddílů, může se také snížit efektivita clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 milion řádků.  Mějte na paměti, že SQL Analytics na pozadí rozdělí vaše data do databází 60, takže pokud vytvoříte tabulku s 100 oddíly, výsledkem je to, že se 6000 ve skutečnosti budou zobrazovat oddíly.  

Každá úloha je jiná, takže nejlepší rada je, abyste s dělením experimentovali a zjistili, co je pro vaši úlohu nejvhodnější.  Zvažte použití nižší členitosti, než jaká by pro vás byla vhodná v systému SQL Server.  Například místo denního dělení zvažte použití týdenního nebo měsíčního dělení.

Viz také [Dělení tabulky][Table partitioning].

## <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí
Příkazy INSERT, UPDATE a DELETE se spouštějí v rámci transakce, a když selžou, musí se transakce odvolat.  Abyste minimalizovali potenciální dlouhé odvolávání, minimalizujte velikost transakcí kdykoli je to možné.  Můžete to provést rozdělením příkazů INSERT, UPDATE a DELETE na části.  

Například pokud máte vložení, kterou očekáváte 1 hodinu, pokud je to možné, rozdělte vložení na čtyři části, které se spustí za 15 minut.  Abyste snížili riziko odvolání transakce, využijte příkazy s minimálním protokolováním, například CTAS, TRUNCATE, DROP TABLE nebo INSERT k vyprázdnění tabulek.  

Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  Například místo provedení příkazu DELETE pro odstranění všech řádků v tabulce, kde datum_objednavky bylo Říjen 2001, můžete data dělit měsíčně a pak vyměnit oddíl s daty za prázdný oddíl z jiné tabulky (viz příklady příkazu ALTER TABLE).  

Pro nerozdělené tabulky zvažte použití CTAS k zápisu dat, která chcete uchovat v tabulce, a nepoužívejte DELETE.  Pokud CTAS zabere stejné množství času, je mnohem bezpečnější operace, protože má minimální transakční protokolování a v případě potřeby je můžete kdykoli zrušit.

Viz také [Vysvětlení transakcí][Understanding transactions], [Optimalizace transakcí][Optimizing transactions], [Dělení tabulky][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [CREATE TABLE AS SELECT (CTAS)][Create table as select (CTAS)].

## <a name="reduce-query-result-sizes"></a>Zmenšení velikosti výsledků dotazu  
Tento krok vám pomůže vyhnout se problémům na straně klienta způsobeným velkým výsledkem dotazů.  Dotaz můžete upravit tak, aby se snížil počet vrácených řádků. Některé nástroje pro generování dotazů umožňují přidat k jednotlivým dotazům syntaxi "TOP N".  Výsledek dotazu můžete také CETAS na dočasnou tabulku a pak použít základní export pro zpracování nižší úrovně.

## <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce
Při definování knihovny DDL s použitím nejmenšího datového typu, který bude podporovat vaše data, dojde k vylepšení výkonu dotazů.  To je obzvlášť důležité pro sloupce typu CHAR a VARCHAR.  Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho sloupce definujte jako VARCHAR, pokud tento typ splňuje všechny požadavky, místo používání NVARCHAR.

Viz také [Přehled tabulek][Table overview], [Typy tabulkových dat][Table data types], [CREATE TABLE][CREATE TABLE].

## <a name="use-temporary-heap-tables-for-transient-data"></a>Použijte dočasné tabulky hald pro přechodná data
Při dočasném vytváření dat můžete zjistit, že použití tabulky haldy způsobí rychlejší zpracování celého procesu.  Pokud nahráváte data pouze za účelem jejich přípravy před spuštěním dalších transformací, nahrání tabulky do tabulky haldy bude mnohem rychlejší, než nahrání dat do clusterované tabulky columnstore.  

Nahrání dat do dočasné tabulky bude dokonce mnohem rychlejší, než nahrání tabulky do trvalého úložiště.  Dočasné tabulky začínají znakem "#" a jsou přístupné pouze v relaci, která ji vytvořila, takže mohou fungovat pouze v omezených scénářích.   Tabulky hald se definují v klauzuli WITH příkazu CREATE TABLE.  Pokud používáte dočasnou tabulku, nezapomeňte nad ní také vytvořit statistiky.

Viz také [Dočasné tabulky][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore
Clusterované indexy columnstore jsou jedním z nejúčinnějších způsobů, jak můžete ukládat data v SQL Analytics.  Ve výchozím nastavení jsou tabulky v SQL Analytics vytvořené jako clusterované ColumnStore.  Pokud chcete dosáhnout co nejlepšího výkonu dotazů na tabulky columnstore, je důležité mít kvalitní segmenty.  

Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Kvalitu segmentů lze změřit podle počtu řádků v komprimované skupině řádků.  V části [Příčiny nekvalitních indexů columnstore][Causes of poor columnstore index quality] v článku [Indexy tabulky][Table indexes] najdete podrobné pokyny k zjišťování a zlepšování kvality segmentů clusterovaných tabulek columnstore.  

Vzhledem k tomu, že jsou důležité segmenty columnstore s vysokou kvalitou, je vhodné použít ID uživatelů, která jsou ve střední nebo velké třídě prostředků pro načítání dat. Použití nižších [jednotek datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) znamená, že chcete uživateli, který načítá, přiřadit větší třídu prostředků.

Vzhledem k tomu, že tabulky columnstore obecně neobsahují data do komprimovaného segmentu columnstore, dokud nebude existovat více než 1 000 000 řádků na tabulku a každá tabulka SQL Analytics je rozdělena do 60 tabulek, jako pravidlo povýšení nemůžou tabulky columnstore těžit dotaz, pokud tabulka obsahuje více než 60 000 000 řádků.  Pro tabulky s méně než 60 miliony řádků může být použití indexu columnstore zbytečné.  Ale také to nemusí vadit.  

Kromě toho, pokud svá data dělíte, pamatujte na to, že každý oddíl musí mít alespoň 1 milion řádků, abyste využili výhod clusterovaného indexu columnstore.  Pokud má tabulka 100 oddílů, bude muset mít alespoň 6 miliard řádků, abyste využili výhod clusterovaného úložiště sloupců (60 distribucí × 100 oddílů × 1 milion řádků).  

Pokud vaše tabulka v tomto příkladu neobsahuje 6 miliard řádků, buď snižte počet oddílů, nebo místo ní zvažte použití tabulky haldy.  Můžete také experimentovat, abyste zjistili, jestli pomocí tabulky haldy se sekundárními indexy dosáhnete lepšího výkonu než s tabulkou columnstore.

Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  

Viz také [Indexy tabulky][Table indexes], [Průvodce indexy columnstore][Columnstore indexes guide], [Obnovení indexů columnstore][Rebuilding columnstore indexes].

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Použijte větší třídu prostředků k vylepšení výkonu dotazu
SQL Analytics používá skupiny prostředků jako způsob přidělení paměti pro dotazy.  Mimo pole jsou všichni uživatelé přiřazeni k malé třídě prostředků, která uděluje 100 MB paměti na distribuci.  Protože vždy existuje 60 distribucí a každé distribuci je poskytnuto minimálně 100 MB paměti, velikost celkově přidělené paměti v systému je 6 000 MB, tedy téměř 6 GB.  

Pro určité dotazy, například velká spojení nebo nahrávání do clusterovaných tabulek columnstore, bude větší přidělení paměti přínosem.  U některých dotazů, jako jsou třeba pouhá prohledávání, se výhody neprojeví.  Na překlopení díky většímu počtu tříd prostředků dojde ke snížení souběžnosti, takže chcete tento dopad vzít v úvahu před tím, než všechny uživatele přesunete na velkou třídu prostředků.

Viz také [Třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Použijte menší třídu prostředků pro zlepšení souběžnosti
Pokud se všímáte, že se dotazy uživatelů zdají trvat dlouhou dobu, může to být tím, že uživatelé běží ve větších třídách prostředků a spotřebovávají mnoho slotů souběžnosti, které způsobují, že se jiné dotazy zařadí do fronty.  Pokud chcete zjistit, jestli jsou požadavky uživatelů ve frontě, spusťte příkaz `SELECT * FROM sys.dm_pdw_waits` a zkontrolujte, jestli se vrátí nějaké řádky.

Viz také [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md), [Sys. DM _pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Další zdroje informací
Přečtěte si také článek [Řešení potíží][Troubleshooting], ve kterém najdete běžné problémy a jejich řešení.

Pokud jste v tomto článku nenašli, co jste hledali, zkuste vyhledat všechny dokumenty Azure synapse pomocí hledání dokumentů na levé straně této stránky.  [Fórum Azure synapse][Azure SQL Data Warehouse MSDN Forum] je místo, kde můžete klást otázky pro ostatní uživatele a skupinu produktů Azure synapse. 

Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  Pokud dáváte přednost dotazování na Stack Overflow, máme také [fórum služby Azure Synapse Stack Overflow][Azure SQL Data Warehouse Stack Overflow Forum].

Nakonec prosím použijte stránku [Zpětná vazba Azure synapse][Azure SQL Data Warehouse Feedback] k vytváření žádostí o funkce.  Přidáním vlastních žádostí nebo hlasováním pro ostatní žádosti nám pomůžete určit prioritu funkcí.

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
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
