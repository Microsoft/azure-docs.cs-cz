---
title: Osvědčené postupy vývoje
description: Doporučení a osvědčené postupy, které byste měli znát při vývoji řešení pro fond Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9c4f08b143ab4a0d3e780f68f8d5ab823d4eae12
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745363"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Doporučené postupy vývoje pro fond SYNAPse SQL

Tento článek popisuje pokyny a osvědčené postupy při vývoji řešení fondu SQL.

## <a name="tune-query-performance-with-new-product-enhancements"></a>Vyladění výkonu dotazů pomocí nových vylepšení produktů

- [Ladění výkonu s využitím materializovaných zobrazení](performance-tuning-materialized-views.md)
- [Ladění výkonu s využitím uspořádaného clusterovaného indexu columnstore](performance-tuning-ordered-cci.md)
- [Ladění výkonu s využitím ukládání sad výsledků do mezipaměti](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování

Další informace o snížení nákladů prostřednictvím pozastavení a škálování najdete v článku [Správa výpočetních prostředků.](sql-data-warehouse-manage-compute-overview.md)

## <a name="maintain-statistics"></a>Udržujte statistiky

Fond SQL lze nakonfigurovat tak, aby automaticky zjišťoval a vytvářel statistiky sloupců.  Plány dotazů vytvořené optimalizátorem jsou pouze tak dobré jako dostupné statistiky.  

Doporučujeme povolit AUTO_CREATE_STATISTICS pro databáze a udržovat statistiky denně nebo po každém zatížení, abyste zajistili, že statistiky sloupců použitých ve vašich dotazech jsou vždy aktuální.

Pokud zjistíte, že aktualizace všech statistik trvá příliš dlouho, můžete se pokusit být selektivnější ohledně toho, které sloupce vyžadují časté aktualizace statistik. Například můžete chtít denně aktualizovat sloupce s datem, do kterých se mohou přidávat nové hodnoty.

> [!TIP]
> Největší užitek získáte tím, že budete mít aktualizované statistiky o sloupcích zapojených do spojení, sloupcích použitých v klauzuli WHERE a sloupcích nalezených v souborech GROUP BY.

Viz také [Správa statistik tabulky](sql-data-warehouse-tables-statistics.md), VYTVOŘENÍ [STATISTIKY](sql-data-warehouse-tables-statistics.md)a [AKTUALIZACE STATISTIKY](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash

Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.  Tento návrh usnadňuje uživatelům začít vytvářet tabulky, aniž by se museli rozhodnout, jak mají být jejich tabulky distribuovány.  

Výkon tabulek kruhového dotazování může být pro některé úlohy dostatečný, ale ve většině případů bude lépe fungovat výběr distribučního sloupce.  Nejběžnějším příkladem, kdy tabulka distribuovaná podle sloupce zdaleka překoná tabulku kruhového dotazování, je spojení dvou velkých tabulek faktů.  

Například pokud máte tabulku objednávek, která se distribuuje podle sloupce id_objednavky a tabulku transakcí, která se také distribuuje podle sloupce id_objednavky, a provedete spojení tabulky objednávek s tabulkou transakcí podle sloupce id_objednavky, z dotazu se stane předávací dotaz, to znamená, že eliminujeme operace přesunu dat.  Méně kroků znamená rychlejší dotaz.  Méně přesunů dat také přispívá ke zrychlení dotazů.  

Při načítání distribuované tabulky se ujistěte, že příchozí data nejsou seřazená podle distribučního klíče, zpomalilo by to načítání.  Články, které následují poskytnout další podrobnosti o zlepšení výkonu výběrem sloupce distribuce a jak definovat distribuované tabulky v WITH klauzule příkazu CREATE TABLES.

Viz také [Přehled tabulky](sql-data-warehouse-tables-overview.md), [Rozložení tabulky](sql-data-warehouse-tables-distribute.md), Výběr [rozložení tabulky](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), VYTVOŘIT [TABULKU](sql-data-warehouse-tables-overview.md)a VYTVOŘIT TABULKU [JAKO VÝBĚR](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů

Zatímco rozdělení dat může být efektivní pro údržbu dat prostřednictvím přepínání oddílů nebo optimalizace skenování pomocí eliminace oddílu, s příliš mnoho oddílů může zpomalit vaše dotazy.  

Často vysoké rozlišovací schopnost dělení strategie, která může fungovat dobře na SQL Server nemusí fungovat dobře ve fondu SQL.  Pokud máte příliš mnoho oddílů, může se také snížit efektivita clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 milion řádků.  

Mějte na paměti, že na pozadí fondu SQL oddíly data pro vás do 60 databází, takže pokud vytvoříte tabulku s 100 oddíly, to ve skutečnosti vede k 6000 oddílů.  Každá úloha je jiná, takže nejlepší rada je, abyste s dělením experimentovali a zjistili, co je pro vaši úlohu nejvhodnější.  

> [!TIP]
> Zvažte použití nižší rozlišovací schopnost, než co může mít pracoval pro vás v SQL Server.  Například místo denního dělení zvažte použití týdenního nebo měsíčního dělení.

Viz také [dělení tabulky](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí

Příkazy INSERT, UPDATE a DELETE se spouštějí v rámci transakce, a když selžou, musí se transakce odvolat.  Abyste minimalizovali potenciální dlouhé odvolávání, minimalizujte velikost transakcí kdykoli je to možné.  Můžete to provést rozdělením příkazů INSERT, UPDATE a DELETE na části.  

Například pokud máte INSERT, které očekáváte, že trvat 1 hodinu, pokud je to možné, rozdělit INSERT do čtyř částí, které budou každá spuštěna za 15 minut.  Využijte speciální případy minimální protokolování, jako je CTAS, TRUNCATE, DROP TABLE nebo INSERT do prázdných tabulek, abyste snížili riziko vrácení zpět.  

Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  Například místo provedení příkazu DELETE pro odstranění všech řádků v tabulce, kde datum_objednavky bylo Říjen 2001, můžete data dělit měsíčně a pak vyměnit oddíl s daty za prázdný oddíl z jiné tabulky (viz příklady příkazu ALTER TABLE).  

Pro tabulky bez oddílů zvažte použití CTAS k zápisu dat, která chcete zachovat v tabulce, nikoli pomocí příkazu DELETE.  Pokud CTAS trvá stejné množství času, je mnohem bezpečnější operace spustit, protože má minimální protokolování transakcí a může být zrušena rychle v případě potřeby.

Viz také [principy transakcí](sql-data-warehouse-develop-transactions.md), [optimalizace transakcí](sql-data-warehouse-develop-best-practices-transactions.md), [dělení tabulek](sql-data-warehouse-tables-partition.md), [zkrácená tabulka](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), ZMĚNA [TABULKY](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)a Vytvoření tabulky [jako výběru (CTAS).](sql-data-warehouse-develop-ctas.md)

## <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce

Při definování DDL, pomocí nejmenší datový typ, který bude podporovat vaše data zlepší výkon dotazu.  Tento přístup je zvláště důležité pro char a VARCHAR sloupce.  

Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho sloupce definujte jako VARCHAR, pokud tento typ splňuje všechny požadavky, místo používání NVARCHAR.

Viz také [Přehled tabulky](sql-data-warehouse-tables-overview.md), Datové [typy tabulek](sql-data-warehouse-tables-data-types.md)a [VYTVOŘIT TABULKU](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore

Clustered columnstore indexy jsou jedním z nejúčinnějších způsobů, jak můžete ukládat data ve fondu SQL.  Ve výchozím nastavení jsou tabulky ve fondu SQL vytvořeny jako clusterované columnstore.  

> [!NOTE]
> Chcete-li dosáhnout optimálního výkonu pro dotazy na tabulky columnstore, s dobrou kvalitu segmentu je důležité.  

Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Kvalitu segmentů lze změřit podle počtu řádků v komprimované skupině řádků.  

Podrobné pokyny ke zjišťování a zlepšování kvality segmentů pro clusterované tabulky columnstore naleznete v článku [Příčiny špatné kvality indexu columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) v tabulce [indexů.](sql-data-warehouse-tables-index.md)  

Vzhledem k tomu, že vysoce kvalitní segmenty columnstore jsou důležité, je vhodné použít ID uživatelů, které jsou ve střední nebo velké třídy prostředků pro načítání dat. Použití [nižšíjednotky datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) znamená, že chcete přiřadit větší třídu prostředků pro načítacího uživatele.

Vzhledem k tomu, columnstore tabulky obecně nebude nabízená data do segmentu komprimované columnstore, dokud existuje více než 1 milion řádků na tabulku a každý fond SQL tabulka je rozdělena do 60 tabulek, obecně, columnstore tabulky nebudou mít prospěch dotazu, pokud tabulka má více než 60 milionů řádků.  

Pro tabulku s méně než 60 milionů řádků nemusí mít žádný smysl mít index columnstore.  Ale také to nemusí vadit.  

Kromě toho, pokud svá data dělíte, pamatujte na to, že každý oddíl musí mít alespoň 1 milion řádků, abyste využili výhod clusterovaného indexu columnstore.  Pokud tabulka obsahuje 100 oddílů, bude muset mít alespoň 6 miliard řádků, aby mohla těžit z úložiště seskupených sloupců (60 distribucí *100 oddílů* 1 milion řádků).  

Pokud vaše tabulka v tomto příkladu neobsahuje 6 miliard řádků, buď snižte počet oddílů, nebo místo ní zvažte použití tabulky haldy.  Můžete také experimentovat, abyste zjistili, jestli pomocí tabulky haldy se sekundárními indexy dosáhnete lepšího výkonu než s tabulkou columnstore.

> [!TIP]
> Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  

Viz také [Tabulka indexy](sql-data-warehouse-tables-index.md), [Columnstore indexy průvodce](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)a [znovuvytváření columnstore indexy](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Další kroky

Pokud v tomto článku nenajdete to, co hledáte, zkuste pomocí "Hledat dokumenty" na levé straně této stránky a prohledat všechny dokumenty Azure Synapse.  

[Fórum Azure Synapse](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) je místo, kde můžete psát dotazy ostatním uživatelům a skupině produktů Azure Synapse.  Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  

Pokud dáváte přednost pokládání otázek na Stack Overflow, máme také [Fórum pro Azure SQL Data Warehouse na Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).

Pomocí stránky [Azure Synapse Feedback](https://feedback.azure.com/forums/307516-sql-data-warehouse) můžete provádět požadavky na funkce.  Přidáním vlastních žádostí nebo hlasováním pro ostatní žádosti nám pomůžete určit prioritu funkcí.
