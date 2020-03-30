---
title: Typy problémů s výkonem dotazů v Azure SQL Database
description: V tomto článku se dozvíte o typech problémů s výkonem dotazů v Azure SQL Database a také se dozvíte, jak identifikovat a vyřešit dotazy s těmito problémy.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: e155321c2727bf4ee871ef7be7b61b6a523ec1fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256130"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Zjistitelné typy kritických bodů výkonu dotazů v Azure SQL Database

Při pokusu o vyřešení kritického místa výkonu začněte určením, zda dochází k kritickému místu, když je dotaz ve spuštěném stavu nebo ve stavu čekání. V závislosti na tomto určení platí různá rozlišení. Pomocí následujícího diagramu můžete pochopit faktory, které mohou způsobit problém související s provozem nebo problém související s čekáním. Problémy a řešení týkající se jednotlivých typů problémů jsou popsány v tomto článku.

K detekci těchto typů kritických bodů výkonu můžete použít Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) nebo SQL Server [DMVs.](sql-database-monitoring-with-dmvs.md)

![Stavy pracovního vytížení](./media/sql-database-monitor-tune-overview/workload-states.png)

**Problémy související se spuštěním**: Problémy související se spuštěním obecně souvisejí s problémy kompilace, které vedou k neoptimálnímu plánu dotazů nebo problémům s prováděním souvisejícím s nedostatečnými nebo nadměrně používanými prostředky.
**Problémy související s čekáním**: Problémy související s čekáním obecně souvisejí s:

- Zámky (blokování)
- I/O
- Tvrzení týkající se využití tempdb
- Čekání grantu paměti

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Problémy s kompilací, které vedou k neoptimálnímu plánu dotazů

Neoptimální plán generovaný optimalizátorem dotazů SQL může být příčinou pomalého výkonu dotazu. Optimalizace dotazů SQL může vytvořit neoptimální plán z důvodu chybějící index, zastaralé statistiky, nesprávný odhad počtu řádků, které mají být zpracovány nebo nepřesný odhad požadované paměti. Pokud víte, že dotaz byl proveden rychleji v minulosti nebo na jiné instanci (buď spravované instance nebo SQL Server instance), porovnat skutečné spuštění plány a zjistěte, zda jsou jiné.

- Identifikujte všechny chybějící indexy pomocí jedné z těchto metod:

  - Používejte [inteligentní přehledy](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Poradce databáze](sql-database-advisor.md) pro jednoduché a sdružené databáze.
  - Dmvs. Tento příklad ukazuje dopad chybějící index, jak zjistit [chybějící indexy](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes) pomocí DMVs a dopad implementace chybějící index doporučení.
