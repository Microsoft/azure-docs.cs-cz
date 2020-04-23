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
ms.openlocfilehash: ed2638cfe4ab7e849e428729ccd17ffdeb6314af
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086347"
---
# <a name="development-best-practices-for-synapse-sql"></a>Doporučené postupy vývoje pro Synapse SQL
Tento článek popisuje pokyny a osvědčené postupy při vývoji řešení datového skladu. 

## <a name="sql-pool-development-best-practices"></a>Osvědčené postupy pro vývoj fondu SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování

Další informace o snižování nákladů prostřednictvím pozastavování a škálování najdete v tématu [Správa výpočetních služeb](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Udržujte statistiky

Ujistěte se, že aktualizujete své statistiky denně nebo po každém zatížení.  Vždy existují kompromisy mezi výkonem a náklady na vytvoření a aktualizaci statistik. Pokud zjistíte, že trvá příliš dlouho udržovat všechny statistiky, být více selektivní o tom, které sloupce mají statistiky nebo které sloupce je třeba časté aktualizace.  

Můžete například aktualizovat sloupce data, kde mohou být denně přidávány nové hodnoty. 

> [!NOTE]
> Největší užitek získáte tím, že budete mít statistiky o sloupcích zapojených do spojení, sloupcích použitých v klauzuli WHERE a sloupcích nalezených v souborech GROUP BY.

Viz také [Správa statistik tabulky](develop-tables-statistics.md), VYTVOŘENÍ [STATISTIKY](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [AKTUALIZACE STATISTIK .](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash

Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.  To usnadňuje uživatelům začít vytvářet tabulky, aniž by museli rozhodnout, jak by měly být distribuovány jejich tabulky.  Kulaté robin tabulky může provádět dostatečně pro některé úlohy. Ve většině případů však výběr distribučního sloupce bude fungovat mnohem lépe.  

Nejběžnějším příkladem, kdy tabulka distribuovaná podle sloupce zdaleka překoná tabulku kruhového dotazování, je spojení dvou velkých tabulek faktů.  

Máte-li například tabulku objednávek, která je distribuována order_id, a tabulku transakcí, která je distribuována také order_id, když připojíte tabulku objednávek k tabulce transakcí v order_id, stane se tento dotaz předávaným dotazem. 

To znamená, že eliminujeme operace pohybu dat.  Méně kroků znamená rychlejší dotaz.  Méně přesunů dat také přispívá ke zrychlení dotazů.

> [!TIP]
> Při načítání distribuované tabulky se ujistěte, že příchozí data nejsou seřazená podle distribučního klíče, zpomalilo by to načítání.  

Další podrobnosti o tom, jak může výběr distribučního sloupce zlepšit výkon a jak definovat distribuovanou tabulku v klauzuli WITH příkazu CREATE TABLES, naleznete v následujících odkazech.

Viz také [Přehled tabulky](develop-tables-overview.md), [Distribuce tabulek](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Výběr distribuce tabulky](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [VYTVOŘIT TABULKU](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a VYTVOŘIT TABULKU [JAKO VÝBĚR](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů
Zatímco rozdělení dat může být efektivní pro údržbu dat prostřednictvím přepínání oddílů nebo optimalizace skenování pomocí eliminace oddílu, s příliš mnoho oddílů může zpomalit vaše dotazy.  Často vysoká rozlišovací schopnost dělení strategie, která může fungovat dobře na SQL Server nemusí fungovat dobře na fondu SQL.  

> [!NOTE]
> Často vysoká rozlišovací schopnost dělení strategie, která může fungovat dobře na SQL Server nemusí fungovat dobře na fondu SQL.  

Pokud máte příliš mnoho oddílů, může se také snížit efektivita clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 milion řádků. Fond SQL rozdělí vaše data za vás do 60 databází. 

Pokud tedy vytvoříte tabulku se 100 oddíly, výsledkem bude 6000 oddílů.  Každá úloha je jiná, takže nejlepší rada je, abyste s dělením experimentovali a zjistili, co je pro vaši úlohu nejvhodnější.  

Jednou z možností, kterou je třeba zvážit, je použití rozlišovací schopnost, která je nižší než co může mít pracoval pro vás v SQL Server.  Například místo denního dělení zvažte použití týdenního nebo měsíčního dělení.

Viz také [dělení tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí

Příkazy INSERT, UPDATE a DELETE se spouštějí v rámci transakce, a když selžou, musí se transakce odvolat.  Abyste minimalizovali potenciální dlouhé odvolávání, minimalizujte velikost transakcí kdykoli je to možné.  Můžete to provést rozdělením příkazů INSERT, UPDATE a DELETE na části.  

Například pokud máte INSERT, který očekáváte, že trvat 1 hodinu, můžete rozdělit INSERT do čtyř částí, čímž se zkrátí každý běh na 15 minut.

> [!TIP]
> Abyste snížili riziko odvolání transakce, využijte příkazy s minimálním protokolováním, například CTAS, TRUNCATE, DROP TABLE nebo INSERT k vyprázdnění tabulek.  

Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  

Například místo provedení příkazu DELETE pro odstranění všech řádků v tabulce, kde datum_objednavky bylo Říjen 2001, můžete data dělit měsíčně a pak vyměnit oddíl s daty za prázdný oddíl z jiné tabulky (viz příklady příkazu ALTER TABLE).  

U tabulek bez oddílů zvažte místo příkazu DELETE použití příkazu CTAS k zápisu dat, která chcete v tabulce uchovat.  Pokud provedení příkazu CTAS trvá stejně dlouho, stále je to mnohem bezpečnější operace, protože zahrnuje minimální protokolování transakce a v případě potřeby ji lze rychle zrušit.

Viz také [principy transakcí](develop-transactions.md), [optimalizace transakcí](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [dělení tabulek](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [zkrácená tabulka](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), ZMĚNA [TABULKY](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a Vytvoření tabulky [jako výběru (CTAS).](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce

Při definování DDL můžete použitím nejmenšího datového typu, který podporuje vaše data, zvýšit výkon dotazu.  To je obzvlášť důležité pro sloupce typu CHAR a VARCHAR.  

Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho sloupce definujte jako VARCHAR, pokud tento typ splňuje všechny požadavky, místo používání NVARCHAR.

Viz také [Přehled tabulky](develop-tables-overview.md), Datové [typy tabulek](develop-tables-data-types.md)a [VYTVOŘIT TABULKU](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore

Clustered columnstore indexy jsou jedním z nejúčinnějších způsobů, jak můžete ukládat data ve fondu SQL.  Ve výchozím nastavení jsou tabulky ve fondu SQL vytvořeny jako clusterované columnstore.  

Pokud chcete dosáhnout co nejlepšího výkonu dotazů na tabulky columnstore, je důležité mít kvalitní segmenty.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  

Kvalitu segmentů lze změřit podle počtu řádků v komprimované skupině řádků.  Naleznete [příčiny špatné columnstore index kvality](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) a [tabulka indexy](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) článek krok za krokem na zjišťování a zlepšování kvality segmentu pro clusterované columnstore tabulek.  

Vzhledem k tomu, že vysoce kvalitní segmenty columnstore jsou důležité, je vhodné použít ID uživatelů, které jsou ve střední nebo velké třídě prostředků pro načítání dat. Použití [nižšíjednotky datového skladu](resource-consumption-models.md) znamená, že chcete přiřadit větší třídu prostředků pro načítacího uživatele.

Vzhledem k tomu, columnstore tabulky obecně nebude nabízená data do segmentu komprimované columnstore, dokud existuje více než 1 milion řádků na tabulku a každá tabulka fondu SQL je rozdělena do 60 tabulek, columnstore tabulky nebudou mít prospěch dotazu, pokud tabulka obsahuje více než 60 milionů řádků.  

> [!TIP]
> Pro tabulky s méně než 60 milionů řádků, které mají columstore index nemusí být optimální řešení.  

Kromě toho, pokud svá data dělíte, pamatujte na to, že každý oddíl musí mít alespoň 1 milion řádků, abyste využili výhod clusterovaného indexu columnstore.  Pokud tabulka obsahuje 100 oddílů, bude muset mít alespoň 6 miliard řádků, aby mohla těžit z úložiště seskupených sloupců (60 distribucí *100 oddílů* 1 milion řádků).  

Pokud vaše tabulka nemá 6 miliard řádků, buď snížit počet oddílů nebo zvážit použití haldy tabulky místo.  Také může být vhodné experimentovat, chcete-li zjistit, zda lze získat lepší výkon pomocí tabulky haldy se sekundárními indexy, nikoli columnstore tabulka.

Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  

Viz také [Tabulka indexy](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Columnstore indexy průvodce](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Opětovné sestavení columnstore indexy](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="sql-on-demand-development-best-practices"></a>Osvědčené postupy vývoje SQL na vyžádání

### <a name="general-considerations"></a>Obecné aspekty

SQL na vyžádání umožňuje dotazovat se na soubory ve vašich účtech úložiště Azure. Nemá možnosti místního úložiště nebo ingestování, což znamená, že všechny soubory, které cíle dotazu jsou externí sql na vyžádání. Proto vše, co souvisí s čtení souborů z úložiště může mít vliv na výkon dotazu.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocate Azure Storage účet a SQL na vyžádání

Chcete-li minimalizovat latenci, můžete spoluumístit účet úložiště Azure a koncový bod SQL na vyžádání. Účty úložiště a koncové body zřízené během vytváření pracovního prostoru se nacházejí ve stejné oblasti.

Pro optimální výkon, pokud máte přístup k jiným účtům úložiště s SQL na vyžádání, ujistěte se, že jsou ve stejné oblasti. V opačném případě bude zvýšena latence pro přenos sítě dat ze vzdálené oblasti do oblasti koncového bodu.

### <a name="azure-storage-throttling"></a>Omezení úložiště Azure

K vašemu účtu úložiště může přistupovat více aplikací a služeb. Když kombinované VOPS nebo propustnost generované aplikacemi, službami a sql úlohy na vyžádání překročí limity účtu úložiště, dojde k omezení úložiště. Dojde-li omezení úložiště, je podstatný negativní vliv na výkon dotazu.

Po zjištění omezení sql na vyžádání má integrované zpracování tohoto scénáře. SQL na vyžádání bude vyžadovat úložiště pomalejším tempem, dokud nebude vyřešeno omezení. 

Pro optimální spuštění dotazu se však doporučuje, abyste během provádění dotazu nezdůrazňují účet úložiště s jinými úlohami.

### <a name="prepare-files-for-querying"></a>Příprava souborů pro dotazování

Pokud je to možné, můžete připravit soubory pro lepší výkon:

- Převést CSV na parkety - Parkety je sloupcový formát. Vzhledem k tomu, že je komprimován, má menší velikosti souborů než soubory CSV se stejnými daty a SQL na vyžádání bude potřebovat méně času a požadavků na úložiště, aby si je přečetl.
- Pokud dotaz cílí na jeden velký soubor, budete mít prospěch z jeho rozdělení na více menších souborů.
- Zkuste zachovat velikost souboru CSV pod 10 GB.
- Upřednostňuje se mít stejně velké soubory pro jednu cestu OPENROWSET nebo externí tabulku LOCATION.
- Rozdělte data uložením oddílů do různých složek nebo názvů souborů - zkontrolujte, zda [použijte funkce filename a path pro cílové oddíly](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Použití funkcí fileinfo a filepath k cílení na určité oddíly

Data jsou často uspořádána v oddílech. Můžete dát pokyn SQL na vyžádání k dotazování na určité složky a soubory. Tím se sníží počet souborů a množství dat, které dotaz potřebuje ke čtení a zpracování. 

V důsledku toho dosáhnete lepšího výkonu. Další informace naleznete v nabídce funkcí [název souboru](develop-storage-files-overview.md#filename-function) a [cesty k souborům](develop-storage-files-overview.md#filepath-function) a příklady [dotazování určitých souborů](query-specific-files.md).

Pokud vaše data v úložišti není rozdělena na oddíly, zvažte jejich rozdělení, abyste mohli tyto funkce použít k optimalizaci dotazů zaměřených na tyto soubory.

Při [dotazování rozdělených tabulek Spark](develop-storage-files-spark-tables.md) z SQL na vyžádání se dotaz automaticky zaměří pouze na potřebné soubory.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Použití cetas umocnění výkonu dotazů a spojení

[CETAS](develop-tables-cetas.md) je jednou z nejdůležitějších funkcí dostupných v SQL na vyžádání. CETAS je paralelní operace, která vytvoří metadata externí tabulky a exportuje výsledek dotazu SELECT do sady souborů v účtu úložiště.

Cetas můžete použít k ukládání často používané části dotazů, jako jsou spojené referenční tabulky, do nové sady souborů. Později se můžete připojit k této jedné externí tabulce namísto opakování běžných spojení ve více dotazech. 

Jak CETAS generuje parketové soubory, statistiky budou automaticky vytvořeny, když první dotaz cílí na tuto externí tabulku a získáte lepší výkon.

### <a name="next-steps"></a>Další kroky

Pokud potřebujete informace, které nejsou uvedeny v tomto článku, použijte "Hledat dokumenty" na levé straně této stránky prohledat všechny dokumenty fondu SQL.  [Fórum fondu SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) je místem, kde můžete klást otázky ostatním uživatelům a skupině produktů fondu SQL.  

Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  Pokud dáváte přednost klást otázky na přetečení zásobníku, máme také [Azure SQL fond zásobníku přetečení fórum](https://stackoverflow.com/questions/tagged/azure-sqldw).
 