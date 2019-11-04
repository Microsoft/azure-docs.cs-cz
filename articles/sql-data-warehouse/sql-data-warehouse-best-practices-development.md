---
title: Osvědčené postupy vývoje pro Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení a osvědčené postupy, které byste měli znát, když budete vyvíjet řešení pro službu Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2debe9000cb60d4dea7395181dfb783fe26586f1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499846"
---
# <a name="development-best-practices-for-azure-sql-data-warehouse"></a>Osvědčené postupy vývoje pro Azure SQL Data Warehouse
Tento článek popisuje doprovodné materiály a osvědčené postupy při vývoji řešení datového skladu. 

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ladění výkonu dotazů s využitím nových vylepšení produktu  
- [Ladění výkonu pomocí materializovaná zobrazení](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Ladění výkonu pomocí seřazeného clusterovaného indexu columnstore](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Ladění výkonu s využitím mezipaměti sady výsledků](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování
Další informace o snižování nákladů prostřednictvím pozastavování a škálování najdete v tématu [Správa výpočetních služeb](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Udržujte statistiky
Azure SQL Data Warehouse lze nakonfigurovat tak, aby automaticky zjišťoval a vytvořil statistiku pro sloupce.  Plány dotazů vytvořené optimalizátorem jsou dostupné jenom jako dostupné statistiky.  Doporučujeme, abyste pro své databáze povolili AUTO_CREATE_STATISTICS a zachovali aktualizované údaje denně nebo po každém zatížení, abyste zajistili, že statistiky pro sloupce používané ve vašich dotazech jsou vždycky aktuální. 

Pokud zjistíte, že aktualizace všech statistik trvá příliš dlouho, můžete se pokusit o to, které sloupce potřebují časté aktualizace statistiky. Například můžete chtít denně aktualizovat sloupce s datem, do kterých se mohou přidávat nové hodnoty. **Nejvíc výhod získáte tak, že budete mít aktualizované statistiky pro sloupce, které jsou součástí spojení, sloupce používané v klauzuli WHERE a sloupce nalezené v GROUP BY.**

Viz také [Správa statistik tabulek][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS].

## <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash
Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.  Tento návrh usnadňuje uživatelům začít vytvářet tabulky, aniž by se museli rozhodovat, jak mají být jejich tabulky distribuovány.  Výkon tabulek kruhového dotazování může být pro některé úlohy dostatečný, ale ve většině případů bude lépe fungovat výběr distribučního sloupce.  Nejběžnějším příkladem, kdy tabulka distribuovaná podle sloupce zdaleka překoná tabulku kruhového dotazování, je spojení dvou velkých tabulek faktů.  Například pokud máte tabulku objednávek, která se distribuuje podle sloupce id_objednavky a tabulku transakcí, která se také distribuuje podle sloupce id_objednavky, a provedete spojení tabulky objednávek s tabulkou transakcí podle sloupce id_objednavky, z dotazu se stane předávací dotaz, to znamená, že eliminujeme operace přesunu dat.  Méně kroků znamená rychlejší dotaz.  Méně přesunů dat také přispívá ke zrychlení dotazů.  Toto vysvětlení pouze poškrábaný plochu. Při načítání distribuované tabulky se ujistěte, že příchozí data nejsou seřazená podle distribučního klíče, zpomalilo by to načítání.  V následujících odkazech najdete další informace o tom, jak výběr distribučního sloupce může zlepšit výkon, a jak definovat distribuovanou tabulku v klauzuli WITH příkazu CREATE TABLES.

Viz také [Přehled tabulek][Table overview], [Distribuce tabulky][Table distribution], [Výběr distribuce tabulky][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů
I když je možné rozdělit data na oddíly při údržbě dat prostřednictvím přepínání oddílů nebo optimalizací kontrol pomocí eliminace oddílu, může vaše dotazy zpomalit příliš mnoho oddílů.  Často se jedná o strategii vysoké členitosti, která může fungovat dobře na SQL Server nemusí fungovat dobře na SQL Data Warehouse.  Pokud máte příliš mnoho oddílů, může se také snížit efektivita clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 milion řádků.  Mějte na paměti, že služba SQL Data Warehouse za vás na pozadí dělí data do 60 databází, takže pokud vytvoříte tabulku se 100 oddíly, výsledkem bude ve skutečnosti 6000 oddílů.  Každá úloha je jiná, takže nejlepší rada je, abyste s dělením experimentovali a zjistili, co je pro vaši úlohu nejvhodnější.  Zvažte použití nižší členitosti, než jaká by pro vás byla vhodná v systému SQL Server.  Například místo denního dělení zvažte použití týdenního nebo měsíčního dělení.

Viz také [Dělení tabulky][Table partitioning].

## <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí
Příkazy INSERT, UPDATE a DELETE se spouštějí v rámci transakce, a když selžou, musí se transakce odvolat.  Abyste minimalizovali potenciální dlouhé odvolávání, minimalizujte velikost transakcí kdykoli je to možné.  Můžete to provést rozdělením příkazů INSERT, UPDATE a DELETE na části.  Například pokud máte vložení, které očekáváte jednu hodinu, pokud je to možné, rozdělte vložení na čtyři části, které se spustí za 15 minut.  Abyste snížili riziko odvolání transakce, využijte příkazy s minimálním protokolováním, například CTAS, TRUNCATE, DROP TABLE nebo INSERT k vyprázdnění tabulek.  Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  Například místo provedení příkazu DELETE pro odstranění všech řádků v tabulce, kde datum_objednavky bylo Říjen 2001, můžete data dělit měsíčně a pak vyměnit oddíl s daty za prázdný oddíl z jiné tabulky (viz příklady příkazu ALTER TABLE).  U tabulek bez oddílů zvažte místo příkazu DELETE použití příkazu CTAS k zápisu dat, která chcete v tabulce uchovat.  Pokud CTAS zabere stejné množství času, je mnohem bezpečnější operace, protože má minimální transakční protokolování a v případě potřeby je můžete kdykoli zrušit.

Viz také [Vysvětlení transakcí][Understanding transactions], [Optimalizace transakcí][Optimizing transactions], [Dělení tabulky][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [CREATE TABLE AS SELECT (CTAS)][Create table as select (CTAS)].

## <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce
Při definování knihovny DDL s použitím nejmenšího datového typu, který bude podporovat vaše data, dojde k vylepšení výkonu dotazů.  To je obzvlášť důležité pro sloupce typu CHAR a VARCHAR.  Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho sloupce definujte jako VARCHAR, pokud tento typ splňuje všechny požadavky, místo používání NVARCHAR.

Viz také [Přehled tabulek][Table overview], [Typy tabulkových dat][Table data types], [CREATE TABLE][CREATE TABLE].

## <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore
Clusterované indexy columnstore jsou jedním z nejefektivnějších způsobů ukládání dat ve službě SQL Data Warehouse.  Vy výchozím nastavení se tabulky ve službě SQL Data Warehouse vytváří jako clusterované columnstore.  Pokud chcete dosáhnout co nejlepšího výkonu dotazů na tabulky columnstore, je důležité mít kvalitní segmenty.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Kvalitu segmentů lze změřit podle počtu řádků v komprimované skupině řádků.  V části [Příčiny nekvalitních indexů columnstore][Causes of poor columnstore index quality] v článku [Indexy tabulky][Table indexes] najdete podrobné pokyny k zjišťování a zlepšování kvality segmentů clusterovaných tabulek columnstore.  Vzhledem k tomu, že jsou důležité segmenty columnstore s vysokou kvalitou, je vhodné použít ID uživatelů, která jsou ve střední nebo velké třídě prostředků pro načítání dat. Použití nižších [jednotek datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) znamená, že chcete uživateli, který načítá, přiřadit větší třídu prostředků.

Protože tabulky columnstore zpravidla nebudou vkládat data do komprimovaného segmentu columnstore, dokud v něm nebude více než 1 milion řádků na tabulku, a každá tabulka služby SQL Data Warehouse je rozdělená na 60 tabulek, obecně platí, že tabulky columnstore nepřináší dotazům žádné výhody, pokud tabulka nemá alespoň 60 milionů řádků.  Pro tabulky s méně než 60 miliony řádků může být použití indexu columnstore zbytečné.  Ale také to nemusí vadit.  Kromě toho, pokud svá data dělíte, pamatujte na to, že každý oddíl musí mít alespoň 1 milion řádků, abyste využili výhod clusterovaného indexu columnstore.  Pokud má tabulka 100 oddílů, bude muset mít alespoň 6 miliard řádků, abyste využili výhod clusterovaného úložiště sloupců (60 distribucí × 100 oddílů × 1 milion řádků).  Pokud vaše tabulka v tomto příkladu neobsahuje 6 miliard řádků, buď snižte počet oddílů, nebo místo ní zvažte použití tabulky haldy.  Můžete také experimentovat, abyste zjistili, jestli pomocí tabulky haldy se sekundárními indexy dosáhnete lepšího výkonu než s tabulkou columnstore.

Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  

Viz také [Indexy tabulky][Table indexes], [Průvodce indexy columnstore][Columnstore indexes guide], [Obnovení indexů columnstore][Rebuilding columnstore indexes].

## <a name="next-steps"></a>Další kroky
Pokud v tomto článku nenajdete, co hledáte, zkuste vyhledat ve všech Azure SQL Data Warehouse dokumentech pomocí hledání dokumentů na levé straně této stránky.  [Fórum Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN Forum] je místo, kde můžete klást otázky ostatním uživatelům a do SQL Data Warehouse skupiny produktů.  Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  Pokud dáváte přednost pokládání otázek na Stack Overflow, máme také [Fórum pro Azure SQL Data Warehouse na Stack Overflow][Azure SQL Data Warehouse Stack Overflow Forum].

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
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
