---
title: Doporučené postupy vývoje pro Synapse SQL
description: Doporučení a osvědčené postupy, které byste měli znát při vývoji pro Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3356817fbaef955e05358f9c92619f3c04f747d7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429613"
---
# <a name="development-best-practices-for-synapse-sql"></a>Doporučené postupy vývoje pro Synapse SQL
Tento článek popisuje pokyny a osvědčené postupy při vývoji řešení datového skladu. 

## <a name="development-best-practices-for-synapse-sql"></a>Doporučené postupy vývoje pro Synapse SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování

Další informace o snižování nákladů prostřednictvím pozastavování a škálování najdete v tématu [Správa výpočetních služeb](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Udržujte statistiky

Ujistěte se, že aktualizujete své statistiky denně nebo po každém zatížení.  Vždy existují kompromisy mezi výkonem a náklady na vytvoření a aktualizaci statistik. Pokud zjistíte, že trvá příliš dlouho udržovat všechny statistiky, být více selektivní o tom, které sloupce mají statistiky nebo které sloupce je třeba časté aktualizace.  

Můžete například aktualizovat sloupce data, kde mohou být denně přidávány nové hodnoty. **Největší užitek získáte tím, že budete mít statistiky o sloupcích zapojených do spojení, sloupcích použitých v klauzuli WHERE a sloupcích nalezených v souborech GROUP BY.**

Viz také [Správa statistik tabulky](develop-tables-statistics.md), VYTVOŘENÍ [STATISTIKY](develop-tables-statistics.md), [AKTUALIZACE STATISTIK .][UPDATE STATISTICS]

### <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash

Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.  To usnadňuje uživatelům začít vytvářet tabulky, aniž by museli rozhodnout, jak by měly být distribuovány jejich tabulky.  Kulaté robin tabulky může provádět dostatečně pro některé úlohy. Ve většině případů však výběr distribučního sloupce bude fungovat mnohem lépe.  

Nejběžnějším příkladem, kdy tabulka distribuovaná podle sloupce zdaleka překoná tabulku kruhového dotazování, je spojení dvou velkých tabulek faktů.  Máte-li například tabulku objednávek, která je distribuována order_id, a tabulku transakcí, která je distribuována také order_id, když připojíte tabulku objednávek k tabulce transakcí v order_id, stane se tento dotaz předávaným dotazem. To znamená, že eliminujeme operace pohybu dat.  Méně kroků znamená rychlejší dotaz.  Méně přesunů dat také přispívá ke zrychlení dotazů.

Při načítání distribuované tabulky se ujistěte, že příchozí data nejsou seřazená podle distribučního klíče, zpomalilo by to načítání.  Další podrobnosti o tom, jak může výběr distribučního sloupce zlepšit výkon a jak definovat distribuovanou tabulku v klauzuli WITH příkazu CREATE TABLES, naleznete v následujících odkazech.

Viz také [Přehled tabulky][Table overview], [Distribuce tabulek][Table distribution], [Výběr distribuce tabulek][Selecting table distribution], [VYTVOŘIT TABULKU][CREATE TABLE], VYTVOŘIT TABULKU [JAKO VÝBĚR][CREATE TABLE AS SELECT].

### <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů
Zatímco rozdělení dat může být efektivní pro údržbu dat prostřednictvím přepínání oddílů nebo optimalizace skenování pomocí eliminace oddílu, s příliš mnoho oddílů může zpomalit vaše dotazy.  Často vysoká rozlišovací schopnost dělení strategie, která může fungovat dobře na SQL Server nemusí fungovat dobře na fondu SQL.  

Pokud máte příliš mnoho oddílů, může se také snížit efektivita clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 milion řádků. Fond SQL rozdělí vaše data za vás do 60 databází. Pokud tedy vytvoříte tabulku se 100 oddíly, výsledkem bude 6000 oddílů.  Každá úloha je jiná, takže nejlepší rada je, abyste s dělením experimentovali a zjistili, co je pro vaši úlohu nejvhodnější.  

Jednou z možností, kterou je třeba zvážit, je použití rozlišovací schopnost, která je nižší než co může mít pracoval pro vás v SQL Server.  Například místo denního dělení zvažte použití týdenního nebo měsíčního dělení.

Viz také [dělení tabulky][Table partitioning].

### <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí

Příkazy INSERT, UPDATE a DELETE se spouštějí v rámci transakce, a když selžou, musí se transakce odvolat.  Abyste minimalizovali potenciální dlouhé odvolávání, minimalizujte velikost transakcí kdykoli je to možné.  Můžete to provést rozdělením příkazů INSERT, UPDATE a DELETE na části.  Například pokud máte INSERT, který očekáváte, že trvat 1 hodinu, můžete rozdělit INSERT do čtyř částí, čímž se zkrátí každý běh na 15 minut.

Abyste snížili riziko odvolání transakce, využijte příkazy s minimálním protokolováním, například CTAS, TRUNCATE, DROP TABLE nebo INSERT k vyprázdnění tabulek.  Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  Například místo provedení příkazu DELETE pro odstranění všech řádků v tabulce, kde datum_objednavky bylo Říjen 2001, můžete data dělit měsíčně a pak vyměnit oddíl s daty za prázdný oddíl z jiné tabulky (viz příklady příkazu ALTER TABLE).  

U tabulek bez oddílů zvažte místo příkazu DELETE použití příkazu CTAS k zápisu dat, která chcete v tabulce uchovat.  Pokud provedení příkazu CTAS trvá stejně dlouho, stále je to mnohem bezpečnější operace, protože zahrnuje minimální protokolování transakce a v případě potřeby ji lze rychle zrušit.

Viz také [principy transakcí][Understanding transactions], [optimalizace transakcí][Optimizing transactions], [dělení tabulek][Table partitioning], [Zkrácení tabulky][TRUNCATE TABLE], ALTER [TABLE][ALTER TABLE], Vytvořit tabulku jako [výběr (CTAS).][Create table as select (CTAS)]

### <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce

Při definování DDL můžete použitím nejmenšího datového typu, který podporuje vaše data, zvýšit výkon dotazu.  To je obzvlášť důležité pro sloupce typu CHAR a VARCHAR.  Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho sloupce definujte jako VARCHAR, pokud tento typ splňuje všechny požadavky, místo používání NVARCHAR.

Viz také [Přehled tabulek][Table overview], [Typy tabulkových dat](develop-tables-data-types.md), [CREATE TABLE][CREATE TABLE].

### <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore

Clustered columnstore indexy jsou jedním z nejúčinnějších způsobů, jak můžete ukládat data ve fondu SQL.  Ve výchozím nastavení jsou tabulky ve fondu SQL vytvořeny jako clusterované columnstore.  Pokud chcete dosáhnout co nejlepšího výkonu dotazů na tabulky columnstore, je důležité mít kvalitní segmenty.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  

Kvalitu segmentů lze změřit podle počtu řádků v komprimované skupině řádků.  V části [Příčiny nekvalitních indexů columnstore][Causes of poor columnstore index quality] v článku [Indexy tabulky][Table indexes] najdete podrobné pokyny k zjišťování a zlepšování kvality segmentů clusterovaných tabulek columnstore.  Vzhledem k tomu, že vysoce kvalitní segmenty columnstore jsou důležité, je vhodné použít ID uživatelů, které jsou ve střední nebo velké třídě prostředků pro načítání dat. Použití [nižšíjednotky datového skladu](azure -synapse-resource-consumption-models.md) znamená, že chcete přiřadit větší třídu prostředků pro načítacího uživatele.

Vzhledem k tomu, columnstore tabulky obecně nebude nabízená data do segmentu komprimované columnstore, dokud existuje více než 1 milion řádků na tabulku a každá tabulka fondu SQL je rozdělena do 60 tabulek, columnstore tabulky nebudou mít prospěch dotazu, pokud tabulka obsahuje více než 60 milionů řádků.  Pro tabulky s méně než 60 milionů řádků, které mají columstore index nemusí být optimální řešení.  

Kromě toho, pokud svá data dělíte, pamatujte na to, že každý oddíl musí mít alespoň 1 milion řádků, abyste využili výhod clusterovaného indexu columnstore.  Pokud tabulka obsahuje 100 oddílů, bude muset mít alespoň 6 miliard řádků, aby mohla těžit z úložiště seskupených sloupců (60 distribucí *100 oddílů* 1 milion řádků).  

Pokud vaše tabulka nemá 6 miliard řádků, buď snížit počet oddílů nebo zvážit použití haldy tabulky místo.  Také může být vhodné experimentovat, chcete-li zjistit, zda lze získat lepší výkon pomocí tabulky haldy se sekundárními indexy, nikoli columnstore tabulka.

Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  

Viz také [Tabulka indexy][Table indexes], [Columnstore indexy průvodce][Columnstore indexes guide], [Opětovné sestavení columnstore indexy][Rebuilding columnstore indexes].

### <a name="next-steps"></a>Další kroky

Pokud jste v tomto článku nenašli to, co jste hledali, zkuste pomocí "Hledat dokumenty" na levé straně této stránky a prohledat všechny dokumenty fondu Azure SQL.  [Fórum fondu SQL Azure][Azure SQL pool MSDN Forum] je místo, kde můžete klást otázky ostatním uživatelům a skupině produktů fondu SQL.  

Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  Pokud dáváte přednost klást otázky na přetečení zásobníku, máme také [Azure SQL fond zásobníku přetečení fórum][Azure SQL pool Stack Overflow Forum].

Pomocí stránky [Azure SQL pool Feedback][Azure SQL pool Feedback] můžete provádět požadavky na funkce.  Přidáním vlastních žádostí nebo hlasováním pro ostatní žádosti nám pomůžete určit prioritu funkcí.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Create table as select (CTAS)]: ../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table overview]:develop-tables-overview.md
[Table distribution]: ../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Causes of poor columnstore index quality]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Temporary tables]:develop-tables-temporary.md
[Guide for using PolyBase]:../sql-analytics/data-loading-best-practices.md
[Load data]: ../sql-data-warehouse/esign-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Monitor your workload using DMVs]: ../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Pause compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Resume compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Scale compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Understanding transactions]:develop-transactions.md
[Optimizing transactions]: ../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Troubleshooting]: ../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[LABEL]:develop-label.md

