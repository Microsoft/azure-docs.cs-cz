---
title: Typy potíží s výkonem dotazů v Azure SQL Database
description: V tomto článku se dozvíte o typech potíží s výkonem dotazů v Azure SQL Database a také o tom, jak identifikovat a vyřešit dotazy s těmito problémy.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 039332a8728e5d7e5b605f51f4bb53e6dcbb6381
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109165"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Zjistitelné typy kritických bodů výkonu dotazů ve službě Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Při pokusu o vyřešení kritického bodu výkonu Začněte tím, že určíte, zda k tomuto kritickému problému dochází, když je dotaz ve spuštěném nebo čekajícím stavu. V závislosti na tomto určení se uplatní různá rozlišení. Pomocí následujícího diagramu můžete porozumět faktorům, které mohou způsobit, že došlo k potížím souvisejícím se systémem nebo s čekáním na problém. Problémy a řešení týkající se jednotlivých typů problémů jsou popsány v tomto článku.

K detekci těchto typů problémů s výkonem můžete použít Azure SQL Database [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) nebo SQL Server [zobrazení dynamické správy](database/monitoring-with-dmvs.md) .

![Stavy úlohy](./media/identify-query-performance-issues/workload-states.png)

**Problémy související se spouštěním**: spuštěné související problémy se obecně vztahují k problémům s kompilací, které mají za následek nedostatečné nebo nevyužité prostředky, a to s tím související problémy při kompilaci.
**Problémy související s čekáním**: čekání související problémy se obecně týkají:

- Zámky (blokování)
- I/O
- Spor týkající se využití databáze TempDB
- Přidělení paměti čeká

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Problémy s kompilací, které mají za následek optimální plán dotazů

Neoptimální plán generovaný optimalizátorem dotazů SQL může být příčinou pomalého výkonu dotazů. Optimalizátor dotazů SQL může vydávat optimální plán kvůli chybějícímu indexu, zastaralým statistikám, nesprávnému odhadu počtu zpracovaných řádků nebo nepřesnému odhadu požadované paměti. Pokud víte, že dotaz byl proveden rychleji v minulosti nebo na jiné instanci, porovnejte skutečné plány spuštění a zjistěte, zda se liší.

