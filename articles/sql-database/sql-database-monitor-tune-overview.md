---
title: Monitorování a optimalizace výkonu – Azure SQL Database | Dokumentace Microsoftu
description: Tipy pro ladění ve službě Azure SQL Database prostřednictvím hodnocení a zlepšování výkonu.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2fa43fcd48736a3d044deb07ed690af580c3b987
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416276"
---
# <a name="monitoring-and-performance-tuning"></a>Sledování a ladění výkonu

Azure SQL Database umožňuje snadno monitorovat využití, přidání nebo odebrání prostředků (procesoru, paměti, vstupně-výstupní operace), řešení potíží s potenciální problémy a najít doporučení, která může zlepšit výkon vaší databáze. Azure SQL Database obsahuje mnoho funkcí, které automaticky opravit problémy ve vašich databázích Pokud chcete, aby databáze přizpůsobit vaší úloze a automaticky optimalizace výkonu. Existují však některé vlastní problémy, které může být třeba řešit. Tento článek vysvětluje některé osvědčené postupy a nástroje, které můžete použít k řešení problémů s výkonem.

Existují dvě hlavní aktivity, které musíte udělat, aby se zajistilo, že databáze je běží bez problémů:
- [Monitorování výkonu databáze](#monitoring-database-performance) Pokud chcete mít jistotu, že prostředky přiřazené k vaší databázi můžete zpracování vašich úloh. Pokud zjistíte, že nenarážíte limity prostředků, je třeba identifikovat hlavní dotazy využívající prostředky a optimalizovat, nebo přidat další prostředky upgradujte úroveň služby.
- [Řešení problémů s výkonem](#troubleshoot-performance-issues) za účelem zjištění, proč některá potenciální problém se stalo, identifikovat hlavní příčinu problému a akce, která bude tento problém vyřešit.

## <a name="monitoring-database-performance"></a>Monitorování výkonu databáze

Monitorování výkonu databáze SQL v Azure začíná sledováním využití prostředků relativně ke zvolené úrovni výkonu databáze. Je třeba monitorovat následující prostředky:
 - **Využití procesoru** – je potřeba zkontrolovat, se dosažení 100 % využití procesoru v delší časové období. To může znamenat, že potřebujete upgrade, databáze nebo instance nebo identifikovat a ladění dotazů, které se používají nejvíce výpočetní výkon.
 - **Statistiky čekání** – je potřeba zkontrolovat, co důvod, proč vaše dotazy čekají na několik zdrojů informací. Queriesmig počkejte data načtena, nebo se uloží soubory databáze, čekání, protože je dosažení limitu některé prostředků atd.
 - **Využití vstupně-výstupních operací** – je potřeba zkontrolovat, se blíží limity vstupně-výstupních operací pro použité úložiště.
 - **Využití paměti** -množství paměti k dispozici pro databáze nebo instance je přímo úměrný počtu virtuálních jader a je potřeba zkontrolovat je dostatečně pro vaši úlohu. Životnost stránky je jeden z parametrů, které může obsahovat stránky rychle odeberou se z paměti.

Azure SQL Database **poskytuje avíza dokladů, které vám může pomoct vyřešit a opravte potenciální problémy s výkonem**. Můžete snadno identifikovat příležitosti, jak vylepšit a optimalizovat výkon dotazů beze změny zdroje kontrolou [doporučení pro optimalizaci výkonu](sql-database-advisor.md). Častým důvodem toho, že databáze je pomalá, jsou chybějící indexy a nedostatečně optimalizované dotazy. Můžete použít tato doporučení pro vyladění výkonu vašich úloh. Můžete také umožňují Azure SQL database a [automaticky optimalizace výkonu vašich dotazů](sql-database-automatic-tuning.md) použitím všechny určené doporučení a ověřuje, že pomáhají zvýšit výkon databáze.

Máte následující možnosti pro monitorování a řešení potíží s výkonem databáze:

- V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **databází SQL**, vyberte databázi a pak použít graf monitorování k vyhledání prostředků blíží jejich maximální. Ve výchozím nastavení se zobrazí spotřeba DTU. Klikněte na tlačítko **upravit** změnit časový rozsah a hodnoty.
- Nástroje, jako je SQL Server Management Studio poskytuje mnoho užitečných sestav, jako jsou [řídicí panel Výkon](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) tam, kde můžete monitorovat využití prostředků a identifikovat hlavní dotazy, využívající prostředky nebo [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)ve kterém můžete identifikovat dotazy s nižším výkonem.
- Použití [Query Performance Insight](sql-database-query-performance.md) [webu Azure portal](https://portal.azure.com) v identifikovat dotazy, které tráví nejvíce prostředků. Tato funkce je dostupná v izolované databáze a Elastických fondů pouze.
- Použití [služby SQL Database Advisor](sql-database-advisor-portal.md) Chcete-li zobrazit doporučení pro vytváření a vyřazení indexů, parametrické dotazy a řešení problémů schématu. Tato funkce je dostupná v izolované databáze a Elastických fondů pouze.
- Použití [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pro automatické monitorování výkonu vaší databáze. Po zjištění problému s výkonem se vygeneruje protokol diagnostiky s podrobnostmi a kořenové příčiny analýzy RCA () problému. Pokud je to možné, poskytuje doporučení pro zlepšení výkonu.
- [Povolení automatického ladění](sql-database-automatic-tuning-enable.md) a nechat Azure SQL database automaticky opravě zjištěných problémů s výkonem.
- Použití [zobrazení dynamické správy (DMV)](sql-database-monitoring-with-dmvs.md), [rozšířených událostí](sql-database-xevent-db-diff-from-svr.md)a [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) podrobný Poradce při potížích s problémy s výkonem.

> [!TIP]
> Zobrazit [Průvodce výkonem](sql-database-performance-guidance.md) najít techniky, které můžete použít ke zlepšení výkonu služby Azure SQL Database po identifikaci problému výkonu pomocí jednoho nebo více z výše uvedených metod.

## <a name="troubleshoot-performance-issues"></a>Řešení potíží s výkonem

K diagnostikování a vyřešení problémů s výkonem, začněte tím, že vysvětlení stavu každý aktivní dotaz a podmínek, které způsobují problémy s výkonem relevantní pro každý stav úlohy. Kvůli zvýšení výkonu Azure SQL Database se pochopit, že každý požadavek aktivní dotaz z vaší aplikace je buď ve stavu čekání nebo spuštěné. Při řešení problému s výkonem ve službě Azure SQL Database, mějte v následujícím grafu jako jste si tento článek k diagnostikování a vyřešení problémů s výkonem.

![Stavy úlohy](./media/sql-database-monitor-tune-overview/workload-states.png)

Pro úlohy s problémy s výkonem, tyto problémy s výkonem pravděpodobně z důvodu kolize procesoru ( **týkající se spouštění** podmínky) nebo jednotlivé dotazy čekají na libovolnou položku ( **související čekání** podmínku ).

Příčiny nebo **týkající se spouštění** problémů může být:
- **Problémy při kompilaci** -optimalizátoru dotazů SQL může vytvořit optimální plán kvůli zastaralé statistiky, nesprávné odhad počtu řádků, které budou zpracovány nebo odhadu požadované paměti. Pokud víte, že tento dotaz se provedl rychleji v minulosti nebo na jinou instanci (mi nebo SQL Server instance), proveďte vlastní provedení plány a porovnejte je chcete zobrazit, že jsou různé. Došlo k pokusu o použití pomocné parametry dotazu nebo znovu sestaví statistiku ani index k získání lepší plánu. Povolte Automatická oprava plánů ve službě Azure SQL Database automaticky tyto problémy zmírnit.
- **Problémů provádění** – Pokud je optimální plán dotazu, pak pravděpodobně dosahuje některá omezení prostředků v databázi, jako je propustnost zápisu protokolu nebo může použít defragmentovanou indexy, které by měl být znovu vytvořen. Velký počet souběžných dotazů, které zůstávají prostředky může být také příčiny problémů provádění. **Související čekání** problémy jsou ve většině případů související s problémy provádění, protože dotazy, které nejsou efektivní provádění pravděpodobně čekají na několik zdrojů informací.

Příčiny nebo **související čekání** problémů může být:
- **Blokování** – jeden dotaz můžou blokovat zámek pro některé objekty v databázi, zatímco jiné se pokoušíte získat přístup na stejné objekty. Můžete snadno identifikovat blokující dotazů pomocí zobrazení dynamické správy nebo nástroje pro monitorování.
- **Vstupně-výstupních operací problémy** -dotazů může být čekání na stránky k zápisu do souborů dat či protokolu. V tomto případě se zobrazí `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`, nebo `PAGEIOLATCH_*` počkejte statistické údaje ve zobrazení dynamické správy.
- **Problémy s databází TempDB** – Pokud používáte velké množství dočasné tabulky nebo uvidíte velké množství databází TempDB rozlití kapaliny v plánech dotazů může jít o problém s databází TempDB propustnost. 
- **Problémy související s pamětí** -nemusí mít dostatek paměti pro vaši úlohu tak, aby životnost vaše stránka může vyřaďte nebo dotazy získávají méně přidělení paměti, než je potřeba. V některých případech bude integrované inteligentní funkce v optimalizátoru dotazů opravit tyto problémy.
 
V následujících částech budou vysvětlena jak identifikovat a vyřešit některé z těchto problémů.

## <a name="running-related-performance-issues"></a>Problémy s výkonem souvisejících s spuštění

V rámci obecných pokynů Pokud vaše využití procesoru je trvale dosahovalo nebo přesahovalo 80 %, je nutné problém s výkonem souvisejících s spuštěná. Pokud máte potíže se související se spuštěná, může být způsobena nedostatkem prostředků procesoru nebo může mít relaci k jednomu z následujících podmínek:

- Moc velký počet spuštěných dotazů
- Příliš mnoho kompilaci dotazů
- Jeden nebo více provádění dotazů používají plán neoptimálním průběhem dotazu

Pokud zjistíte, že máte problém s výkonem souvisejících s běží, je vaším cílem je k jeho identifikaci přesné pomocí jedné nebo několika metod. Nejběžnější metody k identifikaci problémů souvisejících se spuštění jsou následující:

- Použití [webu Azure portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) k monitorování procenta využití procesoru.
- Pomocí následujících [zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md):

  - [Sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) vrátí spotřeby procesoru, vstupně-výstupní operace a paměti pro databázi Azure SQL Database. Jeden řádek existuje pro každých 15 sekund, i v případě, že neexistuje žádná aktivita v databázi. Historická data se udržují za jednu hodinu.
  - [Sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) vrátí data o využití a úložiště CPU pro službu Azure SQL Database. Data se shromažďují a agregují v pětiminutovém intervalu.

> [!IMPORTANT]
> Sada dotazů T-SQL pomocí těchto zobrazení dynamické správy k řešení potíží využití procesoru, naleznete v tématu [procesoru identifikovat problémy s výkonem](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a> Řešení potíží s dotazy s problémy plán spuštění závislé na parametru dotazu

Potíže s parametrem citlivé plán (PSP) odkazuje na scénáři, kde Optimalizátor dotazů generuje plán provádění dotazu, který je ideální jenom pro konkrétní parametr (nebo sadu hodnot) a bude plánů v mezipaměti optimální zadání hodnot parametrů používaných pro počet po sobě jdoucích spuštění. Optimální jiné plány může pak způsobit problémy s výkonem dotazů a celkovou propustnost snížení zatížení. Další informace o parametru pro analýzu sítě a zpracování dotazů, najdete v článku [Průvodce architekturou zpracování dotazu](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Existuje několik řešení zmírnit problémy, každá má přidružené kompromisy a nevýhody:

- Použití [znovu ZKOMPILOVAT](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pomocný parametr dotazu v každé spuštění dotazu. Toto řešení obchoduje čas kompilace a zvýšení využití procesoru pro lepší kvalitu plánu. Použití `RECOMPILE` možnost je často není možné pro úlohy vyžadující vysokou propustnost.
- Použití [možnosti (optimalizace pro...) ](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pomocný parametr dotazu přepsat hodnotu skutečný parametr s hodnotou typické parametr, který vytváří plán dostatečné pro většinu možností hodnotu parametru.   Tato možnost vyžaduje dostatečné povědomí o charakteristiky související plán a optimální parametr hodnoty.
- Použití [možnosti (optimalizace pro neznámý)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pomocný parametr dotazu přepsat hodnotu skutečný parametr výměnou za pomocí vektoru průměr hustoty. Dalším způsobem, jak to provést, je zachycení příchozí hodnoty parametrů do místní proměnné a pak pomocí místní proměnné v rámci predikáty, namísto použití parametrů samotných. Musí být průměrné hustota *dostatečné* pomocí této opravy.
- Zakažte parametr pro analýzu sítě zcela pomocí [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pomocný parametr dotazu.
- Použití [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pomocný parametr dotazu tak, aby se znovu zkompiluje v mezipaměti. Toto řešení předpokládá *dobré dostatečně* běžné plán už je v mezipaměti. Aby bylo možné snížit pravděpodobnost svého Dobrý plán vyřazuje a nový plán chybný kompilovanému mohou také zakázat automatické aktualizace statistiky.
- Nelze vynutit jiný plán explicitně pomocí [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pomocný parametr dotazu (explicitním zadáním, nastavením konkrétní plán pomocí Query Store nebo povolením [automatické ladění](sql-database-automatic-tuning.md).
- Nahraďte jedinou proceduru vnořenou sadu postupy, které lze každý použít na základě podmíněnou logiku a přidružené parametr hodnoty.
- Vytvoření dynamické řetězce provádění alternativy k definici statické metody.

Další informace o řešení těchto typů problémů najdete v tématu:

- To [můžu cítit parametr](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) blogový příspěvek
- To [dynamické srovnání plánu kvality pro parametrizované dotazy sql](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) blogový příspěvek
- To [techniky optimalizace dotazů SQL v SQL serveru: Parametr Sniffing](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) blogový příspěvek

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Řešení potíží s aktivitu kompilace z důvodu nesprávné Parametrizace

Když má dotaz literály, databázový stroj zvolí možnost automaticky parametrizovat příkaz nebo uživatel můžete explicitně parametrizovat kvůli snížení počtu zkompiluje. Vysoké využití procesoru může způsobit vysoký počet zkompiluje dotazu pomocí stejného vzoru, ale jiné hodnoty literálu. Podobně pokud jen částečně parametrizace dotazu, který se bude mít literály, databázový stroj parametrizovat ho dále.  Níže je příklad částečně parametrizovaného dotazu:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

V předchozím příkladu `t1.c1` trvá `@p1` ale `t2.c2` pokračuje trvat identifikátor GUID jako literál. V takovém případě pokud se změní hodnota `c2`, dotaz bude zacházeno jako jiný dotaz, a dojde k nové kompilace. Pokud chcete snížit kompilace v předchozím příkladu, řešením je také parametrizovat identifikátor GUID.

Následující dotaz zobrazí počet dotazů podle hodnota hash dotazu k určení, pokud dotaz nebo není správně parametrizované:

```sql
SELECT  TOP 10  
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
### <a name="factors-influencing-query-plan-changes"></a>Faktory ovlivňující změny plánu dotazu

Opětovná kompilace plán provádění dotazů může vést k plánu generování dotazu, který se liší od původně mezipaměti. Existují různé důvody, proč může automaticky překompilovány existující původní plán:
- Změny ve schématu odkazuje dotaz
- Změny dat k tabulkám, odkazuje dotaz 
- Změny v možnosti kontext dotazu 

Kompilované plán může vysunout z různých důvodů, včetně restartování instance mezipaměti, změny konfigurace, tlaku na paměť a explicitní žádosti o vymazání mezipaměti v oboru databáze. Kromě toho pomocí pomocného parametru RECOMPILE znamená, že nebudou zapisována do mezipaměti plánu.

Rekompilace (nebo nové kompilace po vyřazení mezipaměti) může stále dojít generování plán provádění identické dotazu z jedné původně pozorováno.  Pokud jsou však změny plánu ve srovnání s předchozí nebo původní plán, tady jsou nejčastěji používané vysvětlení pro proč změnit plán provádění dotazu:

- **Změnit návrh fyzické**. Například nové indexy vytvořit, je více optimální využití nového indexu, než datová struktura původně vybrali pro první verzi efektivněji krytí, které požadavky dotazu může být použita na nové kompilace, pokud se rozhodne optimalizátoru dotazů provádění dotazu.  Všechny fyzické změny odkazované objekty nemusí se nová možnost plánu v době kompilace.

- **Server prostředků rozdíly**. Ve scénáři, kde se liší jeden plán systému"A" a "systém B" – dostupnost prostředků, jako je počet dostupných procesorů mohou mít vliv na jaký plán získá vygenerována.  Pokud jeden systém má vyšší počet procesorů, například může být zvolen paralelním plánu. 

- **Různé statistické údaje**. Statistické údaje přidružené k odkazované objekty změnit nebo se významně liší od původního systému statistiky.  Pokud změníte statistiky a rekompilujte dochází, optimalizátoru dotazů použije statistiky od tohoto konkrétního bodu v čase. Revidované statistiky může mít distribuce výrazně odlišné dat a frekvencí, které nejsou písmena v původní kompilace.  Tyto změny se používají k odhadu kardinality odhadu (počet řádků očekávané výsledky pomocí stromu dotazu logický tok).  Změny odhadů mohutnost může vést nám k výběru různých fyzických operátory a související objednávky z operací.  I menší změny statistiky může způsobit plán provádění změny dotazu.

- **Databáze byl změněn úroveň nebo kardinalitu estimator verze kompatibility**.  Změny na úrovni kompatibility databáze můžete povolit nové strategie a funkce, které může mít za následek plán spuštění různých dotazů.  Zakázání nebo povolení příznak trasování 4199 nebo změnu stavu databáze s vymezeným oborem konfiguraci, kterou může také ovlivnit QUERY_OPTIMIZER_HOTFIXES dotazování nad rámec úroveň kompatibility databáze volby plánu spuštění v době kompilace.  Příznaky trasování 9481 (starší verze platnost CE) a 2312 (vynutit výchozí CE) jsou taky naplánovat by to mělo dopad. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Vyřešte problém dotazy nebo Poskytněte další zdroje informací

Jakmile identifikujete problém, můžete ladit problémových dotazů nebo upgradovat výpočetního prostředí nebo ke zvýšení kapacity databáze Azure SQL a chránit před požadavky na procesor úroveň služby. Informace o škálování prostředků pro izolované databáze, najdete v části [škálování izolované databáze prostředků ve službě Azure SQL Database](sql-database-single-database-scale.md) a škálování prostředků pro elastické fondy, najdete v části [škálování elastického fondu prostředků ve službě Azure SQL Databáze](sql-database-elastic-pool-scale.md). Informace o vertikálním navýšení managed instance najdete v tématu [omezení prostředků na úrovni Instance](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Určí, zda je spuštěna problémy z důvodu zvýšení zatížení svazku

Pro zvýšení využití procesoru může účet zvýšení provozu aplikace a úlohy, ale musí být pozor, abyste správně diagnostiku tohoto problému. V případě vysoké využití procesoru v odpovězte na tyto otázky k určení, zda skutečně zvýšení využití procesoru je z důvodu změny hlasitosti úlohy:

1. Jsou dotazy z aplikace příčinu vysoké využití procesoru problém?
2. Pro začátek využívání procesoru dotazy (které je možné identifikovat):

   - Určete, pokud došlo k několika plánům spuštění přidružené stejný dotaz. Pokud ano, zjistěte, proč.
   - Pro dotazy s stejný plán spuštění určete, pokud spuštění s úspěšností byly konzistentní a zvýšení počtu spuštění. Pokud ano, jsou problémy s výkonem pravděpodobně z důvodu zvýšení zatížení.

Stručně řečeno, pokud plán provádění dotazu neměli spouštět jinak, ale využití procesoru zvýšit spolu s počtem provádění, pravděpodobně nastane problém s úlohy související s zvýšení výkonu.

Není vždy jednoduché k závěru, že dojde ke změně svazku zatížení, který má zásadní vliv na využití procesoru problém.   Faktory ke zvážení: 

- **Změnit využití prostředků**

  Zvažte například scénář, ve kterém procesoru zvýšit na hodnotu 80 % delší dobu.  Využití procesoru samostatně neznamená svazku úlohy změnit.  Dotaz na plán provádění, že regrese a rozdělení změny dat můžete přispívat také další využití prostředků i v případě, že aplikace provádí stejné úlohy přesné.

- **Nový dotaz objevil**

   Aplikace může to podpořit nové sady dotazů v různých časech.

- **Počet požadavků, které zvýší nebo sníží**

   Tento scénář je Nejobvyklejšími měr pracovní vytížení. Počet dotazů nebude vždy odpovídat další využití prostředků. Tato metrika je však stále významné signál za předpokladu, že další faktory jsou beze změny.

## <a name="waiting-related-performance-issues"></a>Problémy s výkonem souvisejících s čekání

Jakmile jste si jisti, že nejsou připojena vysoké využití procesoru, problémy s výkonem souvisejících s spuštění, máte problém s výkonem souvisejících s čekání. Konkrétně vaše prostředky procesoru nejsou používány efektivně protože procesoru čeká na jiný prostředek. V takovém případě bude dalším krokem je identifikovat, co čeká prostředky procesoru. Nejběžnější metody pro zobrazení na začátek čekání kategorií typů:

- [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) poskytuje statistiky čekání každý dotaz v čase. Na Query Store čekání typy jsou sloučeny do kategorií čekání. Je k dispozici v mapování kategorií wait čekat typy [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [Sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) vrátí informace o všech čeká tak vlákna, která provedených během operace. Toto souhrnné zobrazení můžete použít k diagnostice problémů s výkonem s Azure SQL Database a také s konkrétní dotazy a dávek.
- [Sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) vrátí informace o frontě čekání úlohy, které čekají na některých prostředků.

Ve scénářích vysoké využití procesoru Query Store a statistiky čekání nemusí pokaždé odpovídat využití procesoru pro dvě z těchto důvodů:

- Využívání dotazy vysoké využití procesoru může být stále provádí a dotazy nebylo dokončeno.
- Vysoké využití procesoru náročné dotazy byly spuštěny při došlo k chybě převzetí služeb při selhání

Query Store a zobrazení dynamické správy sledování statistiky čekání pouze zobrazit výsledky pro dotazy na úspěšně dokončené a časového limitu a nezobrazují data pro aktuálně spouští příkazy (až do dokončení). Zobrazení dynamické správy [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) umožňuje sledovat právě spouští dotazy a přidružené pracovní doby.

Jak je znázorněno v předchozí tabulce, jsou nejčastěji používané čeká:

- Zámky (blokování)
- VSTUPNĚ-VÝSTUPNÍCH OPERACÍ
- `tempdb`-týkající se sporů
- Paměť udělení čeká

> [!IMPORTANT]
> Sada dotazů T-SQL pomocí těchto zobrazení dynamické správy k řešení těchto problémů souvisejících s čekání najdete tady:
>
> - [Identifikace problémů s výkonem vstupně-výstupních operací](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifikujte `tempdb` problémy s výkonem](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifikujte paměti udělení čeká](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox – čeká a zámky](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Zlepšení výkonu databáze s více prostředky

Nakonec pokud nebyly nalezeny žádné užitečné položky, které může zlepšit výkon vaší databáze, můžete změnit množství prostředků, které jsou k dispozici ve službě Azure SQL Database. Můžete přiřadit více prostředků tak, že změníte [jednotek DTU úrovně](sql-database-service-tiers-dtu.md) izolované databáze nebo zvýšení Edtu elastického fondu v každém okamžiku. Případně pokud používáte [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md), můžete změnit úroveň služby nebo zvýšit prostředky přidělené k vaší databázi.

1. Pro izolované databáze můžete [změna úrovní služeb](sql-database-single-database-scale.md) nebo [výpočetní prostředky](sql-database-single-database-scale.md) na vyžádání ke zlepšení výkonu databáze.
2. Pro více databází, zvažte použití [elastické fondy](sql-database-elastic-pool-guidance.md) automaticky škálovat prostředky.

## <a name="tune-and-refactor-application-or-database-code"></a>Ladění a Refaktorujte aplikace nebo kódu databáze

Můžete změnit kód aplikace více optimálně použít databázi, změňte indexy, vynutit plány nebo používání pomocné parametry ručně přizpůsobit databáze vaší úloze. Najdete některé pokyny a tipy pro ruční optimalizace a přepsáním kódu v [tématu pokyny k výkonu](sql-database-performance-guidance.md) článku.

## <a name="next-steps"></a>Další postup

- Povolení automatického ladění ve službě Azure SQL Database a nechat funkce automatického ladění plně spravovat vaše úlohy najdete v tématu [povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Chcete-li použít ruční ladění, můžete zkontrolovat [doporučení na webu Azure portal pro optimalizaci](sql-database-advisor-portal.md) a aplikovat ručně ty, které zlepšují výkon vašich dotazů.
- Změnit prostředky, které jsou k dispozici v databázi tak, že změníte [úrovně služby Azure SQL Database](sql-database-performance-guidance.md)