<!--MSDN references-->
[ALTER TABLE]: /sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE EXTERNAL FILE FORMAT]: /sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE STATISTICS]: /sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE AS SELECT]: /sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[DBCC PDW_SHOWEXECUTIONPLAN]: /sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[INSERT]: /sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[OPTION]: /sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[TRUNCATE TABLE]: /sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[UPDATE STATISTICS]: /sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_exec_sessions]: /sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_exec_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_request_steps]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_sql_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_dms_workers]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_waits]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[Columnstore indexes guide]: /sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL pool Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL pool MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL pool Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL pool loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/

## <a name="development-best-practices-for-sql-on-demand-preview"></a>Doporučené postupy vývoje pro SQL na vyžádání (preview)

### <a name="general-considerations"></a>Obecné aspekty

SQL na vyžádání umožňuje dotazovat se na soubory ve vašich účtech úložiště Azure. Nemá možnosti místního úložiště nebo ingestování, což znamená, že všechny soubory, které cíle dotazu jsou externí sql na vyžádání. Proto vše, co souvisí s čtení souborů z úložiště může mít vliv na výkon dotazu.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocate Azure Storage účet a SQL na vyžádání

 
Chcete-li minimalizovat latenci, můžete spoluumístit účet úložiště Azure a koncový bod SQL na vyžádání. Účty úložiště a koncové body zřízené během vytváření pracovního prostoru se nacházejí ve stejné oblasti. 
 