- Identifikujte chybějící indexy pomocí jedné z těchto metod:

  - Použijte [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Database Advisor](database/database-advisor-implement-performance-recommendations.md) pro databáze s jednou a ve fondu.
  - Zobrazení dynamické správy. Tento příklad ukazuje vliv chybějícího indexu, jak detekovat [chybějící indexy](database/performance-guidance.md#identifying-and-adding-missing-indexes) pomocí zobrazení dynamické správy a dopad implementace chybějícího doporučení indexu.
- Zkuste použít [pomocný parametr dotazu](/sql/t-sql/queries/hints-transact-sql-query), [Aktualizovat statistiku](/sql/t-sql/statements/update-statistics-transact-sql)nebo [znovu sestavit indexy](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) , abyste získali lepší plán. Povolte [automatickou opravu plánu](../azure-sql/database/automatic-tuning-overview.md) v Azure SQL Database, aby se tyto problémy automaticky zmírnily.

  Tento [příklad](database/performance-guidance.md#query-tuning-and-hinting) ukazuje dopad předoptimálního plánu dotazu z důvodu parametrizovaného dotazu, způsobu detekce této podmínky a způsobu, jak použít pomocný parametr dotazu k vyřešení.

- Zkuste změnit úroveň kompatibility databáze a implementovat Inteligentní zpracování dotazů. Optimalizátor dotazů SQL může vygenerovat jiný plán dotazu v závislosti na úrovni kompatibility vaší databáze. Vyšší úrovně kompatibility poskytují další [Možnosti inteligentního zpracování dotazů](/sql/relational-databases/performance/intelligent-query-processing).

  - Další informace o zpracování dotazů najdete v tématu [Průvodce architekturou zpracování dotazů](/sql/relational-databases/query-processing-architecture-guide).
  - Postup změny úrovní kompatibility databáze a další informace o rozdílech mezi úrovněmi kompatibility najdete v tématu [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Další informace o odhadu mohutnosti najdete v tématu [odhad mohutnosti](/sql/relational-databases/performance/cardinality-estimation-sql-server) .

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Řešení dotazů s využitím optimálních plánů spouštění dotazů

Následující části popisují, jak řešit dotazy s nejoptimálním plánem spouštění dotazů.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> Dotazy, které mají problémy s parametrem citlivostní plán (PSP)

K problému s parametrem s citlivým plánem (PSP) dochází, když Optimalizátor dotazů generuje plán spouštění dotazů, který je optimální pouze pro konkrétní hodnotu parametru (nebo sadu hodnot) a plán uložený v mezipaměti není optimální pro hodnoty parametrů, které se používají při následném spuštění. Plány, které nejsou optimální, můžou následně způsobit problémy s výkonem dotazů a snižovat celkovou propustnost úloh.

Další informace o sledování parametrů a zpracování dotazů naleznete v [příručce k architektuře zpracování dotazů](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Několik alternativních řešení může zmírnit problémy PSP. Každé řešení má Spojené kompromisy a nevýhody:

- Použijte [znovu zkompilování](/sql/t-sql/queries/hints-transact-sql-query) nápovědy pro dotaz při každém spuštění dotazu. Toto řešení usnadňuje dobu kompilace a zvýšenou kapacitu procesoru pro lepší kvalitu plánu. `RECOMPILE`Možnost není často dostupná pro úlohy, které vyžadují vysokou propustnost.
- Použijte pomocný parametr dotazu [Option (optimize for.](/sql/t-sql/queries/hints-transact-sql-query) ..) pro přepsání skutečné hodnoty parametru s typickou hodnotou parametru, která vytvoří plán, který je dostatečně dobrý pro většinu možností hodnoty parametru. Tato možnost vyžaduje dobrou představu o optimálních hodnotách parametrů a přidružených vlastnostech plánu.
- Použijte pomocný parametr dotazu [Option (optimalizovat pro neznámý)](/sql/t-sql/queries/hints-transact-sql-query) pro přepsání skutečné hodnoty parametru a místo toho použijte průměr vektoru hustoty. To lze provést také zachycením hodnot příchozích parametrů v místních proměnných a následným použitím místních proměnných v predikátech namísto použití samotných parametrů. Pro tuto opravu musí být průměrná hustota *dostatečně dobrá*.
- Pomocí pomocného parametru dotazu [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query) zakažte pouze sledování parametrů.
- Použijte pomocný parametr dotazu [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query) k zabránění rekompilacím v mezipaměti. V tomto alternativním řešení se předpokládá, že v mezipaměti již existuje dobrý společný plán. Můžete také zakázat automatické aktualizace statistiky, aby se snížila pravděpodobnost, že bude dobrý plán vyřazený a bude zkompilován nový špatný plán.
- Vynutit plán explicitním použitím pomocného parametru dotazu [použít plán](/sql/t-sql/queries/hints-transact-sql-query) tak, že přepíšete dotaz a přidáte pomocný parametr do textu dotazu. Nebo nastavte konkrétní plán pomocí úložiště dotazů nebo povolením [automatického ladění](../azure-sql/database/automatic-tuning-overview.md).
- Nahraďte jeden postup vnořenou sadou procedur, které mohou být použity na základě podmíněné logiky a přidružených hodnot parametrů.
- Vytváření alternativ dynamického provádění řetězce pro definici statické procedury.

Další informace o řešení problémů s PSP najdete v těchto blogových příspěvcích:

- [Mám v pachu parametr](/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. dynamické SQL vs. postupy vs. plánování kvality pro parametrizované dotazy](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)
- [Techniky optimalizace dotazů SQL v SQL Server: sledování parametrů](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Aktivita kompilace způsobila nesprávné Parametrizace

Pokud má dotaz literály, buď databázový stroj automaticky parameterizes příkaz, nebo uživatel výslovně parameterizes příkaz pro snížení počtu kompilací. Vysoký počet kompilací pro dotaz používající stejný vzor, ale různé hodnoty literálu může mít za následek vysoké využití procesoru. Podobně platí, že pokud pouze částečně parametrizovat dotaz, který nadále má literály, databázový stroj nebude dotaz dále parametrizovat.

Tady je příklad částečně parametrizovaného dotazu:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F';
```

V tomto příkladu `t1.c1` přijímá `@p1` , ale nadále přebírá `t2.c2` GUID jako literál. V takovém případě, pokud změníte hodnotu pro `c2` , dotaz je považován za jiný dotaz a dojde k nové kompilaci. Chcete-li snížit kompilace v tomto příkladu, můžete také parametrizovat identifikátor GUID.

Následující dotaz znázorňuje počet dotazů podle hodnoty hash dotazu k určení, jestli je dotaz správně parametrizovaný:

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
ORDER BY count (distinct p.query_id) DESC;
```

### <a name="factors-that-affect-query-plan-changes"></a>Faktory ovlivňující změny plánu dotazů

Opětovná kompilace plánu spuštění dotazu může mít za následek vygenerovaný plán dotazu, který se liší od původního plánu v mezipaměti. Existující původní plán se může automaticky znovu zkompilovat z různých důvodů:

- Na změny ve schématu odkazuje dotaz
- Dotaz na změny dat v tabulkách odkazuje
- Možnosti kontextu dotazu se změnily.

Kompilovaný plán může být vysunut z mezipaměti z různých důvodů, například:

- Restartování instance
- Změny konfigurace v rozsahu databáze
- Přetížení paměti
- Explicitní požadavky na vymazání mezipaměti

Použijete-li pomocný parametr RECOMPILE, plán nebude uložen do mezipaměti.

Opětovná kompilace (nebo nová kompilace po vyřazení mezipaměti) může stále vést k vytvoření plánu provádění dotazů, který je totožný s původní. Při změně plánu z předchozího nebo původního plánu jsou tato vysvětlení pravděpodobná:

- **Změna fyzického návrhu**: například nově vytvořené indexy efektivněji pokrývají požadavky dotazu. Nové indexy lze použít pro novou kompilaci, pokud se nástroj Optimalizátor dotazů rozhodne, že použití tohoto nového indexu je více optimální než použití struktury dat, která byla původně vybrána pro první verzi provedení dotazu. Všechny fyzické změny v odkazovaných objektech mohou mít za následek novou volbu plánu v době kompilace.

- **Rozdíly mezi prostředky serveru**: Pokud se plán v jednom systému liší od plánu v jiném systému, může být dostupnost prostředků, například počet dostupných procesorů, ovlivněna tím, který plán se vygeneruje. Pokud má například jeden systém více procesorů, může být zvolen paralelní plán. Další informace o paralelismuách v Azure SQL Database najdete v tématu [Konfigurace maximálního stupně paralelismu (MAXDOP) v Azure SQL Database](database/configure-max-degree-of-parallelism.md).

- **Různé statistiky**: statistiky spojené s odkazovanými objekty se možná změnily nebo můžou být v nepodstatném rozdílu od statistik původních systémů. Pokud se změní Statistika a provede se opětovná kompilace, používá Optimalizátor dotazů statistiku, která začíná od okamžiku jejich změny. Změny distribuce a četnosti dat revidované statistiky se mohou lišit od původní kompilace. Tyto změny slouží k vytvoření odhadů mohutnosti. (*Odhady mohutnosti* jsou počet řádků, které se mají přesměrovat do logického stromu dotazů.) Změny odhadů mohutnosti můžou vést k volbě různých fyzických operátorů a přidružených objednávek operací. I drobné změny statistik můžou mít za následek změnu plánu spuštění dotazu.

- **Změnila se úroveň kompatibility databáze nebo verze Estimator**: změny úrovně kompatibility databáze mohou umožňovat nové strategie a funkce, které mohou mít za následek jiný plán spouštění dotazů. Kromě úrovně kompatibility databáze může mít zakázaný nebo povolený příznak trasování 4199 nebo změněný stav QUERY_OPTIMIZER_HOTFIXES konfigurace s rozsahem databáze také v době kompilace vliv výběru plánu spouštění dotazů. Tento plán ovlivňují také příznaky trasování 9481 (vynutit starší verze CE) a 2312 (vynutit výchozí CE).

## <a name="resource-limits-issues"></a>Problémy s omezením prostředků

Pomalé dotazování, které nesouvisí s podoptimálními plány dotazů a chybějící indexy, obecně souvisejí s nedostatečnými nebo nepoužitými prostředky. Pokud je plán dotazu optimální, dotaz (a databáze) se může považovat za omezení prostředků databáze, elastického fondu nebo spravované instance. Příkladem může být nadměrné propustnost zápisu protokolu pro úroveň služby.

- Zjištění potíží s prostředky pomocí Azure Portal: Chcete-li zjistit, zda se jedná o omezení prostředků, přečtěte si téma [monitorování prostředků SQL Database](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). Informace o izolovaných databázích a elastických fondech najdete v tématu [Database Advisor doporučení k výkonu](database/database-advisor-implement-performance-recommendations.md) a [dotazování na výkon](database/query-performance-insight-use.md).
- Zjišťování omezení prostředků pomocí [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Zjištění potíží s prostředky pomocí [zobrazení dynamické správy](database/monitoring-with-dmvs.md):

  - [Sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV vrací pro databázi spotřebu CPU, vstupně-výstupních operací a paměti. U každého intervalu 15 sekund existuje jeden řádek, a to i v případě, že databáze neobsahuje žádné aktivity. Historická data se uchovávají po dobu jedné hodiny.
  - [Sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV vrátí využití CPU a data úložiště pro Azure SQL Database. Data se shromažďují a agregují v intervalech po pěti minutách.
  - [Mnoho jednotlivých dotazů, které kumulativně využívají vysoký procesor](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

Pokud problém identifikujete jako nedostatečný prostředek, můžete upgradovat prostředky a zvýšit tak kapacitu vaší databáze, aby se zvýšila nároky na procesor. Další informace najdete v tématu [škálování jednotlivých prostředků databáze ve Azure SQL Database](database/single-database-scale.md) a [škálování prostředků elastického fondu v Azure SQL Database](database/elastic-pool-scale.md). Informace o škálování spravované instance najdete v tématu [omezení prostředků na úrovni služby](managed-instance/resource-limits.md#service-tier-characteristics) .

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Problémy s výkonem způsobené větším objemem úloh

Zvýšení provozu aplikací a objemu úloh může způsobit zvýšené využití CPU. Pro správné diagnostiku tohoto problému je ale nutné být opatrní. Když se zobrazí problém s vysokým využitím procesoru, odpovězte na tyto otázky, abyste zjistili, jestli se zvýšení způsobilo změnami svazku zatížení:

- Jsou dotazy z aplikace příčinou potíží s vysokým využitím procesoru?
- Pro [Nejčastější dotazy náročné na procesor, které můžete identifikovat](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past):

  - Bylo k jednomu dotazu přidruženo více plánů spuštění? Pokud ano, proč?
  - U dotazů se stejným plánem spuštění byly časy spuštění konzistentní? Zvýšil se počet spuštění? Pokud ano, zvýšení zatížení bude nejspíš způsobovat problémy s výkonem.

Pokud se plán spouštění dotazů nespustí jinak, ale využití procesoru se zvýšilo společně s počtem spuštění, je problém s výkonem pravděpodobným zvýšením zatížení.

Nemusíte vždycky snadno identifikovat změnu objemu úloh, která je příčinou potíží procesoru. Vezměte v úvahu tyto faktory:

- **Změnilo se využití prostředků**: můžete například zvážit situaci, kdy se využití CPU v delší době zvýšilo na 80 procent. Samotný využití procesoru neznamená, že se změnil objem úloh. Regrese v plánu provádění dotazů a změny v distribuci dat můžou přispět i k využívání prostředků i v případě, že aplikace provádí stejnou úlohu.

- **Vzhled nového dotazu**: aplikace může v různých časech zařídit novou sadu dotazů.

- **Zvýšení nebo snížení počtu požadavků**: Tento scénář je nejzjevnější míra zatížení. Počet dotazů nemusí vždy odpovídat více využití prostředků. Tato metrika je však stále významným signálem za předpokladu, že jiné faktory nejsou změněny.

Pomocí Intelligent Insights můžete zjistit [zvýšení zatížení](database/intelligent-insights-troubleshoot-performance.md#workload-increase) a [naplánovat regrese](database/intelligent-insights-troubleshoot-performance.md#plan-regression).

- **Paralelismus**: nadměrný paralelismus může zhoršit výkon ostatních souběžných úloh omezují dalších dotazů na prostředky procesoru a pracovního vlákna. Další informace o paralelismuách v Azure SQL Database najdete v tématu [Konfigurace maximálního stupně paralelismu (MAXDOP) v Azure SQL Database](database/configure-max-degree-of-parallelism.md).

## <a name="waiting-related-problems"></a>Problémy související s čekáním

Po zrušení neoptimálního plánu a problémů *souvisejících* s výkonem, které souvisejí s problémy se spouštěním, je problém s výkonem většinou u dotazů čekání na určitý prostředek. Problémy související s čekáním můžou způsobovat tyto příčiny:

- **Blokování**:

  Jeden dotaz může obsahovat zámek objektů v databázi, zatímco se jiní pokusí o přístup ke stejným objektům. Blokování dotazů můžete identifikovat pomocí [zobrazení dynamické správy](database/monitoring-with-dmvs.md#monitoring-blocked-queries) nebo [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#locking). Další informace najdete v tématu [pochopení a řešení problémů s blokováním Azure SQL](database/understand-resolve-blocking.md).
- **Vstupně-výstupní problémy**

  Dotazy můžou čekat na zápis stránek do dat nebo souborů protokolu. V takovém případě se podívejte `INSTANCE_LOG_RATE_GOVERNOR` na `WRITE_LOG` `PAGEIOLATCH_*` statistiku, nebo v DMV. V tématu Použití zobrazení dynamické správy k [identifikaci potíží s výkonem v/](database/monitoring-with-dmvs.md#identify-io-performance-issues)v.
- **Problémy s databází TempDB**

  Pokud úloha používá dočasné tabulky nebo jsou v plánech přechody do TempDB, dotazy mohou mít problémy s propustností databáze TempDB. Informace najdete v tématu Použití zobrazení dynamické správy k [problémům s databází tempdb](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Problémy související s pamětí**

  Pokud úloha nemá dostatek paměti, může dojít k poklesu životnosti stránky očekávané a dotazy můžou dostat méně paměti, než potřebují. V některých případech integrované inteligentní funkce v nástroji pro optimalizaci dotazů vyřeší problémy související s pamětí. V tématu Použití zobrazení dynamické správy [Identifikujte problémy s přidělením paměti](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Metody pro zobrazení hlavních kategorií čekání

Tyto metody se běžně používají k zobrazení hlavních kategorií typů čekání:

- Použití Intelligent Insights k identifikaci dotazů se snížením výkonu kvůli [nárůstu počtu čekání](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Pomocí [úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) můžete najít statistiku čekání pro každý dotaz v průběhu času. V úložišti dotazů jsou typy čekání kombinovány do kategorií čekání. Můžete najít mapování kategorií čekání na typy čekání v [Sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Pomocí [Sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) můžete vracet informace o všech čekáních zjištěných vlákny během operace dotazu. Pomocí tohoto agregovaného zobrazení můžete diagnostikovat problémy s výkonem Azure SQL Database a také s konkrétními dotazy a dávkami. Dotazy můžou čekat na prostředky, čekání nebo externí čekání.
- Pomocí [Sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) můžete vracet informace o frontě úloh, které čekají na určitý prostředek.

V případě scénářů s vysokým využitím procesoru se v úložišti dotazů a v statistikách čekání nemusí projevit využití CPU, pokud:

- Vysoce náročné dotazy využívající procesor jsou pořád spuštěné.
- Dotazy s vysokým využitím procesoru byly spuštěny, když došlo k převzetí služeb při selhání.

Zobrazení dynamické správy, které sledují úložiště dotazů a statistiky čekání zobrazují výsledky pouze pro úspěšně dokončené a časované dotazy. Nezobrazují data pro aktuálně zpracovávané příkazy, dokud se nedokončí příkazy. Pomocí [Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) zobrazení dynamické správy můžete sledovat aktuálně zpracovávané dotazy a přidruženou dobu pracovního procesu.

> [!TIP]
> Další nástroje:
>
> - [TigerToolbox čekání a zámky](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="see-also"></a>Viz také

* [Konfigurace maximálního stupně paralelismu (MAXDOP) v Azure SQL Database](database/configure-max-degree-of-parallelism.md)
* [Pochopení a řešení potíží s blokováním Azure SQL Database v Azure SQL Database](database/understand-resolve-blocking.md)

## <a name="next-steps"></a>Další kroky

* [Přehled monitorování a ladění SQL Database](database/monitor-tune-overview.md)
