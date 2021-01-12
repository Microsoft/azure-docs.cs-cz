---
title: Osvědčené postupy pro vyhrazené fondy SQL
description: Doporučení a osvědčené postupy, které byste měli znát při práci s vyhrazenými fondy SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 242e5d042aa14e3b7bd92ebb37ae1be61b1b2c8f
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120967"
---
# <a name="best-practices-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Osvědčené postupy pro vyhrazené fondy SQL ve službě Azure synapse Analytics

Tento článek poskytuje kolekci osvědčených postupů, které vám pomůžou dosáhnout optimálního výkonu vyhrazených fondů SQL ve službě Azure synapse Analytics. Níže najdete základní pokyny a důležité oblasti, se kterými se můžete soustředit při sestavování řešení. V každé části se seznámíte s konceptem a potom odkazujete na podrobnější články, které se vztahují k konceptu podrobněji.

## <a name="dedicated-sql-pools-loading"></a>Načítání vyhrazených fondů SQL

Pokyny k načítání vyhrazených fondů SQL najdete v tématu [doprovodné materiály k načítání dat](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování

Další informace o snížení nákladů prostřednictvím pozastavení a škálování najdete v tématu [Správa výpočtů](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Udržujte statistiky

I když SQL Server automaticky detekuje a vytváří nebo aktualizuje statistické údaje o sloupcích, vyhrazené fondy SQL vyžadují ruční údržbu statistik. Budete chtít udržovat vaše statistiky, abyste měli jistotu, že jsou plány fondu SQL optimalizované.  Plány vytvořené optimalizátorem jsou jenom tak dobré jako dostupné statistiky.

> [!TIP]
> Vytvoření vzorkové statistiky nad každým sloupcem je snadný způsob, jak začít se statistikami.  

Stejně důležité je aktualizovat statistiky, když dojde k významným změnám vašich dat.  Konzervativní přístup může být denní aktualizace statistik nebo aktualizace po každém načtení.  Vždy existují kompromisy mezi výkonem a náklady na vytvoření a aktualizaci statistik.

Pro zkrácení doby údržby, vybírejte, které sloupce mají statistické údaje, nebo potřebujete nejčastější aktualizace. Například můžete chtít aktualizovat sloupce kalendářních dat, do kterých lze každý den přidat nové hodnoty. Zaměřte se na použití statistik pro sloupce, které jsou součástí spojení, sloupce používané v klauzuli WHERE a sloupce nalezené v klauzuli GROUP BY.

Další informace o statistice najdete v článcích [Správa statistik tabulek](develop-tables-statistics.md), [vytváření statistik](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [aktualizace statistických údajů](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="group-insert-statements-into-batches"></a>Seskupujte příkazy INSERT do dávek

Jednorázové zatížení malé tabulky s příkazem INSERT, jako je například `INSERT INTO MyLookup VALUES (1, 'Type 1')` , může být nejlepším řešením v závislosti na vašich potřebách. Pokud však v průběhu dne potřebujete načíst tisíce nebo miliony řádků, je pravděpodobně vkládání typu Singleton neoptimální.

Jedním ze způsobů, jak tento problém vyřešit, je vyvinout jeden proces, který zapisuje do souboru, a pak jiný proces, který tento soubor pravidelně načítá. Další informace najdete v článku věnovaném [vložení](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Použijte PolyBase k rychlému načítání a exportu dat

Fond SQL podporuje načítání a export dat prostřednictvím několika nástrojů, včetně Azure Data Factory, základů a BCP.  Pro malá množství dat, kde není výkon tak důležitý, by vám měl stačit libovolný nástroj.  

> [!NOTE]
> Základem je nejlepší volba při načítání nebo exportování velkých objemů dat nebo potřebujete rychlejší výkon.

Úlohy funkce PolyBase můžete spustit pomocí příkazů CTAS nebo INSERT INTO. CTAS bude minimalizovat protokolování transakcí a představuje nejrychlejší způsob, jak načíst data. Azure Data Factory podporuje také základní zátěže a může dosáhnout výkonu podobně jako CTAS. Základna podporuje různé formáty souborů, včetně souborů gzip.

Chcete-li maximalizovat propustnost při použití textových souborů gzip, rozdělte soubory do 60 nebo více souborů, čímž maximalizujete paralelismus zátěže. Pro rychlejší celkovou propustnost zvažte souběžné načítání dat. Další informace o tématech souvisejících s touto částí najdete v následujících článcích:

- [Načtení dat](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Průvodce používáním funkce PolyBase](data-loading-best-practices.md)
- [Modely a strategie načítání Azure SQL fondu](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)
- [Načtení dat pomocí Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Přesun dat pomocí služby Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Vytvořit tabulku jako Select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Načtěte a následně dotazujte externí tabulky

Základová databáze není pro dotazy optimální. Základní tabulky pro vyhrazené fondy SQL aktuálně podporují pouze soubory objektů BLOB v Azure a Azure Data Lake úložiště. Tyto soubory nemají k dispozici žádné výpočetní prostředky. V důsledku toho vyhrazené fondy SQL nemůžou přesměrovat tuto práci a musí číst celý soubor načtením do tempdb, aby mohli data číst.

Pokud máte několik dotazů na dotazování na tato data, je lepší načíst tato data jednou a používat dotazy k použití místní tabulky. Další základní pokyny jsou uvedené v  [příručce k používání základního](data-loading-best-practices.md) článku.

## <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash

Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.   Díky tomu můžou uživatelé snadno začít vytvářet tabulky, aniž by se museli rozhodovat, jak se mají jejich tabulky distribuovat. Tabulky kruhového dotazování můžou být pro některé úlohy dostatečně výkonné. Ale ve většině případů poskytuje distribuční sloupec lepší výkon.  

Nejběžnějším příkladem tabulky distribuované pomocí sloupce, který vykonává tabulku kruhového dotazování, je, že se spojí dvě velké tabulky faktů.  

Například pokud máte tabulku objednávky distribuované pomocí order_id a tabulka transakcí je distribuována také pomocí order_id, při připojení tabulky Orders k tabulce transakcí v order_id se jedná o dotaz předávacího dotazu. Operace přesunu dat se pak eliminují. Méně kroků znamená rychlejší dotaz. Méně přesunů dat také přispívá ke zrychlení dotazů.

> [!NOTE]
> Při načítání distribuované tabulky by se vaše příchozí data neměla seřadit podle distribučního klíče. Tím se vaše zatížení zpomalí.

Níže uvedené odkazy na články vám poskytnou další podrobnosti o vylepšení výkonu prostřednictvím výběru distribučního sloupce. Najdete zde také informace o tom, jak definovat distribuovanou tabulku v klauzuli WITH příkazu CREATE TABLE:

- [Přehled tabulky](develop-tables-overview.md)
- [Distribuce tabulky](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Výběr distribuce tabulky](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů

I když je možné rozdělit data na oddíly při údržbě dat prostřednictvím přepínání oddílů nebo optimalizací kontrol pomocí eliminace oddílu, může vaše dotazy zpomalit příliš mnoho oddílů.  Často se jedná o strategii vysoké členitosti, která může fungovat dobře na SQL Server nemusí dobře fungovat na vyhrazeném fondu SQL.  

Příliš mnoho oddílů může snížit efektivitu clusterovaných indexů columnstore, pokud má každý oddíl méně než 1 000 000 řádků. vyhrazené fondy SQL automaticky rozdělí data do databáze 60. Takže pokud vytvoříte tabulku s 100 oddíly, výsledkem budou 6000 oddíly. Každá úloha je odlišná, takže nejlepším postupem je experimentovat s vytvářením oddílů, abyste viděli, co nejlépe vyhovuje vašim úlohám.  

Jedna z možností, kterou je třeba zvážit, je použití členitosti, která je nižší než implementace pomocí SQL Server. Zvažte například použití týdenních nebo měsíčních oddílů namísto denních oddílů.

Další informace o dělení do oddílů jsou podrobně popsané v článku [dělení tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí

Příkazy INSERT, UPDATE a DELETE se spouštějí v transakci. Když selžou, musí se vrátit zpátky. Chcete-li omezit potenciál dlouhého vrácení zpět, minimalizujte velikost transakcí, kdykoli je to možné.  Minimalizace velikosti transakcí lze provést rozdělením příkazů INSERT, UPDATE a DELETE do částí. Například pokud máte vložení, které očekáváte 1 hodinu, můžete vložit na čtyři části. Každé spuštění pak bude zkráceno na 15 minut.  

> [!TIP]
> Pokud chcete snížit riziko vrácení zpět, využijte zvláštní případy minimálního protokolování, například CTAS, ZKRÁCENí, odkládací tabulka nebo vložení do prázdných tabulek.  

Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  Například namísto provedení příkazu DELETE pro odstranění všech řádků v tabulce, kde order_date byla v říjnu 2001, mohli byste rozdělit data měsíčně. Pak můžete oddíl s daty prázdného oddílu přepínat z jiné tabulky (viz příklady příkazu ALTER TABLE).  

Pro nerozdělené tabulky zvažte použití CTAS k zápisu dat, která chcete uchovat v tabulce, a nepoužívejte DELETE.  Pokud CTAS zabere stejné množství času, je mnohem bezpečnější spustit, protože má minimální transakční protokolování a v případě potřeby je možné rychle zrušit.

Další informace o obsahu, který se vztahuje k této části, najdete v článcích níže:

- [Vytvořit tabulku jako Select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Vysvětlení transakcí](develop-transactions.md)
- [Optimalizace transakcí](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Dělení tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="reduce-query-result-sizes"></a>Zmenšení velikosti výsledků dotazu

Omezení velikosti výsledků dotazu pomáhá vyhnout se problémům na straně klienta způsobeným rozsáhlými výsledky dotazů.  Dotaz můžete upravit tak, aby se snížil počet vrácených řádků. Některé nástroje pro generování dotazů umožňují přidat k jednotlivým dotazům syntaxi "TOP N".  Výsledek dotazu můžete také CETAS na dočasnou tabulku a pak použít základní export pro zpracování nižší úrovně.

## <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce

Při definování knihovny DDL použijte nejmenší datový typ, který bude podporovat vaše data. tím se zvýší výkon dotazů.  Toto doporučení je zvláště důležité pro sloupce typu CHAR a VARCHAR.  Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho definujte sloupce jako VARCHAR, pokud je to vše potřebné místo použití NVARCHAR.

Podrobnější přehled základních konceptů, které jsou relevantní pro výše uvedené informace, najdete v článku [Přehled tabulek](develop-tables-overview.md), [typy tabulkových dat](develop-tables-data-types.md)a [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="use-temporary-heap-tables-for-transient-data"></a>Použijte dočasné tabulky hald pro přechodná data

Když dočasně vydáváte data na vyhrazené fondy SQL, tabulky haldy obvykle vytvářejí celkový proces rychleji.  Pokud načítáte data pouze pro přípravu před spuštěním více transformací, je načítání tabulky do tabulky haldy rychlejší než načítání dat do clusterované tabulky columnstore.  

Načítání dat do dočasné tabulky se také načítá mnohem rychleji než načtení tabulky do trvalého úložiště.  Dočasné tabulky začínají znakem "#" a jsou přístupné pouze v relaci, která ji vytvořila. V důsledku toho mohou fungovat pouze v omezených scénářích. Tabulky hald se definují v klauzuli WITH příkazu CREATE TABLE.  Pokud používáte dočasnou tabulku, nezapomeňte nad ní také vytvořit statistiky.

Další doprovodné materiály najdete v článku [dočasné tabulky](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [Create Table jako vyberte](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) články.

## <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore

Clusterované indexy columnstore jsou jedním z nejúčinnějších způsobů, jak můžete ukládat data do vyhrazeného fondu SQL.  Ve výchozím nastavení jsou tabulky ve vyhrazeném fondu SQL vytvořeny jako clusterované ColumnStore.  Pokud chcete dosáhnout co nejlepšího výkonu dotazů na tabulky columnstore, je důležité mít kvalitní segmenty.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  

Kvalita segmentů se dá měřit počtem řádků v komprimované skupině řádků. Podrobné pokyny k detekci a zlepšení kvality segmentů clusterovaných tabulek columnstore najdete v článku o [příčinách špatné kvality indexu columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) v článku [indexy tabulky](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .  

Vzhledem k tomu, že jsou důležité segmenty columnstore s vysokou kvalitou, je vhodné použít ID uživatelů, která jsou ve střední nebo velké třídě prostředků pro načítání dat. Použití nižších [jednotek datového skladu](resource-consumption-models.md) znamená, že chcete uživateli, který načítá, přiřadit větší třídu prostředků.

Tabulky columnstore obecně nezadávají data do komprimovaného segmentu columnstore, dokud nebude existovat více než 1 000 000 řádků na tabulku. Každá vyhrazená tabulka fondu SQL je rozdělená na oddíly tabulky 60. V takovém případě tabulky columnstore nebudou využívat dotaz, pokud tabulka obsahuje více než 60 000 000 řádků.  

> [!TIP]
> Pro tabulky, které mají méně než 60 000 000 řádků, nemusí mít index columnstore optimální řešení.  

Při vytváření oddílů dat musí mít každý oddíl 1 000 000 řádků, které budou využívat clusterovaný index columnstore.  V případě tabulky s 100 oddíly musí mít více než 6 000 000 000 řádků, aby bylo možné těžit z clusterovaného úložiště sloupců (60 distribuce *100 oddíly* řádky 1 000 000).  

Pokud tabulka nemá 6 000 000 000 řádků, máte dvě hlavní možnosti. Buď snižte počet oddílů, nebo zvažte místo toho použití tabulky haldy.  Může také docházet k experimentování s cílem zjistit, zda je možné dosáhnout lepšího výkonu pomocí tabulky haldy se sekundárními indexy místo tabulky columnstore.

Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  Další informace o indexech tabulek a columnstore a najdete je v článku [indexy tabulky](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Průvodce indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [nové sestavení indexů columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true#rebuilding-indexes-to-improve-segment-quality) .

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Použijte větší třídu prostředků k vylepšení výkonu dotazu

Fondy SQL používají skupiny prostředků jako způsob přidělování paměti pro dotazy. Zpočátku jsou všichni uživatelé přiřazeni k malé třídě prostředků, která uděluje 100 MB paměti na distribuci.  K dispozici jsou vždy 60 distribuce. Každé distribuci je přidělené minimálně 100 MB. Celkové přidělení paměti pro celé systémy je 6 000 MB nebo jen pod 6 GB.  

Pro určité dotazy, například velká spojení nebo nahrávání do clusterovaných tabulek columnstore, bude větší přidělení paměti přínosem.  Některé dotazy, například čistě prověřování, uvidí bez výhod. Použití větších tříd prostředků má vliv na souběžnost. Proto si budete chtít pamatovat tyto skutečnosti před tím, než všechny uživatele přesunete na velkou třídu prostředků.

Další informace o třídách prostředků najdete v článku [třídy prostředků pro správu úloh](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Použití menší třídy prostředků ke zvýšení souběžnosti

Pokud si všimnete delšího zpoždění v dotazech uživatele, můžou být vaši uživatelé spuštěni ve větších třídách prostředků. Tento scénář podporuje spotřebu slotů souběžnosti, což může způsobit, že se další dotazy zařadí do fronty.  Chcete-li zjistit, zda jsou dotazy uživatelů zařazeny do fronty, spusťte příkaz `SELECT * FROM sys.dm_pdw_waits` a zjistěte, zda jsou vráceny nějaké řádky.

[Třídy prostředků pro správu úloh](../sql-data-warehouse/resource-classes-for-workload-management.md) a [Sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) články vám poskytnou další informace.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Použijte zobrazení dynamických zpráv k monitorování a optimalizaci dotazů

Fondy SQL mají několik zobrazení dynamické správyů, které se dají použít k monitorování provádění dotazů.  Níže uvedený článek o monitorování vás provede podrobnými pokyny k zobrazení podrobností o spuštěném dotazu.  S rychlým vyhledáváním dotazů v těchto zobrazeních dynamických zpráv může pomoci použití možnosti LABEL v dotazech. Další podrobné informace najdete v článcích uvedených v následujícím seznamu:

- [Monitorování vaší úlohy pomocí DMV](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [POPISEK](develop-label.md)
- [NASTAVENÍ](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Další kroky

Běžné problémy a řešení najdete také v článku [věnovaném řešení potíží](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

Pokud potřebujete informace, které nejsou k dispozici v tomto článku, vyhledejte na [stránce s dotazem na Synapseu Microsoft&Q](/answers/topics/azure-synapse-analytics.html) , kde můžete klást otázky ostatním uživatelům a skupině produktů fondu SQL.  

Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  Pokud dáváte přednost dotazování na Stack Overflow, máme také [Fórum Azure SQL pool Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).

Pro žádosti o funkce použijte stránku [názory na fond SQL Azure](https://feedback.azure.com/forums/307516-sql-data-warehouse) .  Přidání vašich žádostí nebo hlasování o dalších žádostech nám pomůže soustředit se na většinu funkcí na vyžádání.