- Pokuste se použít [rady při psaní dotazu](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query), [aktualizovat statistiky](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)nebo [znovu sestavit indexy](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) získat lepší plán. Povolte [automatickou opravu plánu](sql-database-automatic-tuning.md) v Azure SQL Database, abyste tyto problémy automaticky zmírnili.

  Tento [příklad](sql-database-performance-guidance.md#query-tuning-and-hinting) ukazuje dopad neoptimálního plánu dotazů z důvodu parametrizovaného dotazu, jak zjistit tuto podmínku a jak vyřešit nápovědu k dotazu.

- Zkuste změnit úroveň kompatibility databáze a implementovat inteligentní zpracování dotazů. Optimalizace dotazů SQL může generovat jiný plán dotazů v závislosti na úrovni kompatibility databáze. Vyšší úrovně kompatibility poskytují [inteligentnější možnosti zpracování dotazů](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing).

  - Další informace o zpracování dotazů naleznete v [příručce Query Processing Architecture Guide](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide).
  - Chcete-li změnit úrovně kompatibility databáze a přečíst si další informace o rozdílech mezi úrovněmi kompatibility, [přečtěte si článek ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Další informace o odhadu mohutnosti naleznete v [tématu Odhad mohutnosti](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Řešení dotazů pomocí neoptimálních plánů spuštění dotazu

V následujících částech se popisuje, jak vyřešit dotazy s plánem provádění dotazů neoptimální.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>Dotazy, které mají problémy s plánem citlivým na parametry (PSP)

Problém plánu citlivého na parametry (PSP) nastane, když optimalizátor dotazů vygeneruje plán spuštění dotazu, který je optimální pouze pro určitou hodnotu parametru (nebo sadu hodnot) a plán uložený v mezipaměti pak není optimální pro hodnoty parametrů, které se používají v po sobě jdoucích Popravy. Plány, které nejsou optimální, pak mohou způsobit problémy s výkonem dotazu a snížit celkovou propustnost pracovního vytížení.

Další informace o sledování parametrů a zpracování dotazů naleznete v [průvodci architekturou zpracování dotazů](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Několik řešení může zmírnit problémy psp. Každé řešení má přidružené kompromisy a nevýhody:

- Při každém spuštění dotazu použijte nápovědu k [rekompilaci](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) dotazu. Toto řešení obchoduje kompilace čas a zvýšení CPU pro lepší kvalitu plánu. Tato `RECOMPILE` možnost často není možné pro úlohy, které vyžadují vysokou propustnost.
- Pomocí nápovědy dotazu [OPTION (OPTIMIZE FOR...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) přepište hodnotu skutečného parametru pomocí typické hodnoty parametru, která vytvoří plán, který je dostatečně dobrý pro většinu možností hodnoty parametru. Tato možnost vyžaduje dobré pochopení optimálních hodnot parametrů a souvisejících charakteristik plánu.
- Pomocí nápovědy k dotazu [OPTION (OPTIMALIZOVAT PRO NEZNÁMÉ)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) přepište skutečnou hodnotu parametru a místo toho použijte průměr vektoru hustoty. Můžete to také provést zachycením příchozích hodnot parametrů v místních proměnných a potom pomocí místních proměnných v rámci predikátů namísto použití samotných parametrů. Pro tuto opravu musí být průměrná hustota *dostatečně dobrá*.
- Zakažte parametr čichání zcela pomocí [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) nápovědy dotazu.
- Pomocí nápovědy k dotazu [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) zabraňte rekompilacím v mezipaměti. Toto řešení předpokládá, že dostatečně dobrý společný plán je již v mezipaměti. Můžete také zakázat automatické aktualizace statistiky, abyste snížili pravděpodobnost, že dobrý plán bude vystěhován a bude zkompilován nový špatný plán.
- Vynutit plán explicitně pomocí [nápovědy](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) dotazu USE PLAN přepsáním dotazu a přidáním nápovědy v textu dotazu. Nebo nastavte konkrétní plán pomocí úložiště dotazů nebo povolením [automatického ladění](sql-database-automatic-tuning.md).
- Nahraďte jeden postup vnořenou sadou procedur, které lze použít na základě podmíněné logiky a souvisejících hodnot parametrů.
- Vytvořte alternativy spuštění dynamického řetězce k definici statické procedury.

Další informace o řešení problémů se systémem PSP naleznete v těchto příspěvcích na blogu:

- [Cítím parametr](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. dynamické postupy SQL vs. procedury vs. kvalita plánu pro parametrizované dotazy](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Techniky optimalizace dotazů SQL na serveru SQL Server: Sledování parametrů](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Kompilace aktivity způsobené nesprávnou parametrizací

Pokud dotaz obsahuje literály, databázový stroj automaticky parametrizuje příkaz nebo uživatel explicitně parametrizuje příkaz, aby se snížil počet kompilací. Vysoký počet kompilací pro dotaz pomocí stejného vzoru, ale různé hodnoty literálu může mít za následek vysoké využití procesoru. Podobně pokud pouze částečně parametrizovat dotaz, který nadále mají literály, databázový stroj není parametrizovat dotaz dále.

Tady je příklad částečně parametrizovaného dotazu:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

V tomto `t1.c1` příkladu `@p1` `t2.c2` trvá , ale nadále brát GUID jako literál. V tomto případě pokud změníte `c2`hodnotu pro , dotaz je považován za jiný dotaz a dojde k nové kompilaci. Chcete-li snížit kompilace v tomto příkladu, by také parametrizovat GUID.

Následující dotaz zobrazuje počet dotazů podle hodnoty hash dotazu k určení, zda je dotaz správně parametrizován:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>Faktory, které ovlivňují změny plánu dotazu

Rekompilace plánu spuštění dotazu může mít za následek generovaný plán dotazů, který se liší od původního plánu uloženého v mezipaměti. Existující původní plán může být automaticky překompilován z různých důvodů:

- Změny ve schématu jsou odkazovány dotazem
- Změny dat v tabulkách jsou odkazovány dotazem
- Možnosti kontextu dotazu byly změněny.

Zkompilovaný plán může být vysunut z mezipaměti z různých důvodů, například:

- Instance se restartuje.
- Změny konfigurace s rozsahem databáze
- Přetížení paměti
- Explicitní požadavky na vymazání mezipaměti

Pokud použijete nápovědu recompile, plán nebude uložen do mezipaměti.

Rekompilace (nebo čerstvá kompilace po vyřazování mezipaměti) může stále vést ke generování plánu spuštění dotazu, který je shodný s originálem. Pokud se plán změní z předchozího nebo původního plánu, tato vysvětlení jsou pravděpodobná:

- **Změněný fyzický návrh**: Například nově vytvořené indexy efektivněji pokrývají požadavky dotazu. Nové indexy mohou být použity na nové kompilaci, pokud optimalizace dotazu rozhodne, že použití tohoto nového indexu je optimální než pomocí datové struktury, která byla původně vybrána pro první verzi spuštění dotazu. Jakékoli fyzické změny odkazovaných objektů může mít za následek novou volbu plánu v době kompilace.

- **Rozdíly mezi prostředky serveru**: Pokud se plán v jednom systému liší od plánu v jiném systému, může dostupnost prostředků, například počet dostupných procesorů, ovlivnit, který plán bude vygenerován. Například pokud jeden systém má více procesorů, paralelní plán může být vybrán.

- **Různé statistiky**: Statistiky spojené s odkazované objekty se mohly změnit nebo se mohou podstatně lišit od statistik původního systému. Pokud se statistika změní a dojde k rekompilaci, optimalizátor dotazů použije statistiky od změny. Distribuce a frekvence údajů revidovaných statistik se mohou lišit od rozdělení a četnosti původní kompilace. Tyto změny se používají k vytvoření odhady mohutnosti. (*Odhady mohutnosti* jsou počet řádků, u kterých se očekává, že budou protékat stromem logického dotazu.) Změny odhadů mohutnosti mohou vést k výběru různých fyzických operátorů a přidružených objednávek operací. I menší změny statistiky může mít za následek změnu plánu spuštění dotazu.

- **Změněná úroveň kompatibility databáze nebo verze odhadu mohutnosti**: Změny úrovně kompatibility databáze mohou povolit nové strategie a funkce, které mohou vést k jinému plánu spuštění dotazu. Kromě úrovně kompatibility databáze může zakázaný nebo povolený příznak trasování 4199 nebo změněný stav konfigurace s rozsahem databáze QUERY_OPTIMIZER_HOTFIXES ovlivnit také volby plánu spuštění dotazu v době kompilace. Trasovací příznaky 9481 (force legacy CE) a 2312 (force default CE) také ovlivňují plán.

## <a name="resource-limits-issues"></a>Problémy s omezeními prostředků

Pomalý výkon dotazu, který nesouvisí s neoptimálními plány dotazů a chybějící indexy obecně souvisejí s nedostatečnými nebo nadměrně používanými prostředky. Pokud je plán dotazů optimální, dotaz (a databáze) může být přístupů omezení prostředků pro databázi, elastický fond nebo spravované instance. Příkladem může být nadměrná propustnost zápisu protokolu pro úroveň služby.

- Zjišťování problémů s prostředky pomocí portálu Azure: Chcete-li zjistit, zda jsou problémem omezení prostředků, přečtěte si informace o [monitorování prostředků databáze SQL](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring). Pro jednotlivé databáze a elastické fondy, naleznete [v tématu doporučení výkonu Poradce pro databázi](sql-database-advisor.md) a [přehledy výkonu dotazů](sql-database-query-performance.md).
- Zjišťování limitů prostředků pomocí [inteligentních přehledů](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Zjišťování problémů se zdroji pomocí [dmvs](sql-database-monitoring-with-dmvs.md):

  - [Sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV vrátí procesor, vstupně-va a využití paměti pro databázi SQL. Jeden řádek existuje pro každý interval 15 sekund, i v případě, že neexistuje žádná aktivita v databázi. Historická data jsou udržována po dobu jedné hodiny.
  - [Sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV vrátí využití procesoru a data úložiště pro Azure SQL Database. Data jsou shromažďována a agregována v pětiminutových intervalech.
  - [Mnoho jednotlivých dotazů, které kumulativně spotřebovávají vysoký procesor](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

Pokud identifikujete problém jako nedostatečný prostředek, můžete upgradovat prostředky ke zvýšení kapacity databáze SQL absorbovat požadavky na procesor. Další informace najdete [v tématu Škálování prostředků jedné databáze v Azure SQL Database](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu v Azure SQL Database](sql-database-elastic-pool-scale.md). Informace o škálování spravované instance naleznete v tématu [Omezení prostředků úrovně služby](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Problémy s výkonem způsobené zvýšeným objemem pracovního vytížení

Zvýšení provozu aplikací a objemu pracovního vytížení může způsobit zvýšené využití procesoru. Ale musíte být opatrní, abyste správně diagnostikovali tento problém. Když se zobrazí problém s vysokým procesorem, odpovězte na tyto otázky a zjistěte, zda je zvýšení způsobeno změnami svazku pracovního vytížení:

- Jsou dotazy z aplikace příčinou problému s vysokým procesorem?
- Pro [nejlepší dotazy náročné na procesor, které můžete identifikovat](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past):

  - Bylo ke stejnému dotazu přidruženo více plánů spuštění? Pokud ano, proč?
  - Byly u dotazů se stejným plánem spuštění konzistentní časy spuštění? Zvýšil se počet poprav? Pokud ano, zvýšení pracovního vytížení pravděpodobně způsobuje problémy s výkonem.

Stručně řečeno, pokud plán spuštění dotazu neprovedl jinak, ale využití procesoru se zvýšilo spolu s počtem spuštění, problém s výkonem pravděpodobně souvisí se zvýšením pracovního vytížení.

Není vždy snadné identifikovat změnu svazku pracovního vytížení, která je hnací silou problému s procesorem. Zvažte tyto faktory:

- **Změněné využití prostředků**: Zvažte například scénář, kdy se využití procesoru zvýšilo na 80 procent po delší dobu. Samotné využití procesoru neznamená, že se změnil svazek pracovního vytížení. Regrese v plánu spuštění dotazu a změny v distribuci dat může také přispět k větší využití prostředků i v případě, že aplikace spustí stejné zatížení.

- **Vzhled nového dotazu**: Aplikace může řídit novou sadu dotazů v různých časech.

- **Zvýšení nebo snížení počtu požadavků**: Tento scénář je nejviditelnější míra pracovního vytížení. Počet dotazů ne vždy odpovídá většímu využití prostředků. Tato metrika je však stále významný signál, za předpokladu, že ostatní faktory jsou beze změny.

Pomocí inteligentních přehledů můžete zjišťovat [zvýšení pracovního vytížení](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) a [plánovat regrese](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Problémy související s čekáním

Jakmile jste odstranili neoptimální plán a *čekání související* problémy, které souvisejí s problémy s prováděním, problém s výkonem je obecně dotazy jsou pravděpodobně čeká na nějaký prostředek. Problémy související s čekáním mohou být způsobeny:

- **Blokování**:

  Jeden dotaz může obsahovat zámek na objekty v databázi, zatímco jiní se pokusí o přístup ke stejným objektům. Blokování dotazů můžete identifikovat pomocí [dmv s.](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries) nebo [inteligentních přehledů](sql-database-intelligent-insights-troubleshoot-performance.md#locking).
- **Problémy s vi**

  Dotazy mohou čekat na zapisované stránky do datových nebo protokolových souborů. V takovém případě `INSTANCE_LOG_RATE_GOVERNOR`zkontrolujte statistiky , `WRITE_LOG`, nebo `PAGEIOLATCH_*` počkejte v DMV. Viz použití DMVs k identifikaci problémů s [výkonem vi](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues).
- **TempDB problémy**

  Pokud zatížení používá dočasné tabulky nebo jsou tempdb rozlití v plánech, dotazy mohou mít problém s propustnost tempdb. Viz použití DMVs k [identifikaci TempDB problémy](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Problémy související s pamětí**

  Pokud zatížení nemá dostatek paměti, může dojít k poklesu životnosti stránky nebo může dojít k získání méně paměti, než je potřeba. V některých případech integrované inteligence v Optimalizaci dotazů opraví problémy související s pamětí. Viz použití dmvs k [identifikaci problémů paměti udělení](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Metody pro zobrazení kategorií nejvyššího čekání

Tyto metody se běžně používají k zobrazení nejvyšší kategorie typů čekání:

- Použití inteligentních přehledů k identifikaci dotazů s snížením výkonu v důsledku [zvýšeného čekání](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Pomocí [úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) vyhledejte statistiky čekání pro každý dotaz v průběhu času. V úložišti dotazů jsou typy čekání sloučeny do kategorií čekání. Můžete najít mapování čekací chod kategorií čekat typy v [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Pomocí [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) vrátit informace o všech čeká našel podprocesů, které byly provedeny během operace dotazu. Toto agregované zobrazení můžete použít k diagnostice problémů s výkonem s Azure SQL Database a také s konkrétními dotazy a listy. Dotazy mohou čekat na prostředky, čekání fronty nebo externí čekání.
- Pomocí [souboru sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) vraťte informace o frontě úkolů, které čekají na některý zdroj.

Ve scénářích s vysokým procesorem nemusí statistiky úložiště dotazů a čekání odrážet využití procesoru, pokud:

- Dotazy s vysokou spotřebou procesoru jsou stále spuštěny.
- Dotazy s vysokou spotřebou procesoru byly spuštěny, když došlo k převzetí služeb při selhání.

DMVs, které sledují statistiky úložiště dotazů a čekání zobrazit výsledky pouze úspěšně dokončena a časový limit dotazy. Nezobrazují data pro aktuálně spuštěné příkazy, dokud příkazy neskončí. Pomocí zobrazení dynamické správy [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) můžete sledovat aktuálně spuštěné dotazy a přidružený pracovní čas.

> [!TIP]
> Další nástroje:
>
> - [TigerToolbox čeká a západky](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Další kroky

[Přehled monitorování a ladění databáze SQL](sql-database-monitor-tune-overview.md)