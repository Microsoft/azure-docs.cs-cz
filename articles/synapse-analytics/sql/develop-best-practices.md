---
title: Osvědčené postupy vývoje pro synapse SQL
description: Doporučení a osvědčené postupy, které byste měli znát při vývoji pro synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 479b20bcb0803d5483d139939da627d53ceccb49
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667612"
---
# <a name="development-best-practices-for-synapse-sql"></a>Osvědčené postupy vývoje pro synapse SQL

Tento článek popisuje doprovodné materiály a osvědčené postupy při vývoji řešení datového skladu. 

## <a name="dedicated-sql-pool-development-best-practices"></a>Osvědčené postupy při vývoji vyhrazených fondů SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování

Další informace o snižování nákladů prostřednictvím pozastavování a škálování najdete v tématu [Správa výpočetních služeb](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Udržujte statistiky

Ujistěte se, že statistické údaje aktualizujete denně nebo po každém zatížení.  Vždy existují kompromisy mezi výkonem a náklady na vytvoření a aktualizaci statistik. Pokud zjistíte, že se vám budou uchovávat všechna vaše statistiky, trvá vám moc dlouho, a proto je více selektivních, které sloupce mají statistické údaje nebo které sloupce potřebují často aktualizovat.  

Například můžete chtít aktualizovat sloupce kalendářních dat, do kterých lze přidat nové hodnoty denně. 

> [!NOTE]
> Nejvíc výhod získáte tak, že budete mít statistiku pro sloupce, které jsou součástí spojení, sloupce používané v klauzuli WHERE a sloupce nalezené v klauzuli GROUP BY.

Viz také [Správa statistik tabulek](develop-tables-statistics.md), [vytváření statistik](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true), [aktualizace statistiky](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

### <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash

Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování. Díky této možnosti můžou uživatelé snadno vytvářet tabulky, aniž by se museli rozhodovat, jak se mají jejich tabulky distribuovat.  Tabulky kruhového dotazování můžou být pro některé úlohy dostatečně výkonné. Ve většině případů je ale výběr distribučního sloupce mnohem lepší.  

Nejběžnějším příkladem, kdy tabulka distribuovaná podle sloupce zdaleka překoná tabulku kruhového dotazování, je spojení dvou velkých tabulek faktů.  

Například pokud máte tabulku Orders distribuovanou pomocí order_id a tabulku transakcí, která je také distribuována pomocí order_id, při připojení tabulky Orders k tabulce transakcí na order_id se tento dotaz stane průchozím dotazem. 

To znamená, že eliminují operace přesunu dat.  Méně kroků znamená rychlejší dotaz.  Méně přesunů dat také přispívá ke zrychlení dotazů.

> [!TIP]
> Při načítání distribuované tabulky se ujistěte, že příchozí data nejsou seřazená podle distribučního klíče, zpomalilo by to načítání.  

Další podrobnosti o tom, jak vybrat distribuční sloupec, může zvýšit výkon a jak definovat distribuovanou tabulku v klauzuli WITH příkazu CREATE TABLES, najdete na následujících odkazech.

Viz také [Přehled tabulek](develop-tables-overview.md), [distribuce tabulky](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Výběr distribuce tabulky](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)a [Create Table jako vyberte](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů
I když je možné rozdělit data na oddíly při údržbě dat prostřednictvím přepínání oddílů nebo optimalizací kontrol pomocí eliminace oddílu, může vaše dotazy zpomalit příliš mnoho oddílů.  Často se jedná o strategii vysoké členitosti, která může fungovat dobře na SQL Server nemusí dobře fungovat na vyhrazeném fondu SQL.  

> [!NOTE]
> Často se jedná o strategii vysoké členitosti, která může fungovat dobře na SQL Server nemusí dobře fungovat na vyhrazeném fondu SQL.  

Pokud máte příliš mnoho oddílů, může se také snížit efektivita clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 milion řádků. Vyhrazený fond SQL umožňuje rozdělit data na oddíly do databází 60. 

Takže pokud vytvoříte tabulku s 100 oddíly, výsledkem budou 6000 oddíly.  Každá úloha je jiná, takže nejlepší rada je, abyste s dělením experimentovali a zjistili, co je pro vaši úlohu nejvhodnější.  

Jedna z možností, kterou je třeba zvážit, je použití členitosti, která je nižší než ta, která by mohla být pro vás v SQL Server fungovala.  Například místo denního dělení zvažte použití týdenního nebo měsíčního dělení.

Viz také [dělení tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí

Příkazy INSERT, UPDATE a DELETE se spouštějí v rámci transakce, a když selžou, musí se transakce odvolat.  Abyste minimalizovali potenciální dlouhé odvolávání, minimalizujte velikost transakcí kdykoli je to možné.  Můžete to provést rozdělením příkazů INSERT, UPDATE a DELETE na části.  

Například pokud máte vložení, kterou očekáváte 1 hodinu, můžete vložit vložení na čtyři části a tím zkrátit každé spuštění na 15 minut.

> [!TIP]
> Abyste snížili riziko odvolání transakce, využijte příkazy s minimálním protokolováním, například CTAS, TRUNCATE, DROP TABLE nebo INSERT k vyprázdnění tabulek.  

Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  

Například místo provedení příkazu DELETE pro odstranění všech řádků v tabulce, kde datum_objednavky bylo Říjen 2001, můžete data dělit měsíčně a pak vyměnit oddíl s daty za prázdný oddíl z jiné tabulky (viz příklady příkazu ALTER TABLE).  

Pro nerozdělené tabulky zvažte použití CTAS k zápisu dat, která chcete uchovat v tabulce, a nepoužívejte DELETE.  Pokud CTAS zabere stejné množství času, je mnohem bezpečnější operace, protože má minimální transakční protokolování a v případě potřeby je můžete kdykoli zrušit.

Viz také [Principy transakcí](develop-transactions.md), [optimalizace transakcí](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [dělení tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)a [CREATE TABLE AS Select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce

Při definování knihovny DDL s použitím nejmenšího datového typu, který bude podporovat vaše data, dojde k vylepšení výkonu dotazů. Tato akce je zvláště důležitá pro sloupce typu CHAR a VARCHAR.  

Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Také definujte sloupce jako VARCHAR, pokud je to vše potřebné místo použití NVARCHAR.

Viz také [Přehled tabulek](develop-tables-overview.md), [typy tabulkových dat](develop-tables-data-types.md)a [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore

Clusterované indexy columnstore jsou jedním z nejúčinnějších způsobů, jak můžete ukládat data do vyhrazeného fondu SQL.  Ve výchozím nastavení jsou tabulky ve vyhrazeném fondu SQL vytvořeny jako clusterované ColumnStore.  

Pokud chcete dosáhnout co nejlepšího výkonu dotazů na tabulky columnstore, je důležité mít kvalitní segmenty.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  

Kvalitu segmentů lze změřit podle počtu řádků v komprimované skupině řádků.  Podrobné pokyny k zjišťování a zlepšování kvality segmentů clusterovaných tabulek columnstore najdete v článku o [příčinách nekvalitních indexů columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) a [indexů tabulek](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .  

Vzhledem k tomu, že jsou důležité segmenty columnstore s vysokou kvalitou, je vhodné použít ID uživatelů, která jsou ve střední nebo velké třídě prostředků pro načítání dat. Použití nižších [jednotek datového skladu](resource-consumption-models.md) znamená, že chcete uživateli, který načítá, přiřadit větší třídu prostředků.

Vzhledem k tomu, že tabulky columnstore obecně neobsahují data do komprimovaného segmentu columnstore, dokud nebude existovat více než 1 000 000 řádků na tabulku a každá vyhrazená tabulka fondu SQL je rozdělena do 60 tabulek, tabulky columnstore nebudou využívat dotaz, pokud tabulka neobsahuje více než 60 000 000 řádků.  

> [!TIP]
> Pro tabulky, které mají méně než 60 000 000 řádků, nemusí mít index columnstore optimální řešení.  

Pokud navíc zadáte oddíly dat, budete chtít zvážit, že každý oddíl musí mít 1 000 000 řádků, aby mohl využívat clusterový index columnstore.  Pokud má tabulka 100 oddílů, bude muset mít aspoň 6 000 000 000 řádků, abyste využili výhod clusterovaného úložiště sloupců (60 distribuce *100 oddíly* řádky 1 000 000).  

Pokud tabulka nemá 6 000 000 000 řádků, snižte počet oddílů nebo zvažte místo toho použití tabulky haldy.  Může také docházet k experimentování s cílem zjistit, zda je možné dosáhnout lepšího výkonu pomocí tabulky haldy se sekundárními indexy místo tabulky columnstore.

Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  

Viz také [indexy tabulky](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Průvodce indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)a [sestavení indexů columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="serverless-sql-pool-development-best-practices"></a>Osvědčené postupy při vývoji fondu SQL bez serveru

### <a name="general-considerations"></a>Obecné aspekty

Fond SQL bez serveru umožňuje dotazování souborů ve vašich účtech úložiště Azure. Nemá místní možnosti úložiště nebo ingestování, což znamená, že všechny soubory, které dotaz cílí, jsou externí pro fond SQL bez serveru. To znamená, že všechno, co se týká čtení souborů ze služby Storage, může mít dopad na výkon dotazů.

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>Společně najít účet Azure Storage a fond SQL bez serveru

Pokud chcete minimalizovat latenci, Najděte svůj účet Azure Storage a koncový bod fondu SQL bez serveru. Účty úložiště a koncové body zřízené během vytváření pracovního prostoru se nacházejí ve stejné oblasti.

Pro zajištění optimálního výkonu, pokud přistupujete k jiným účtům úložiště s fondem SQL bez serveru, ujistěte se, že jsou ve stejné oblasti. V opačném případě se zvýší latence síťového přenosu dat ze vzdálené oblasti do oblasti koncového bodu.

### <a name="azure-storage-throttling"></a>Omezení Azure Storage

K vašemu účtu úložiště může mít přístup více aplikací a služeb. Pokud kombinované IOPS nebo propustnost vygenerované aplikacemi, službami a úlohami fondu SQL serveru překračují limity účtu úložiště, dojde k omezení úložiště. Když dojde k omezování úložiště, má zásadní negativní vliv na výkon dotazů.

Po zjištění omezení má fond SQL bez serveru integrované zpracování tohoto scénáře. Fond SQL bez serveru bude podávat požadavky do úložiště pomalejším tempem, dokud se nevyřeší omezení. 

Pro optimální provádění dotazů ale doporučujeme, abyste během provádění dotazu nehodnotili účet úložiště dalšími úlohami.

### <a name="prepare-files-for-querying"></a>Příprava souborů pro dotazování

Pokud je to možné, můžete připravit soubory pro lepší výkon:

- Převede CSV na Parquet – Parquet je sloupcový formát. Vzhledem k tomu, že je komprimována, má menší velikost souborů než soubory CSV se stejnými daty a fond SQL bez serveru bude potřebovat méně času a požadavky na úložiště ke čtení.
- Pokud se dotaz zaměřuje na jeden velký soubor, budete ho moct rozdělit do několika menších souborů.
- Zkuste zachovat velikost souboru CSV nižší než 10 GB.
- Doporučuje se mít soubory stejné velikosti pro jednu cestu OPENROWSET nebo externí umístění tabulky.
- Rozdělení dat do oddílů ukládáním oddílů do různých složek nebo názvů souborů – Pokud [chcete cílit na konkrétní oddíly, ověřte použití funkcí filename a FilePath](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Cílení na konkrétní oddíly pomocí funkcí fileinfo a filepath

Data jsou často organizována v oddílech. SQL fondu bez serveru můžete dát k dotazování na konkrétní složky a soubory. Tím se sníží počet souborů a množství dat, které dotaz potřebuje ke čtení a zpracování. 

V důsledku toho dosáhnete lepšího výkonu. Další informace najdete v tématu funkce [filename](query-data-storage.md#filename-function) a [FilePath](query-data-storage.md#filepath-function) a příklady, jak [zadávat dotazy na konkrétní soubory](query-specific-files.md).

Pokud vaše data v úložišti nejsou rozdělená na oddíly, zvažte jejich dělení, aby bylo možné použít tyto funkce k optimalizaci dotazů, které cílí na tyto soubory.

Při [dotazování na oddíly Apache Spark externích tabulek Azure synapse](develop-storage-files-spark-tables.md) z fondu SQL bez serveru se dotaz automaticky cílí jenom na soubory, které potřebujete.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Použití CETAS ke zvýšení výkonu a spojení dotazů

[CETAS](develop-tables-cetas.md) je jednou z nejdůležitějších funkcí dostupných v neserverovém fondu SQL. CETAS je paralelní operace, která vytváří metadata externí tabulky a exportuje výsledek dotazu SELECT do sady souborů v účtu úložiště.

CETAS můžete použít k ukládání často používaných částí dotazů, jako jsou připojené referenční tabulky, do nové sady souborů. Později se můžete k této jedné externí tabulce připojit místo opakujících se běžných spojení ve více dotazech. 

Když CETAS generuje soubory Parquet, Statistika se automaticky vytvoří, když první dotaz cílí na tuto externí tabulku a získáte lepší výkon.

### <a name="next-steps"></a>Další kroky

Pokud potřebujete informace, které nejsou uvedené v tomto článku, pomocí funkce **vyhledat dokument** na levé straně této stránky Prohledejte všechny dokumenty fondu SQL.  [Microsoft Q&Stránka s otázkou pro Azure synapse Analytics](/answers/topics/azure-synapse-analytics.html) je místo, kde můžete klást otázky ostatním uživatelům a do skupiny produktů Azure synapse Analytics. Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  

Pokud dáváte přednost dotazování na Stack Overflow, máme také [fórum služby Azure synapse Analytics Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).