Pro optimální výkon, pokud máte přístup k jiným účtům úložiště s SQL na vyžádání, ujistěte se, že jsou ve stejné oblasti. V opačném případě bude zvýšena latence pro přenos sítě dat ze vzdálené oblasti do oblasti koncového bodu.

### <a name="azure-storage-throttling"></a>Omezení úložiště Azure

K vašemu účtu úložiště může přistupovat více aplikací a služeb. Když kombinované VOPS nebo propustnost generované aplikacemi, službami a sql úlohy na vyžádání překročí limity účtu úložiště, dojde k omezení úložiště. Dojde-li omezení úložiště, je podstatný negativní vliv na výkon dotazu. 
 
Po zjištění omezení sql na vyžádání má integrované zpracování tohoto scénáře. SQL na vyžádání bude vyžadovat úložiště pomalejším tempem, dokud nebude vyřešeno omezení. Pro optimální spuštění dotazu se však doporučuje, abyste během provádění dotazu nezdůrazňují účet úložiště s jinými úlohami.

### <a name="prepare-files-for-querying"></a>Příprava souborů pro dotazování

Pokud je to možné, můžete připravit soubory pro lepší výkon:

- Převést CSV na parkety - Parkety je sloupcový formát. Vzhledem k tomu, že je komprimován, má menší velikosti souborů než soubory CSV se stejnými daty a SQL na vyžádání bude potřebovat méně času a požadavků na úložiště, aby si je přečetl.
- Pokud dotaz cílí na jeden velký soubor, budete mít prospěch z jeho rozdělení na více menších souborů.
- Zkuste zachovat velikost souboru CSV pod 10 GB.
- Upřednostňuje se mít stejně velké soubory pro jednu cestu OPENROWSET nebo externí tabulku LOCATION.
- Rozdělte data uložením oddílů do různých složek nebo názvů souborů - zkontrolujte, zda [použijte funkce filename a path pro cílové oddíly](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Použití funkcí fileinfo a filepath k cílení na určité oddíly

Data jsou často uspořádána v oddílech. Můžete dát pokyn SQL na vyžádání k dotazování na určité složky a soubory. Tím se sníží počet souborů a množství dat, které dotaz potřebuje ke čtení a zpracování. V důsledku toho dosáhnete lepšího výkonu. Další informace naleznete v nabídce funkcí [název souboru](develop-storage-files-overview.md#filename-function) a [cesty k souborům](develop-storage-files-overview.md#filepath-function) a příklady [dotazování určitých souborů](query-specific-files.md).

Pokud vaše data v úložišti není rozdělena na oddíly, zvažte jejich rozdělení, abyste mohli tyto funkce použít k optimalizaci dotazů zaměřených na tyto soubory.

Při [dotazování rozdělených tabulek Spark](develop-storage-files-spark-tables.md) z SQL na vyžádání se dotaz automaticky zaměří pouze na potřebné soubory.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Použití cetas umocnění výkonu dotazů a spojení

[CETAS](develop-tables-cetas.md) je jednou z nejdůležitějších funkcí dostupných v SQL na vyžádání. CETAS je paralelní operace, která vytvoří metadata externí tabulky a exportuje výsledek dotazu SELECT do sady souborů v účtu úložiště.

Cetas můžete použít k ukládání často používané části dotazů, jako jsou spojené referenční tabulky, do nové sady souborů. Později se můžete připojit k této jedné externí tabulce namísto opakování běžných spojení ve více dotazech. Jak CETAS generuje parketové soubory, statistiky budou automaticky vytvořeny, když první dotaz cílí na tuto externí tabulku a získáte lepší výkon.
