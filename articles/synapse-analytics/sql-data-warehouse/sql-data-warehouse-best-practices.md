---
title: Osvědčené postupy pro vyhrazený fond SQL (dřív SQL DW)
description: Doporučení a osvědčené postupy pro vývoj řešení pro vyhrazený fond SQL (dříve SQL DW) ve službě Azure synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d62ea594916dbf5656569b7e1a3afe9ee81d8f89
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685289"
---
# <a name="best-practices-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Osvědčené postupy pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics

Tento článek je kolekcí osvědčených postupů, které vám pomůžou dosáhnout optimálního výkonu z [vyhrazeného nasazení fondu SQL (dříve SQL DW)](sql-data-warehouse-overview-what-is.md) .  Účelem tohoto článku je poskytnout vám základní pokyny a zvýraznit důležité oblasti, které se zaměřují.  

## <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování

Další informace o snižování nákladů prostřednictvím pozastavování a škálování najdete v tématu [Správa výpočetních služeb](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Udržujte statistiky

Vyhrazený fond SQL (dřív SQL DW) se dá nakonfigurovat tak, aby automaticky zjišťoval a vytvořil statistiku pro sloupce.  Plány dotazů vytvořené optimalizátorem jsou dostupné jenom jako dostupné statistiky.  

Doporučujeme, abyste povolili AUTO_CREATE_STATISTICS pro vaše databáze a pravidelně aktualizovali statistiku nebo po každém zatížení, abyste zajistili, že statistiky pro sloupce používané ve vašich dotazech jsou vždycky aktuální.

Pokud zjistíte, že aktualizace všech statistik trvá příliš dlouho, můžete se pokusit o to, které sloupce potřebují časté aktualizace statistiky. Například můžete chtít denně aktualizovat sloupce s datem, do kterých se mohou přidávat nové hodnoty.

> [!TIP]
> Nejvíc výhod získáte tak, že budete mít aktualizované statistiky pro sloupce, které jsou součástí spojení, sloupce používané v klauzuli WHERE a sloupce nalezené v GROUP BY.

Viz také [Správa statistik tabulek](sql-data-warehouse-tables-statistics.md), [vytváření statistik](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [aktualizace statistik](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Použijte zobrazení dynamických zpráv k monitorování a optimalizaci dotazů

Vyhrazený fond SQL (dříve SQL DW) má několik zobrazení dynamické správy, které se dají použít k monitorování provádění dotazů.  Podrobné pokyny, jak se podívat na podrobnosti o spuštěném dotazu, najdete v článku [sledování úlohy pomocí zobrazení dynamické správy](sql-data-warehouse-manage-monitor.md) .  

S rychlým vyhledáváním dotazů v těchto zobrazeních dynamických zpráv může pomoci použití možnosti LABEL v dotazech.

Viz také [Monitorování úloh pomocí zobrazení dynamické správy](sql-data-warehouse-manage-monitor.md), [Label](sql-data-warehouse-develop-label.md), [Option](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [Sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ladění výkonu dotazů s využitím nových vylepšení produktu

- [Ladění výkonu s využitím materializovaných zobrazení](performance-tuning-materialized-views.md)
- [Ladění výkonu s využitím uspořádaného clusterovaného indexu columnstore](performance-tuning-ordered-cci.md)
- [Ladění výkonu s využitím ukládání sad výsledků do mezipaměti](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Seskupujte příkazy INSERT do dávek

Jednorázové načtení do malé tabulky s příkazem INSERT nebo dokonce pravidelným načtením vyhledávacího objektu se může ve vašich potřebách vykonat i v příkazu jako `INSERT INTO MyLookup VALUES (1, 'Type 1')` .  

Pokud však v průběhu dne potřebujete načíst tisíce nebo miliony řádků, můžete zjistit, že vám příkazy INSERT s jedním prvkem jednoduše nestačí.  Místo toho vyvíjejte své procesy tak, aby zapisovaly do souboru, který bude pravidelně kontrolovat a načítat další proces.

Viz také [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Použijte PolyBase k rychlému načítání a exportu dat

Vyhrazený fond SQL (dřív SQL DW) podporuje načítání a export dat prostřednictvím několika nástrojů, včetně Azure Data Factory, základů a BCP.  Pro malá množství dat, kde není výkon tak důležitý, by vám měl stačit libovolný nástroj.  Když však načítáte nebo exportujete velká množství dat nebo vyžadujete vysoký výkon, nejlepší volbou je PolyBase.  

Základna je navržená tak, aby využila distribuovanou povahu systému a načetla a exportovali velikost dat rychleji než jakýkoli jiný nástroj.  Úlohy funkce PolyBase můžete spustit pomocí příkazů CTAS nebo INSERT INTO.   

> [!TIP]
> Použití příkazu CTAS minimalizuje protokolování transakce a je to nejrychlejší způsob, jak načíst vaše data.

Azure Data Factory podporuje také základní zátěže a může dosáhnout podobného výkonu jako CTAS.  PolyBase podporuje řadu formátů souborů, včetně souborů GZip.  
  
> [!NOTE]
> Chcete-li maximalizovat propustnost při použití textových souborů gzip, rozdělte soubory do 60 nebo více souborů, čímž maximalizujete paralelismus zátěže.  Pro rychlejší celkovou propustnost zvažte souběžné načítání dat.

Viz také [načtení dat](design-elt-data-loading.md), [Průvodce používáním základních](guidance-for-loading-data.md), [vyhrazených vzorů a strategií načítání fondů SQL](/archive/blogs/sqlcat/), [načítání dat pomocí Azure Data Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), [přesouvání dat pomocí Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md), [Vytvoření externího formátu souboru](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [Vytvoření tabulky jako Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Načtěte a následně dotazujte externí tabulky

Přestože lze PolyBase (také označovanou jako externí tabulky) považovat za nejrychlejší způsob načítání dat, není ideální pro dotazy. Základní tabulky v současné době podporují pouze soubory objektů BLOB v Azure a Azure Data Lake úložiště. Tyto soubory nemají podporu v žádných výpočetních prostředcích.  

V důsledku toho vyhrazený fond SQL nemůže tuto práci vyřešit, a proto musí číst celý soubor tak, že ho načte do databáze tempdb, aby bylo možné data číst.  Proto pokud máte několik dotazů, které budou tato data dotazovat, je lepší data jednou nahrát a nastavit dotazy, aby používaly místní tabulku.

Viz také [Průvodce používáním základny](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash

Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.  To umožňuje uživatelům jednoduše začít vyvářet tabulky, aniž by se museli rozhodovat, jakým způsobem se mají jejich tabulky distribuovat.  Výkon tabulek kruhového dotazování může být pro některé úlohy dostatečný, ale ve většině případů bude lépe fungovat výběr distribučního sloupce.  

Nejběžnějším příkladem, kdy tabulka distribuovaná podle sloupce zdaleka překoná tabulku kruhového dotazování, je spojení dvou velkých tabulek faktů.  

Například pokud máte tabulku objednávek, která se distribuuje podle sloupce id_objednavky a tabulku transakcí, která se také distribuuje podle sloupce id_objednavky, a provedete spojení tabulky objednávek s tabulkou transakcí podle sloupce id_objednavky, z dotazu se stane předávací dotaz, to znamená, že eliminujeme operace přesunu dat.  Méně kroků znamená rychlejší dotaz.  Méně přesunů dat také přispívá ke zrychlení dotazů.  

> [!TIP]
> Při načítání distribuované tabulky se ujistěte, že příchozí data nejsou seřazená podle distribučního klíče, zpomalilo by to načítání.  

Další informace o tom, jak vybrat distribuční sloupec, může zlepšit výkon a jak definovat distribuovanou tabulku v klauzuli WITH příkazu CREATE TABLE, najdete na následujících odkazech.

Viz také [Přehled tabulek](sql-data-warehouse-tables-overview.md), [distribuce tabulky](sql-data-warehouse-tables-distribute.md), [výběr distribuce tabulky](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [Create Table jako SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů

I když je možné rozdělit data na oddíly při údržbě dat prostřednictvím přepínání oddílů nebo optimalizací kontrol pomocí eliminace oddílu, může vaše dotazy zpomalit příliš mnoho oddílů.  Často se jedná o strategii vysoké členitosti, která může fungovat dobře, SQL Server nemusí dobře fungovat ve vyhrazeném fondu SQL (dřív SQL DW).  

Pokud máte příliš mnoho oddílů, může se také snížit efektivita clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 milion řádků.  Mějte na paměti, že na pozadí vyhrazený fond SQL zadělí vaše data do databází 60, takže pokud vytvoříte tabulku s 100 oddíly, výsledkem je to, že se ve skutečnosti budou zobrazovat oddíly 6000.  

Každá úloha je jiná, takže nejlepší rada je, abyste s dělením experimentovali a zjistili, co je pro vaši úlohu nejvhodnější.  Zvažte použití nižší členitosti, než jaká by pro vás byla vhodná v systému SQL Server.  Například místo denního dělení zvažte použití týdenního nebo měsíčního dělení.

Viz také [dělení tabulky](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí

Příkazy INSERT, UPDATE a DELETE se spouštějí v rámci transakce, a když selžou, musí se transakce odvolat.  Abyste minimalizovali potenciální dlouhé odvolávání, minimalizujte velikost transakcí kdykoli je to možné.  Můžete to provést rozdělením příkazů INSERT, UPDATE a DELETE na části.  

Například pokud máte vložení, kterou očekáváte 1 hodinu, pokud je to možné, rozdělte vložení na čtyři části, které se spustí za 15 minut.  Abyste snížili riziko vrácení zpět, využijte zvláštní případy minimálního protokolování, jako je CTAS, ZKRÁCENí, odkládací tabulka nebo vložení do prázdných tabulek.  

Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  Například namísto provedení příkazu DELETE k odstranění všech řádků v tabulce, kde byla order_date v říjnu 2001, můžete rozdělit data měsíčně a pak na oddíl přejít s daty prázdného oddílu z jiné tabulky (viz příklady [příkazu ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ).  

Pro nerozdělené tabulky zvažte použití CTAS k zápisu dat, která chcete uchovat v tabulce, a nepoužívejte DELETE.  Pokud CTAS zabere stejné množství času, je mnohem bezpečnější operace, protože má minimální transakční protokolování a v případě potřeby je můžete kdykoli zrušit.

Viz také [Principy transakcí](sql-data-warehouse-develop-transactions.md), [optimalizace transakcí](sql-data-warehouse-develop-best-practices-transactions.md), [dělení tabulky](sql-data-warehouse-tables-partition.md), [Truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [CREATE TABLE AS Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Zmenšení velikosti výsledků dotazu

Tento krok vám pomůže vyhnout se problémům na straně klienta způsobeným velkým výsledkem dotazů.  Dotaz můžete upravit tak, aby se snížil počet vrácených řádků. Některé nástroje pro generování dotazů umožňují přidat k jednotlivým dotazům syntaxi "TOP N".  Výsledek dotazu můžete také CETAS na dočasnou tabulku a pak použít základní export pro zpracování nižší úrovně.

## <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce

Při definování knihovny DDL s použitím nejmenšího datového typu, který bude podporovat vaše data, dojde k vylepšení výkonu dotazů.  Tento přístup je zvláště důležitý pro sloupce typu CHAR a VARCHAR.  

Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho sloupce definujte jako VARCHAR, pokud tento typ splňuje všechny požadavky, místo používání NVARCHAR.

Viz také [Přehled tabulek](sql-data-warehouse-tables-overview.md), [typy tabulkových dat](sql-data-warehouse-tables-data-types.md), [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Použijte dočasné tabulky hald pro přechodná data

Při dočasném vytváření dat můžete zjistit, že použití tabulky haldy způsobí rychlejší zpracování celého procesu.  Pokud nahráváte data pouze za účelem jejich přípravy před spuštěním dalších transformací, nahrání tabulky do tabulky haldy bude mnohem rychlejší, než nahrání dat do clusterované tabulky columnstore.  

Nahrání dat do dočasné tabulky bude dokonce mnohem rychlejší, než nahrání tabulky do trvalého úložiště.  Dočasné tabulky začínají znakem "#" a jsou přístupné pouze v relaci, která ji vytvořila, takže mohou fungovat pouze v omezených scénářích.

Tabulky hald se definují v klauzuli WITH příkazu CREATE TABLE.  Pokud používáte dočasnou tabulku, nezapomeňte nad ní také vytvořit statistiky.

Viz také [dočasné tabulky](sql-data-warehouse-tables-temporary.md) [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [Create Table jako SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore

Clusterované indexy columnstore jsou jedním z nejúčinnějších způsobů, jak můžete ukládat data do vyhrazeného fondu SQL.  Ve výchozím nastavení jsou tabulky ve vyhrazeném fondu SQL vytvořeny jako clusterované ColumnStore.  Pokud chcete dosáhnout co nejlepšího výkonu dotazů na tabulky columnstore, je důležité mít kvalitní segmenty.  

Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Kvalitu segmentů lze změřit podle počtu řádků v komprimované skupině řádků.  Podrobné pokyny k detekci a zlepšení kvality segmentů clusterovaných tabulek columnstore najdete v článku o [příčinách špatné kvality indexu columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) v článku [indexy tabulky](sql-data-warehouse-tables-index.md) .  

Vzhledem k tomu, že jsou důležité segmenty columnstore s vysokou kvalitou, je vhodné použít ID uživatelů, která jsou ve střední nebo velké třídě prostředků pro načítání dat. Použití nižších [jednotek datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) znamená, že chcete uživateli, který načítá, přiřadit větší třídu prostředků.

Vzhledem k tomu, že tabulky columnstore obecně neobsahují data do komprimovaného segmentu columnstore, dokud nebude existovat více než 1 000 000 řádků na tabulku a každá vyhrazená tabulka fondu SQL je rozdělena do 60 tabulek, jako pravidlo jezdce nebudou tabulky columnstore dávat dotazy, pokud tabulka neobsahuje více než 60 000 000 řádků.  Pro tabulky s méně než 60 miliony řádků může být použití indexu columnstore zbytečné.  Ale také to nemusí vadit.  

Kromě toho, pokud svá data dělíte, pamatujte na to, že každý oddíl musí mít alespoň 1 milion řádků, abyste využili výhod clusterovaného indexu columnstore.  Pokud má tabulka 100 oddílů, bude muset mít aspoň 6 000 000 000 řádků, abyste využili výhod clusterovaného úložiště sloupců (60 distribuce *100 oddíly* řádky 1 000 000).  

Pokud vaše tabulka v tomto příkladu neobsahuje 6 miliard řádků, buď snižte počet oddílů, nebo místo ní zvažte použití tabulky haldy.  Můžete také experimentovat, abyste zjistili, jestli pomocí tabulky haldy se sekundárními indexy dosáhnete lepšího výkonu než s tabulkou columnstore.

> [!TIP]
> Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  

Viz také [Indexy tabulky](sql-data-warehouse-tables-index.md), [Průvodce indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Obnovení indexů columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Použijte větší třídu prostředků k vylepšení výkonu dotazu

Vyhrazený fond SQL používá skupiny prostředků jako způsob přidělení paměti pro dotazy.  Mimo pole jsou všichni uživatelé přiřazeni k malé třídě prostředků, která uděluje 100 MB paměti na distribuci.  Protože vždy existuje 60 distribucí a každé distribuci je poskytnuto minimálně 100 MB paměti, velikost celkově přidělené paměti v systému je 6 000 MB, tedy téměř 6 GB.  

Pro určité dotazy, například velká spojení nebo nahrávání do clusterovaných tabulek columnstore, bude větší přidělení paměti přínosem.  Některé dotazy, jako jsou čistě kontroly, nepřinesou žádnou výhodu.  Použití větších tříd prostředků ale redukuje souběžnost, takže budete chtít tento dopad vzít v úvahu před tím, než všechny uživatele přesunete na velkou třídu prostředků.

Viz také [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Použijte menší třídu prostředků pro zlepšení souběžnosti

Pokud si všimnete, že uživatelské dotazy mají dlouhou prodlevu, může to být tím, že uživatelé běží ve větších třídách prostředků a spotřebovávají mnoho slotů souběžnosti, které způsobují, že se jiné dotazy zařadí do fronty.  Pokud chcete zjistit, jestli jsou požadavky uživatelů ve frontě, spusťte příkaz `SELECT * FROM sys.dm_pdw_waits` a zkontrolujte, jestli se vrátí nějaké řádky.

Viz také [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md) [Sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="other-resources"></a>Další prostředky

Přečtěte si také článek [Řešení potíží](sql-data-warehouse-troubleshoot.md), ve kterém najdete běžné problémy a jejich řešení.

Pokud jste v tomto článku nenašli, co jste hledali, zkuste vyhledat všechny dokumenty Azure synapse pomocí hledání dokumentů na levé straně této stránky.  [Microsoft Q&Stránka s otázkou pro Azure synapse](/answers/topics/azure-synapse-analytics.html) je místo, kde můžete klást otázky ostatním uživatelům a do skupiny produktů Azure synapse. Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  

Pokud dáváte přednost dotazování na Stack Overflow, máme také [fórum služby Azure Synapse Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).

Pro žádosti o funkce prosím použijte stránku s [názory na Azure synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) .  Přidáním vlastních žádostí nebo hlasováním pro ostatní žádosti nám pomůžete určit prioritu funkcí.