---
title: Osvědčené postupy vývoje
description: Doporučení a osvědčené postupy, které byste měli znát při vývoji řešení pro synapse fond SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f8db19b209ddefebd4a297f18fb9b178fcf40f21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89457902"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Osvědčené postupy vývoje pro synapse fond SQL

Tento článek popisuje doprovodné materiály a osvědčené postupy při vývoji vašeho řešení fondu SQL.

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ladění výkonu dotazů s využitím nových vylepšení produktu

- [Ladění výkonu s využitím materializovaných zobrazení](performance-tuning-materialized-views.md)
- [Ladění výkonu s využitím uspořádaného clusterovaného indexu columnstore](performance-tuning-ordered-cci.md)
- [Ladění výkonu s využitím ukládání sad výsledků do mezipaměti](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování

Další informace o snížení nákladů prostřednictvím pozastavení a škálování najdete v článku věnovaném [správě výpočetních](sql-data-warehouse-manage-compute-overview.md) prostředků.

## <a name="maintain-statistics"></a>Udržujte statistiky

Fond SQL se dá nakonfigurovat tak, aby automaticky zjišťoval a vytvořil statistiku pro sloupce.  Plány dotazů vytvořené optimalizátorem jsou dostupné jenom jako dostupné statistiky.  

Doporučujeme, abyste povolili AUTO_CREATE_STATISTICS pro vaše databáze a pravidelně aktualizovali statistiku nebo po každém zatížení, abyste zajistili, že statistiky pro sloupce používané ve vašich dotazech jsou vždycky aktuální.

Pokud zjistíte, že aktualizace všech statistik trvá příliš dlouho, můžete se pokusit o to, které sloupce potřebují časté aktualizace statistiky. Například můžete chtít denně aktualizovat sloupce s datem, do kterých se mohou přidávat nové hodnoty.

> [!TIP]
> Nejvíc výhod získáte tak, že budete mít aktualizované statistiky pro sloupce, které jsou součástí spojení, sloupce používané v klauzuli WHERE a sloupce nalezené v klauzuli GROUP BY.

Viz také [Správa statistik tabulek](sql-data-warehouse-tables-statistics.md), [vytváření statistik](sql-data-warehouse-tables-statistics.md)a [aktualizace statistik](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash

Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.  Tento návrh usnadňuje uživatelům začít vytvářet tabulky, aniž by se museli rozhodovat, jak mají být jejich tabulky distribuovány.  

Výkon tabulek kruhového dotazování může být pro některé úlohy dostatečný, ale ve většině případů bude lépe fungovat výběr distribučního sloupce.  Nejběžnějším příkladem, kdy tabulka distribuovaná podle sloupce zdaleka překoná tabulku kruhového dotazování, je spojení dvou velkých tabulek faktů.  

Například pokud máte tabulku objednávek, která se distribuuje podle sloupce id_objednavky a tabulku transakcí, která se také distribuuje podle sloupce id_objednavky, a provedete spojení tabulky objednávek s tabulkou transakcí podle sloupce id_objednavky, z dotazu se stane předávací dotaz, to znamená, že eliminujeme operace přesunu dat.  Méně kroků znamená rychlejší dotaz.  Méně přesunů dat také přispívá ke zrychlení dotazů.  

Při načítání distribuované tabulky se ujistěte, že příchozí data nejsou seřazená podle distribučního klíče, zpomalilo by to načítání.  Následující články poskytují další podrobnosti o vylepšení výkonu výběrem distribučního sloupce a definování distribuované tabulky v klauzuli WITH příkazu CREATE TABLES.

Viz také [Přehled tabulek](sql-data-warehouse-tables-overview.md), [distribuce tabulky](sql-data-warehouse-tables-distribute.md), [Výběr distribuce tabulky](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [Create Table](sql-data-warehouse-tables-overview.md)a [Create Table jako SELECT](sql-data-warehouse-develop-ctas.md) .

## <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů

I když je možné rozdělit data na oddíly při údržbě dat prostřednictvím přepínání oddílů nebo optimalizací kontrol pomocí eliminace oddílu, může vaše dotazy zpomalit příliš mnoho oddílů.  

Často platí, že vysoce členitá strategie dělení, která může fungovat dobře na SQL Server, nemusí dobře fungovat na fondu SQL.  Pokud máte příliš mnoho oddílů, může se také snížit efektivita clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 milion řádků.  

Mějte na paměti, že na pozadí fond SQL rozdělí vaše data do databází 60, takže pokud vytvoříte tabulku s 100 oddíly, výsledkem je to, že se 6000 v těchto případech zobrazí oddíly.  Každá úloha je jiná, takže nejlepší rada je, abyste s dělením experimentovali a zjistili, co je pro vaši úlohu nejvhodnější.  

> [!TIP]
> Zvažte použití nižší členitosti, než jakou vám mohl v SQL Server pracovat.  Například místo denního dělení zvažte použití týdenního nebo měsíčního dělení.

Viz také [dělení tabulky](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí

Příkazy INSERT, UPDATE a DELETE se spouštějí v rámci transakce, a když selžou, musí se transakce odvolat.  Abyste minimalizovali potenciální dlouhé odvolávání, minimalizujte velikost transakcí kdykoli je to možné.  Můžete to provést rozdělením příkazů INSERT, UPDATE a DELETE na části.  

Například pokud máte vložení, které očekáváte jednu hodinu, pokud je to možné, rozdělte vložení na čtyři části, které se spustí za 15 minut.  Abyste snížili riziko vrácení zpět, využijte zvláštní případy minimálního protokolování, jako je CTAS, ZKRÁCENí, odkládací tabulka nebo vložení do prázdných tabulek.  

Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  Například místo provedení příkazu DELETE pro odstranění všech řádků v tabulce, kde datum_objednavky bylo Říjen 2001, můžete data dělit měsíčně a pak vyměnit oddíl s daty za prázdný oddíl z jiné tabulky (viz příklady příkazu ALTER TABLE).  

Pro nerozdělené tabulky zvažte použití CTAS k zápisu dat, která chcete uchovat v tabulce, a nepoužívejte DELETE.  Pokud CTAS zabere stejné množství času, je mnohem bezpečnější operace, protože má minimální transakční protokolování a v případě potřeby je můžete kdykoli zrušit.

Viz také [Principy transakcí](sql-data-warehouse-develop-transactions.md), [optimalizace transakcí](sql-data-warehouse-develop-best-practices-transactions.md), [dělení tabulky](sql-data-warehouse-tables-partition.md), [Truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)a [CREATE TABLE AS Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce

Při definování knihovny DDL s použitím nejmenšího datového typu, který bude podporovat vaše data, dojde k vylepšení výkonu dotazů.  Tento přístup je zvlášť důležitý pro sloupce typu CHAR a VARCHAR.  

Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho sloupce definujte jako VARCHAR, pokud tento typ splňuje všechny požadavky, místo používání NVARCHAR.

Viz také [Přehled tabulek](sql-data-warehouse-tables-overview.md), [typy tabulkových dat](sql-data-warehouse-tables-data-types.md)a [Create Table](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore

Clusterované indexy columnstore jsou jedním z nejúčinnějších způsobů, jak můžete ukládat data ve fondu SQL.  Ve výchozím nastavení jsou tabulky ve fondu SQL vytvořeny jako clusterované ColumnStore.  

> [!NOTE]
> Pro dosažení optimálního výkonu dotazů v tabulkách columnstore je důležité mít dobrou kvalitu segmentů.  

Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Kvalitu segmentů lze změřit podle počtu řádků v komprimované skupině řádků.  

Podrobné pokyny k detekci a zlepšení kvality segmentů clusterovaných tabulek columnstore najdete v článku o [příčinách špatné kvality indexu columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) v článku [indexy tabulky](sql-data-warehouse-tables-index.md) .  

Vzhledem k tomu, že jsou důležité segmenty columnstore s vysokou kvalitou, je vhodné použít ID uživatelů, která jsou ve střední nebo velké třídě prostředků pro načítání dat. Použití nižších [jednotek datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) znamená, že chcete uživateli, který načítá, přiřadit větší třídu prostředků.

Vzhledem k tomu, že tabulky columnstore obecně neobsahují data do komprimovaného segmentu columnstore, dokud nebude existovat více než 1 000 000 řádků na tabulku a každá tabulka fondu SQL je rozdělena do 60 tabulek, obecně platí, že tabulky columnstore nebudou využívat dotaz, pokud tabulka neobsahuje více než 60 000 000 řádků.  

Pro tabulku, která má méně než 60 000 000 řádků, nemusí mít žádný smysl index columnstore.  Ale také to nemusí vadit.  

Kromě toho, pokud svá data dělíte, pamatujte na to, že každý oddíl musí mít alespoň 1 milion řádků, abyste využili výhod clusterovaného indexu columnstore.  Pokud má tabulka 100 oddílů, bude muset mít aspoň 6 000 000 000 řádků, abyste využili výhod clusterovaného úložiště sloupců (60 distribuce *100 oddíly* řádky 1 000 000).  

Pokud vaše tabulka v tomto příkladu neobsahuje 6 miliard řádků, buď snižte počet oddílů, nebo místo ní zvažte použití tabulky haldy.  Můžete také experimentovat, abyste zjistili, jestli pomocí tabulky haldy se sekundárními indexy dosáhnete lepšího výkonu než s tabulkou columnstore.

> [!TIP]
> Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  

Viz také [indexy tabulky](sql-data-warehouse-tables-index.md), [Průvodce indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)a znovu [sestavování indexů columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Další kroky

Pokud v tomto článku nenajdete, co hledáte, zkuste vyhledat všechny dokumenty Azure synapse pomocí hledání dokumentů na levé straně této stránky.  

[Microsoft Q&Stránka s otázkou pro Azure synapse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) je místo, kde můžete klást otázky ostatním uživatelům a do skupiny produktů Azure synapse.  Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  

Pokud dáváte přednost dotazování na Stack Overflow, máme také [fórum služby Azure synapse Analytics Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).

K vytváření žádostí o funkce použijte stránku s [názory na Azure synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) .  Přidáním vlastních žádostí nebo hlasováním pro ostatní žádosti nám pomůžete určit prioritu funkcí.
