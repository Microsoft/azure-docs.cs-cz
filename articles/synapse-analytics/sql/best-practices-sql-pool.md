---
title: Doporučené postupy pro fondy SQL
description: Doporučení a osvědčené postupy, které byste měli znát při práci s fondy SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427793"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Doporučené postupy pro fondy SQL v Azure Synapse Analytics

Tento článek obsahuje kolekci osvědčených postupů, které vám pomohou dosáhnout optimálního výkonu pro fondy SQL v Azure Synapse Analytics. Níže najdete základní pokyny a důležité oblasti, na které se můžete zaměřit při vytváření řešení. Každá část vás seznámí s konceptem a pak vás naukazuje na podrobnější články, které pokrývají koncept do větší hloubky.

## <a name="sql-pools-loading"></a>Načítání fondů SQL

Pokyny pro načítání fondů SQL naleznete v [tématu Pokyny pro načítání dat](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Snižte náklady pomocí pozastavení a škálování

Další informace o snížení nákladů prostřednictvím pozastavení a škálování naleznete v [tématu Správa výpočetních prostředků](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Udržujte statistiky

Zatímco SQL Server automaticky detekuje a vytváří nebo aktualizuje statistiky sloupců, fondy SQL vyžadují ruční údržbu statistik. Budete chtít udržovat statistiky, abyste zajistili, že plány fondu SQL jsou optimalizovány.  Plány vytvořené optimalizátorem jsou jenom tak dobré jako dostupné statistiky.

> [!TIP]
> Vytvoření vzorkové statistiky nad každým sloupcem je snadný způsob, jak začít se statistikami.  

Stejně důležité je aktualizovat statistiky, když dojde k významným změnám vašich dat.  Konzervativní přístup může být denní aktualizace statistik nebo aktualizace po každém načtení.  Vždy existují kompromisy mezi výkonem a náklady na vytvoření a aktualizaci statistik.

Chcete-li zkrátit dobu údržby statistiky, buďte selektivní, které sloupce mají statistiky nebo potřebujete nejčastější aktualizaci. Můžete například aktualizovat sloupce kalendářních dat, ve kterých mohou být denně přidávány nové hodnoty. Zaměřte se na statistiky pro sloupce zapojené do spojení, sloupce používané v klauzuli WHERE a sloupce nalezené v souborem GROUP BY.

Další informace o statistikách naleznete v článcích [Spravovat statistiky tabulky](develop-tables-statistics.md), [VYTVOŘIT STATISTIKU](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [AKTUALIZOVAT STATISTIKU.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="group-insert-statements-into-batches"></a>Seskupujte příkazy INSERT do dávek

Jednorázové zatížení malé tabulky s příkazem INSERT, `INSERT INTO MyLookup VALUES (1, 'Type 1')`jako je například nejlepší přístup v závislosti na vašich potřebách. Pokud však potřebujete načíst tisíce nebo miliony řádků po celý den, je pravděpodobné, že singleton INSERTS nejsou optimální.

Jedním ze způsobů, jak tento problém vyřešit, je vyvinout jeden proces, který zapisuje do souboru, a pak jiný proces pro pravidelné načítání tohoto souboru. Další informace naleznete v článku [INSERT.](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Použijte PolyBase k rychlému načítání a exportu dat

Fond SQL podporuje načítání a export dat prostřednictvím několika nástrojů, včetně Azure Data Factory, PolyBase a BCP.  Pro malá množství dat, kde není výkon tak důležitý, by vám měl stačit libovolný nástroj.  

> [!NOTE]
> Polybase je nejlepší volbou při načítání nebo exportu velkých objemů dat nebo potřebujete vyšší výkon.

Úlohy funkce PolyBase můžete spustit pomocí příkazů CTAS nebo INSERT INTO. CTAS minimalizuje protokolování transakcí a je nejrychlejší způsob, jak načíst data. Azure Data Factory také podporuje zatížení PolyBase a může dosáhnout výkonu podobné CTAS. PolyBase podporuje různé formáty souborů, včetně souborů Gzip.

Chcete-li maximalizovat propustnost při použití textových souborů GZIP, rozdělte soubory do 60 nebo více souborů, abyste maximalizovali paralelismus zatížení. Pro rychlejší celkovou propustnost zvažte souběžné načítání dat. Další informace pro témata týkající se této části jsou uvedeny v následujících článcích:

- [Načtení dat](data-loading-overview.md)
- [Průvodce používáním funkce PolyBase](data-loading-best-practices.md)
- [Vzory a strategie načítání fondu Azure SQL](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Načítání dat pomocí Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Přesun dat pomocí služby Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Vytvořit tabulku jako výběr (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Načtěte a následně dotazujte externí tabulky

Polybase není optimální pro dotazy. Tabulky Polybase pro fondy SQL v současné době podporují jenom soubory objektů blob Azure a úložiště Azure Data Lake. Tyto soubory nemají žádné výpočetní prostředky, které by je podporovaly. V důsledku toho fondy SQL nelze převést tuto práci a musí číst celý soubor načtením do tempdb, aby mohl číst data.

Pokud máte několik dotazů pro dotazování na tato data, je lepší načíst tato data jednou a dotazy použít místní tabulku. Další polybase pokyny je součástí [průvodce pro použití PolyBase](data-loading-best-practices.md) článku.

## <a name="hash-distribute-large-tables"></a>Distribuujte velké tabulky pomocí hodnot hash

Ve výchozím nastavení jsou tabulky distribuované metodou kruhového dotazování.   Toto výchozí nastavení usnadňuje uživatelům začít vytvářet tabulky, aniž by museli rozhodnout, jak mají být jejich tabulky distribuovány. Kulaté robin tabulky může provádět dostatečně pro některé úlohy. Ale ve většině případů distribuční sloupec poskytuje lepší výkon.  

Nejběžnějším příkladem tabulky distribuované sloupcem, který překonává tabulku Kruhové dotazování, je, když jsou spojeny dvě velké tabulky faktů.  

Pokud máte například tabulku objednávek distribuovanou order_id a tabulku transakcí také distribuovanou order_id, když připojíte tabulku objednávek k tabulce transakcí v order_id, stane se tento dotaz předávaným dotazem. Operace přesunu dat jsou pak eliminovány. Méně kroků znamená rychlejší dotaz. Méně přesunů dat také přispívá ke zrychlení dotazů.

> [!NOTE]
> Při načítání distribuované tabulky by příchozí data neměla být seřazena v distribučním klíči. Tím se zpomalí vaše zatížení.

Níže uvedené odkazy na článek vám poskytnou další podrobnosti o zlepšení výkonu výběrem distribučního sloupce. Také najdete informace o tom, jak definovat distribuované tabulky v WITH klauzule příkazu CREATE TABLE:

- [Přehled tabulky](develop-tables-overview.md)
- [Distribuce tabulky](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Výběr distribuce tabulky](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Nevytvářejte zbytečně moc oddílů

Zatímco rozdělení dat může být efektivní pro údržbu dat prostřednictvím přepínání oddílů nebo optimalizace skenování pomocí eliminace oddílu, s příliš mnoho oddílů může zpomalit vaše dotazy.  Často vysoká rozlišovací schopnost dělení strategie, která může fungovat dobře na SQL Server nemusí fungovat dobře na fondu SQL.  

S příliš mnoho oddílů může snížit účinnost clusterovaných columnstore indexy, pokud každý oddíl má méně než 1 milion řádků. Fondy SQL automaticky rozdělují data do 60 databází. Pokud tedy vytvoříte tabulku se 100 oddíly, výsledkem bude 6000 oddílů. Každá úloha je jiná, takže nejlepší rada je experimentovat s dělením, abyste zjistili, co funguje nejlépe pro vaše úlohy.  

Jednou z možností, kterou je třeba zvážit, je použití rozlišovací schopnost, která je nižší než co jste implementovali pomocí SQL Server. Zvažte například použití týdennínebo měsíční oddíly namísto denní oddíly.

Další informace o dělení je podrobně popsáno v [tabulce dělení](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) článku.

## <a name="minimize-transaction-sizes"></a>Minimalizujte velikosti transakcí

Příkazy INSERT, UPDATE a DELETE jsou spuštěny v transakci. Když se lžou, musí být vráceny zpět. Chcete-li snížit potenciál pro dlouhé vrácení zpět, minimalizovat velikosti transakcí, kdykoli je to možné.  Minimalizace velikosti transakcí lze provést rozdělením příkazů INSERT, UPDATE a DELETE na části. Například pokud máte INSERT, který očekávát trvat 1 hodinu, můžete rozdělit INSERT do čtyř částí. Každý běh se pak zkrátí na 15 minut.  

> [!TIP]
> Využijte speciální případy minimální protokolování, jako je CTAS, TRUNCATE, DROP TABLE nebo INSERT, k prázdným tabulkám, abyste snížili riziko vrácení zpět.  

Dalším způsobem, jak eliminovat odvolávání transakcí, je použít ke správě dat operace pouze nad metadaty, jako třeba přepínání oddílů.  Například místo spuštění příkazu DELETE k odstranění všech řádků v tabulce, kde byla order_date v říjnu 2001, můžete data rozdělit měsíčně. Pak můžete přepínat oddíl s daty pro prázdný oddíl z jiné tabulky (viz příklady ALTER TABLE).  

Pro tabulky bez oddílů zvažte použití CTAS k zápisu dat, která chcete zachovat v tabulce, nikoli pomocí příkazu DELETE.  Pokud CTAS trvá stejné množství času, je mnohem bezpečnější spustit, protože má minimální protokolování transakcí a může být v případě potřeby rychle zrušena.

Další informace o obsahu souvisejícím s touto částí jsou uvedeny v níže uvedených článcích:

- [Vytvořit tabulku jako výběr (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Vysvětlení transakcí](develop-transactions.md)
- [Optimalizace transakcí](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Dělení tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Zkrátit tabulku](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Zmenšení velikosti výsledků dotazu

Snížení velikosti výsledků dotazu pomáhá vyhnout se problémům na straně klienta způsobeným velkými výsledky dotazu.  Dotaz můžete upravit a snížit tak počet vrácených řádků. Některé nástroje pro generování dotazu umožňují přidat syntaxi "top N" do každého dotazu.  Můžete také CETAS výsledek dotazu do dočasné tabulky a potom použít Export PolyBase pro zpracování nižší úrovně.

## <a name="use-the-smallest-possible-column-size"></a>Použijte co nejmenší velikost sloupce

Při definování DDL použijte nejmenší datový typ, který bude podporovat vaše data, jak to zlepší výkon dotazu.  Toto doporučení je zvláště důležité pro sloupce CHAR a VARCHAR.  Pokud má nejdelší hodnota v sloupci 25 znaků, nadefinujte typ sloupce jako VARCHAR(25).  Vyhněte se definování všech sloupců se znaky na výchozí délku.  Kromě toho definovat sloupce jako VARCHAR, pokud je vše, co je potřeba, spíše než pomocí NVARCHAR.

Podrobnější přehled základních konceptů týkajících se výše uvedených informací naleznete v [přehledu tabulek](develop-tables-overview.md), [tabulkových datových typech](develop-tables-data-types.md)a v článcích [VYTVOŘIT TABULKU.](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="use-temporary-heap-tables-for-transient-data"></a>Použijte dočasné tabulky hald pro přechodná data

Když dočasně přistáváte data do fondů SQL, tabulky hald obecně urychlí celkový proces.  Pokud načítáte data pouze pro fázi před spuštěním více transformací, načítání tabulky do tabulky haldy bude rychlejší než načítání dat do clusterované columnstore tabulky.  

Načítání dat do dočasné tabulky se také načte mnohem rychleji než načítání tabulky do trvalého úložiště.  Dočasné tabulky začínají "#" a jsou přístupné pouze relace, která ji vytvořila. V důsledku toho mohou fungovat pouze v omezených scénářích. Tabulky hald se definují v klauzuli WITH příkazu CREATE TABLE.  Pokud používáte dočasnou tabulku, nezapomeňte nad ní také vytvořit statistiky.

Další pokyny naleznete v [dočasných tabulkách](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [vytvořit tabulku](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a vytvořit tabulku jako [výběr](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) články.

## <a name="optimize-clustered-columnstore-tables"></a>Optimalizujte clusterované tabulky columnstore

Clustered columnstore indexy jsou jedním z nejúčinnějších způsobů, jak můžete ukládat data ve fondu SQL.  Ve výchozím nastavení jsou tabulky ve fondu SQL vytvořeny jako clusterované columnstore.  Pokud chcete dosáhnout co nejlepšího výkonu dotazů na tabulky columnstore, je důležité mít kvalitní segmenty.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  

Kvalitu segmentu lze měřit počtem řádků ve skupině komprimovaných řádků. Podrobné pokyny ke zjišťování a zlepšování kvality segmentů pro clusterované tabulky columnstore naleznete v článku [Příčiny špatné kvality indexu columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) v tabulce [indexů.](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)  

Vzhledem k tomu, že vysoce kvalitní segmenty columnstore jsou důležité, je vhodné použít ID uživatelů, které jsou ve střední nebo velké třídy prostředků pro načítání dat. Použití [nižšíjednotky datového skladu](resource-consumption-models.md) znamená, že chcete přiřadit větší třídu prostředků pro načítacího uživatele.

Columnstore tabulky obecně nebude nabízená data do segmentu komprimované columnstore, dokud existuje více než 1 milion řádků na tabulku. Každá tabulka fondu SQL je rozdělena do 60 tabulek. Jako takové columnstore tabulky nebude mít prospěch dotazu, pokud tabulka má více než 60 milionů řádků.  

> [!TIP]
> Pro tabulky s méně než 60 milionů řádků s columnstore index nemusí být optimální řešení.  

Pokud rozdělíte data, každý oddíl bude muset mít 1 milion řádků těžit z clusterované columnstore indexu.  Pro tabulku se 100 oddíly musí mít alespoň 6 miliard řádků, aby bylo nutné využívat úložiště seskupených sloupců (60 distribucí *100 oddílů* 1 milion řádků).  

Pokud tabulka nemá 6 miliard řádků, máte dvě hlavní možnosti. Buď snížit počet oddílů nebo zvážit použití haldy tabulky místo.  Také může být vhodné experimentovat, chcete-li zjistit, zda lze získat lepší výkon pomocí tabulky haldy se sekundárními indexy, nikoli columnstore tabulka.

Při dotazování tabulky columnstore budou příkazy pracovat rychleji, pokud vyberete pouze sloupce, které potřebujete.  Další informace o indexech table a columnstore a lze je nalézt v rámci [tabulky indexy](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Columnstore indexy vodítko](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [opětovné sestavení columnstore indexy](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) články.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Použijte větší třídu prostředků k vylepšení výkonu dotazu

Fondy SQL používají skupiny prostředků jako způsob přidělení paměti dotazům. Zpočátku jsou všichni uživatelé přiřazeni ke třídě malých prostředků, která uděluje 100 MB paměti na distribuci.  Vždy existuje 60 distribucí. Každé distribuci je přiděleno minimálně 100 MB. Celkové přidělení paměti celého systému je 6 000 MB nebo méně než 6 GB.  

Pro určité dotazy, například velká spojení nebo nahrávání do clusterovaných tabulek columnstore, bude větší přidělení paměti přínosem.  Některé dotazy, například čisté prohledávací, neuvidí žádnou výhodu. Využití větší třídy prostředků má vliv souběžnosti. Takže budete chtít mít na paměti, tyto skutečnosti před přesunutím všech uživatelů do velké třídy prostředků.

Další informace o třídách prostředků naleznete v článku [Třídy prostředků pro správu pracovního vytížení.](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Zvýšení souběžnosti pomocí menší třídy prostředků

Pokud zjistíte, dlouhé zpoždění v uživatelských dotazech, vaši uživatelé mohou být spuštěny ve větších třídách prostředků. Tento scénář podporuje spotřebu slotů souběžnosti, což může způsobit, že ostatní dotazy do fronty.  Chcete-li zjistit, zda jsou `SELECT * FROM sys.dm_pdw_waits` dotazy uživatelů zařazeny do fronty, spusťte a zjistěte, zda jsou vráceny všechny řádky.

[Třídy Resource pro správu pracovního vytížení](../sql-data-warehouse/resource-classes-for-workload-management.md) a [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) články vám poskytnou další informace.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Použijte zobrazení dynamických zpráv k monitorování a optimalizaci dotazů

Fondy SQL mají několik dmvs, které lze použít ke sledování provádění dotazů.  Následující článek monitorování vás provede podrobnými pokyny, jak zobrazit podrobnosti o spuštěném dotazu.  S rychlým vyhledáváním dotazů v těchto zobrazeních dynamických zpráv může pomoci použití možnosti LABEL v dotazech. Další podrobné informace naleznete v článcích uvedených v níže uvedeném seznamu:

- [Monitorování vaší úlohy pomocí DMV](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [Popisek](develop-label.md)
- [Možnost](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Další kroky

Běžné problémy a řešení najdete také v článku [Poradce při potížích.](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Pokud potřebujete informace, které nejsou uvedeny v tomto článku, použijte "Hledat dokumenty" na levé straně této stránky prohledat všechny dokumenty fondu SQL.  [Fórum fondu SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) je místem, kde můžete klást otázky ostatním uživatelům a skupině produktů fondu SQL.  

Toto fórum aktivně sledujeme, abychom zajistili, že vaši otázku zodpoví další uživatel nebo někdo z nás.  Pokud dáváte přednost klást otázky na přetečení zásobníku, máme také [Azure SQL fond zásobníku přetečení fórum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Pro požadavky na funkce použijte stránku [Feedback fondu Azure SQL.](https://feedback.azure.com/forums/307516-sql-data-warehouse)  Přidání vašich požadavků nebo dalších žádostí, které se zvyšují hlasování, nám pomáhá zaměřit se na nejžádanější funkce.
