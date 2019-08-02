---
title: Monitorování ladění výkonu & – Azure SQL Database | Microsoft Docs
description: Tipy pro ladění výkonu v Azure SQL Database prostřednictvím vyhodnocení a vylepšení.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: c52b41c4e6d0618b4df9b2aed985bbd22d89f419
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567205"
---
# <a name="monitoring-and-performance-tuning"></a>Sledování a ladění výkonu

Azure SQL Database poskytuje nástroje a metody pro snadné monitorování využití, přidávání nebo odebírání prostředků (CPU, paměť, I/O), řešení potíží s potenciálními problémy a hledání doporučení, která můžou zlepšit výkon databáze. Azure SQL Database má mnoho funkcí, které mohou automaticky opravovat problémy v databázích, které umožní, aby se databáze přizpůsobila k zatížení a automaticky optimalizoval výkon. Existují však některé vlastní problémy, které mohou vyžadovat řešení potíží. Tento článek popisuje některé osvědčené postupy a nástroje, které lze použít k řešení problémů s výkonem.

Existují dvě hlavní aktivity, které by měly být provedeny, aby bylo zajištěno, že databáze běží bez problémů:
- [Monitorujte výkon databáze](#monitoring-database-performance) a ujistěte se, že prostředky přiřazené k databázi mohou zpracovat úlohu. Pokud zjistíte, že databáze je na dosahu omezení prostředků, je třeba identifikovat a optimalizovat nejlepší dotazy na prostředky, které by měly být přidány, a to upgradem úrovně služby.
- [Vyřešte problémy s výkonem](#troubleshoot-performance-issues) , abyste mohli zjistit, proč došlo k nějakému případnému problému, identifikovat hlavní příčinu problému a provést akci, která tento problém vyřeší.

## <a name="monitoring-database-performance"></a>Monitorování výkonu databáze

Monitorování výkonu databáze SQL v Azure začíná sledováním využití prostředků relativně ke zvolené úrovni výkonu databáze. Následující prostředky by se měly monitorovat pro stejné:
 - **Využití CPU** – ověřte, jestli databáze po delší dobu dosáhla 100% využití procesoru. To může znamenat, že se databáze nebo instance musí upgradovat na vyšší úroveň služby nebo že by se měly identifikovat a vyladit dotazy využívající většinu výpočetního výkonu.
 - **Čekací Statistika** – ověřte, proč se dotazy čekají na některé prostředky. Dotazy čekají, až se data načtou nebo uloží do souborů databáze. Počkejte, protože se dosáhne nějakého limitu prostředků atd.
 - **Použití v/** v – ověřte, jestli se databáze blíží omezení v/v podkladového úložiště.
 - **Využití paměti** – velikost paměti, která je k dispozici pro databázi nebo instanci, je úměrná počtu virtuální jádra a kontrole, jestli je to pro úlohu dostačující. Životnost stránky očekávané je jedním z parametrů, které mohou určit, jak rychle se stránky odstraňují z paměti.

Služba Azure SQL Database **zahrnuje nástroje a prostředky, které vám pomohou vyřešit problémy s výkonem a opravit potenciální problémy s výkonem**. Příležitosti lze snadno identifikovat a optimalizovat výkon dotazů bez změny prostředků, a to kontrolou [doporučení pro ladění výkonu](sql-database-advisor.md). Častým důvodem toho, že databáze je pomalá, jsou chybějící indexy a nedostatečně optimalizované dotazy. Tato doporučení pro ladění můžete použít ke zvýšení výkonu úloh. Díky použití všech identifikovaných doporučení můžeme také službě Azure SQL Database povolit [automatickou optimalizaci výkonu dotazů](sql-database-automatic-tuning.md) , a to díky tomu, že se zlepší výkon databáze.

K dispozici jsou následující možnosti pro monitorování a řešení potíží s výkonem databáze:

- V [Azure Portal](https://portal.azure.com)klikněte na **databáze SQL**, vyberte databázi a potom pomocí grafu monitorování vyhledejte prostředky, které se blíží jejich maximálnímu využití. Ve výchozím nastavení se zobrazuje spotřeba DTU. Kliknutím na **Upravit** změníte časový rozsah a zobrazené hodnoty.
- Nástroje, jako je například SQL Server Management Studio, poskytují mnoho užitečných sestav, jako je [řídicí panel výkonu](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) pro monitorování využití prostředků a identifikaci nejčastějších dotazů, které jsou náročné na prostředky, nebo [úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) pro identifikaci dotazů. předepsané.
- Pomocí [Query Performance Insight](sql-database-query-performance.md) v [Azure Portal](https://portal.azure.com) Identifikujte dotazy, které stráví nejvíc prostředků. Tato funkce je k dispozici pouze v Izolovaná databáze a elastických fondech.
- Pomocí [SQL Database Advisor](sql-database-advisor-portal.md) můžete zobrazit doporučení pro vytváření a odstraňování indexů, dotazů Parametrizace a řešení problémů se schématem. Tato funkce je k dispozici pouze v Izolovaná databáze a elastických fondech.
- Pro automatické sledování výkonu databáze použijte [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) . Jakmile se zjistí problém s výkonem, vygeneruje se diagnostický protokol s podrobnostmi a analýzou hlavní příčiny (RCA) problému. Doporučení pro zlepšení výkonu je k dispozici, pokud je to možné.
- [Povolte automatické ladění](sql-database-automatic-tuning-enable.md) a umožněte službě Azure SQL Database automaticky opravovat zjištěné problémy s výkonem.
- Pomocí [zobrazení dynamické správy (zobrazení dynamické správy)](sql-database-monitoring-with-dmvs.md), [rozšířených událostí](sql-database-xevent-db-diff-from-svr.md)a [úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) podrobnější řešení potíží s výkonem.

> [!TIP]
> V tématu [pokyny k výkonu](sql-database-performance-guidance.md) najdete techniky, pomocí kterých můžete vylepšit výkon Azure SQL Database po identifikaci problému s výkonem pomocí jedné nebo více výše uvedených metod.

## <a name="troubleshoot-performance-issues"></a>Řešení potíží s výkonem

Chcete-li diagnostikovat a vyřešit problémy s výkonem, začněte tím, že pochopíte stav jednotlivých aktivních dotazů a podmínek, které způsobují problémy s výkonem, které jsou relevantní pro jednotlivé stavy zatížení. Pro zlepšení výkonu Azure SQL Database je potřeba pochopit, že každý aktivní požadavek na dotaz z aplikace je buď ve spuštěném, nebo ve stavu čekání na vyřízení. Při řešení potíží s výkonem v Azure SQL Database mějte na paměti následující graf, který vám umožní diagnostikovat a vyřešit problémy s výkonem v tomto článku.

![Stavy úlohy](./media/sql-database-monitor-tune-overview/workload-states.png)

U úloh s problémy s výkonem může být problém s výkonem způsobený kolizími PROCESORů (stav s **průběžnými** platbami) nebo jednotlivé dotazy čekají na něco (podmínka **související s čekáním** ).

Příčiny potíží **souvisejících** s prováděním mohou být následující:
- **Problémy s kompilací** – Optimalizátor dotazů SQL může vydávat dílčí optimální plán z důvodu zastaralých statistik, nesprávného odhadu počtu zpracovaných řádků nebo odhadu požadované paměti. Pokud víte, že dotaz byl proveden rychleji v minulosti nebo v jiné instanci (buď spravované instance, nebo SQL Server instance), proveďte skutečné plány spuštění a porovnejte je, abyste zjistili, zda jsou odlišné. Zkuste použít pomocný parametr dotazu nebo znovu sestavit statistiku nebo znovu sestavit indexy, abyste získali lepší plán. Povolte automatickou opravu plánu v Azure SQL Database, aby se tyto problémy automaticky zmírnily.
- **Problémy** s prováděním – Pokud je plán dotazů optimální, je pravděpodobné, že v databázi dojde k některému omezení prostředků, například propustnost zápisu protokolu nebo používá defragmentované indexy, které by se měly znovu sestavit. Velký počet souběžných dotazů, které tráví, může být také příčinou problémů se spouštěním. **Čekání související** problémy ve většině případů souvisí s problémy při spuštění, protože dotazy, které neběží efektivně, pravděpodobně čekají na některé prostředky.

Příčiny potíží **souvisejících s čekáním** mohou být následující:
- **Blokování** – jeden dotaz může uchovat zámek u některých objektů v databázi, zatímco ostatní se pokoušejí o přístup ke stejným objektům. Blokování dotazů lze snadno identifikovat pomocí nástrojů pro DMV nebo monitorování.
- **Vstupně-výstupní problémy** – dotazy můžou čekat na zápis stránek do dat nebo souborů protokolu. V tomto případě se `INSTANCE_LOG_RATE_GOVERNOR`v `WRITE_LOG`DMV zobrazují `PAGEIOLATCH_*` statistiky, nebo čekají.
- **Problémy** s databází tempdb – Pokud zatížení využívá spoustu dočasných tabulek nebo je v nich velké množství vynechání databází tempdb, dotazy mohou mít problémy s propustností databáze tempdb. 
- **Problémy související** s pamětí – pravděpodobně není dostatek paměti pro úlohu, aby mohla očekávanéa životnosti stránky nebo že dotazy mají méně paměti, než je potřeba. V některých případech se tyto problémy vyřeší integrovanými inteligentními funkcemi v optimalizátoru dotazů.
 
 V následujících částech se dozvíte, jak identifikovat a vyřešit některé z těchto problémů.

## <a name="running-related-performance-issues"></a>Problémy s výkonem související se spouštěním

Obecně platí, že pokud je využití procesoru konzistentně na úrovni 80% nebo vyšší, dochází k potížím s výkonem, ke kterým dochází. V případě, že dojde k problému souvisejícímu se systémem, může to být způsobeno nedostatečnými prostředky procesoru nebo může souviset s jednou z následujících podmínek:

- Příliš mnoho spuštěných dotazů
- Příliš mnoho dotazů kompilace
- Jeden nebo více zpracovávaných dotazů používá plán dotazu pro dílčí optimální použití.

Je-li zjištěno, že došlo k potížím s výkonem, je cílem identifikovat přesný problém pomocí jedné nebo více metod. Nejběžnější metody identifikace potíží souvisejících s prováděním jsou:

- Pomocí [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) můžete monitorovat procento využití procesoru.
- Použijte následující [zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md):

  - [Sys. DM _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) vrací CPU, vstupně-výstupní operace a spotřebu paměti pro Azure SQL Database. Jeden řádek existuje v intervalu 15 sekund, a to i v případě, že databáze neobsahuje žádné aktivity. Historická data se uchovávají po dobu jedné hodiny.
  - [Sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) vrátí využití CPU a data úložiště pro Azure SQL Database. Data se shromažďují a agregují v intervalu pěti minut.

> [!IMPORTANT]
> Pokud chcete řešit problémy s využitím procesoru, nastavte dotazy T-SQL, které používají tyto zobrazení dynamické správy, v tématu [Identifikace problémů s výkonem procesoru](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Řešení potíží s dotazy s plánem spouštění dotazů závislých na parametrech

Problém s parametrem citlivého plánu (PSP) odkazuje na scénář, ve kterém nástroj pro optimalizaci dotazů generuje plán spouštění dotazů, který je optimální pouze pro konkrétní hodnotu parametru (nebo sadu hodnot) a plán uložený v mezipaměti je neoptimální pro hodnoty parametrů používané v po sobě jdoucí spuštění. Neoptimální plány potom můžou vést k problémům s výkonem a celkovému snížení propustnosti úloh. Další informace o sledování parametrů a zpracování dotazů najdete v [Průvodci architekturou zpracování dotazů](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Pro zmírnění těchto problémů existuje několik alternativních řešení, z nichž každý má Spojené kompromisy a nevýhody:

- Použijte [znovu zkompilování](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) nápovědy pro dotaz při každém spuštění dotazu. Toto řešení usnadňuje dobu kompilace a zvýšenou kapacitu procesoru pro lepší kvalitu plánu. `RECOMPILE` Použití možnosti není často možné pro úlohy, které vyžadují vysokou propustnost.
- Použijte pomocný parametr dotazu [Option (optimize for.](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) ..) pro přepsání skutečné hodnoty parametru s typickou hodnotou parametru, která vytvoří dobrý dostatečný plán pro většinu možností hodnoty parametru.   Tato možnost vyžaduje dobrou představu o optimálních hodnotách parametrů a přidružených vlastnostech plánu.
- Použijte [možnost (optimalizovat pro neznámý)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) Nápověda pro dotaz k přepsání skutečné hodnoty parametru v Exchangi pro použití průměru vektoru hustoty. Dalším způsobem, jak to provést, je zachycení hodnot příchozích parametrů do místních proměnných a následné použití místních proměnných v predikátech namísto použití samotných parametrů. Průměrná hustota musí být pro tuto konkrétní opravu *dostatečně dobrá* .
- Zakažte sledování parametrů výhradně pomocí pomocného parametru dotazu [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) .
- Použijte pomocný parametr dotazu [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) , abyste zabránili překompilování v mezipaměti. Toto alternativní řešení předpokládá *dobrý* společný plán, který je už v mezipaměti. Můžete také zakázat automatické aktualizace statistických údajů, aby se snížila pravděpodobnost, že se dobrý plán vyloučí a že se kompiluje nový špatný plán.
- Vynutit plán explicitním použitím pomocného parametru dotazu [použít plán](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) (explicitním zadáním, nastavením konkrétního plánu pomocí úložiště dotazů nebo povolením [automatického ladění](sql-database-automatic-tuning.md)).
- Nahraďte jeden postup vnořenou sadou procedur, které mohou být použity na základě podmíněné logiky a přidružených hodnot parametrů.
- Vytváření alternativ dynamického provádění řetězce pro definici statické procedury.

Další informace o řešení těchto typů problémů najdete v následujících tématech:

- Toto [je v pachu parametru](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) Blogový příspěvek.
- Tato [Dynamická kvalita plánu SQL versus plánování pro parametrizované dotazy na](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) Blogový příspěvek
- Tyto [techniky optimalizace dotazů SQL v SQL Server: Příspěvek na](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) Blogový příspěvek pro parametry

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Řešení potíží s aktivitou kompilace z důvodu nesprávného Parametrizace

Pokud má dotaz literály, buď databázový modul zvolí možnost automatického parametrizovat příkaz nebo ho může uživatel explicitně parametrizovat, aby se snížil počet kompilací. Vysoký počet zkompilování dotazu pomocí stejného vzoru, ale různé hodnoty literálu může mít za následek vysoké využití procesoru. Podobně, pokud pouze částečně parametrizovat dotaz, který nadále má literály, databázový stroj ho ještě neparametrizovat.  Níže je uveden příklad částečně parametrizovaného dotazu:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

V předchozím příkladu převezme `t1.c1` `@p1` , ale `t2.c2` nadále převezme GUID jako literál. V takovém případě, pokud změníte hodnotu pro `c2`, dotaz bude zpracován jako jiný dotaz a dojde k nové kompilaci. Chcete-li omezit kompilace v předchozím příkladu, řešení je také parametrizovat identifikátor GUID.

Následující dotaz znázorňuje počet dotazů podle hodnoty hash dotazu k určení, jestli je dotaz správně parametrizovaný, nebo ne:

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
### <a name="factors-influencing-query-plan-changes"></a>Faktory ovlivňující změny plánu dotazů

Opětovná kompilace plánu spuštění dotazu může mít za následek vygenerovaný plán dotazu, který se liší od původního ukládání do mezipaměti. Existují různé důvody, proč je možné automaticky znovu zkompilovat existující původní plán:
- Změny ve schématu, na které odkazuje dotaz
- Změny dat v tabulkách, na které odkazuje dotaz 
- Změny možností kontextu dotazu 

Kompilovaný plán může být z mezipaměti vysunut z nejrůznějších důvodů, včetně restartování instance, změn konfigurace v oboru databáze, zatížení paměti a explicitních požadavků na vymazání mezipaměti. Kromě toho se pomocí pomocného parametru pro rekompilaci znamená, že plán nebude uložen do mezipaměti.

Opětovná kompilace (nebo nová kompilace po vyřazení mezipaměti) může i nadále způsobit generování identického plánu spuštění dotazu od původně zjištěného.  Pokud jsou však změny v plánu porovnány s předchozím nebo původním plánem, jsou zde nejběžnější vysvětlení, proč se plán spouštění dotazů změnil:

- **Fyzický návrh se změnil**. Například byly vytvořeny nové indexy, které efektivněji pokrývají požadavky dotazu. Ty mohou být použity na novou kompilaci, pokud se Optimalizátor dotazů rozhodne použít tento nový index, než aby používal datovou strukturu původně vybranou pro první verzi provádění dotazů.  Všechny fyzické změny v odkazovaných objektech mohou mít za následek v době kompilace nový plán.

- **Rozdíly prostředků serveru**. V situaci, kdy se jeden plán liší od "System A" oproti systému B "– dostupnost prostředků, například počet dostupných procesorů, může ovlivnit, který plán se vygeneruje.  Pokud má například jeden systém větší počet procesorů, můžete vybrat paralelní plán. 

- **Různé statistiky**. Statistika přidružená k odkazovaným objektům se změnila nebo se podstatně liší od statistik původních systémů.  Pokud dojde ke změně statistiky a k rekompilaci, bude Optimalizátor dotazů používat statistické údaje k určitému bodu v čase. Revidované statistiky mohou mít značně odlišné distribuce a četnosti dat, které nebyly v původní kompilaci.  Tyto změny se používají k odhadování odhadů mohutnosti (počet řádků očekávaných při toku prostřednictvím logického stromu dotazů).  Změny odhadů mohutnosti nám můžou vést k volbě různých fyzických operátorů a přidružených operací.  I drobné změny statistik můžou mít za následek změnu plánu spuštění dotazu.

- **Změnila se úroveň kompatibility databáze nebo verze Estimator**.  Změny úrovně kompatibility databáze mohou umožňovat nové strategie a funkce, které mohou mít za následek jiný plán spouštění dotazů.  Kromě úrovně kompatibility databáze může být zakázáním nebo povolením příznaku trasování 4199 nebo změnou stavu databáze QUERY_OPTIMIZER_HOTFIXES s rozsahem, který je v době kompilace ovlivněn.  Naplánování ovlivňují také příznaky trasování 9481 (vynutit starší verze CE) a 2312 (vynutit výchozí CE). 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Řešení problémů s dotazy nebo poskytnutí dalších prostředků

Po vyřešení problému můžete buď vyladit dotazy na problém nebo upgradovat výpočetní velikost nebo úroveň služby, abyste zvýšili kapacitu vaší databáze SQL Azure, aby se zvýšila nároky na procesor. Informace o škálování prostředků pro izolované databáze najdete v tématu škálování prostředků [jedné databáze v Azure SQL Database](sql-database-single-database-scale.md) a pro škálování prostředků pro elastické fondy najdete v tématu [škálování prostředků elastického fondu v Azure SQL Database](sql-database-elastic-pool-scale.md). Informace o škálování spravované instance najdete v tématu [omezení prostředků na úrovni instance](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Určení, jestli se spouští problémy kvůli zvýšení objemu úloh

Zvýšení provozu a zatížení aplikací může mít za následek zvýšení využití procesoru, ale při správné diagnostice tohoto problému musíte být opatrní. Ve scénáři s vysokým využitím procesoru odpovězte na tyto otázky, abyste zjistili, jestli je z důvodu změn svazku zatížení skutečně zvýšení výkonu procesoru:

1. Jsou dotazy z aplikace příčinou potíží s vysokým využitím procesoru?
2. Pro nejčastější dotazy náročné na procesor (které je možné identifikovat):

   - Určete, zda bylo ke stejnému dotazu přidruženo více plánů spuštění. Pokud ano, určete, proč.
   - U dotazů se stejným plánem spuštění určete, zda byly časy spuštění konzistentní a zda byl zvýšen počet spuštění. Pokud ano, dochází k pravděpodobným problémům s výkonem kvůli nárůstu zatížení.

V případě, že se plán spouštění dotazů nespustí jinak, ale využití procesoru se zvýšilo společně s počtem spuštění, je pravděpodobný problém s výkonem související s zvýšením zatížení.

Nemusíte se vždycky snadno domístit, když se změní hlasitost zatížení, která je schopná vyřešit problém s PROCESORem.   Faktory, které je potřeba vzít v úvahu: 

- **Využití prostředků se změnilo.**

  Představte si třeba situaci, kdy se procesor zvýšil na 80% po delší dobu.  Samotný využití procesoru neznamená, že se změnil objem úloh.  Regrese plánu provádění dotazů a změny distribuce dat můžou přispět i k využívání prostředků i v případě, že aplikace spouští stejnou konkrétní úlohu.

- **Objevil se nový dotaz**

   Aplikace může v různých časech zařídit novou sadu dotazů.

- **Počet požadavků, které se zvýšily nebo snížily**

   Tento scénář představuje nejobvyklejší míru úlohy. Počet dotazů nemusí vždy odpovídat více využití prostředků. Tato metrika je však stále významným signálem, což předpokládá, že jiné faktory nejsou změněny.

## <a name="waiting-related-performance-issues"></a>Čekající problémy s výkonem

Až se rozhodnete, že nejste s vysokými nároky na výkon procesoru, dochází k problémům s výkonem, ke kterým dochází. Prostředky vašeho procesoru se totiž nepoužívají efektivně, protože procesor čeká na jiný prostředek. V tomto případě je dalším krokem určení toho, co vaše prostředky CPU čekají. Nejběžnější metody pro zobrazení hlavních kategorií typu čekání:

- [Úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) poskytuje statistické údaje o čekání na dotaz v průběhu času. V úložišti dotazů jsou typy čekání kombinovány do kategorií čekání. Mapování kategorií čekání na typy čekání je k dispozici v [tabulce sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [Sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) vrací informace o všech čekáních zjištěných vlákny, které byly během operace provedeny. Pomocí tohoto agregovaného zobrazení můžete diagnostikovat problémy s výkonem Azure SQL Database a také s konkrétními dotazy a dávkami.
- [Sys. DM _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) vrací informace o frontě čekání pro úlohy, které čekají na určitý prostředek.

Ve scénářích s vysokým využitím procesoru neodráží Statistika úložiště dotazů a čekání vždy využití CPU z těchto dvou důvodů:

- Je možné, že se pořád spouštějí dotazy s vysokým využitím procesoru a dotazy se nedokončily.
- Dotazy s vysokým využitím procesoru byly spuštěny, když došlo k převzetí služeb při selhání

Úložiště dotazů a statistické údaje čekání – sledování zobrazení dynamické správy zobrazuje jenom výsledky pro úspěšně dokončené a časované dotazy a nezobrazují data pro aktuálně zpracovávané příkazy (až do jejich dokončení). Zobrazení dynamické správy [Sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) umožňuje sledovat aktuálně spuštěné dotazy a související čas pracovního procesu.

Jak je znázorněno v předchozím grafu, nejběžnější čekání:

- Zámky (blokování)
- I/O
- `tempdb`-související spory
- Přidělení paměti čeká

> [!IMPORTANT]
> Chcete-li vyřešit tyto dotazy T-SQL pomocí těchto zobrazení dynamické správy k řešení těchto problémů souvisejících s čekáním, přečtěte si:
>
> - [Identifikace problémů s výkonem v/v](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifikace `tempdb` problémů s výkonem](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifikace čekání na udělení paměti](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox – čekání a zámky](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Zvýšení výkonu databáze pomocí dalších prostředků

Nakonec, pokud nejsou k dispozici žádné položky, které by mohly zvýšit výkon vaší databáze, můžete změnit množství prostředků, které jsou k dispozici v Azure SQL Database. Další prostředky můžete přiřadit změnou [úrovně služby DTU](sql-database-service-tiers-dtu.md) v izolované databázi nebo v jakémkoli okamžiku zvýšit eDTU elastického fondu. Případně, pokud používáte [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md), můžete změnit buď úroveň služby, nebo zvýšit prostředky přidělené vaší databázi.

1. U izolovaných databází můžete [měnit úrovně služeb](sql-database-single-database-scale.md) nebo [výpočetní prostředky](sql-database-single-database-scale.md) na vyžádání, aby se zlepšil výkon databáze.
2. U více databází zvažte možnost použití [elastických fondů](sql-database-elastic-pool-guidance.md) k automatickému škálování prostředků.

## <a name="tune-and-refactor-application-or-database-code"></a>Ladit a Refaktorovat kód aplikace nebo databáze

Kód aplikace můžete změnit tak, aby lépe používal databázi, změnili indexy, vynutila plány nebo používali tipy k ručnímu přizpůsobení databáze na vaše zatížení. Seznamte se s pokyny a tipy k ručnímu vyladění a psaní kódu v [tématu Průvodce výkonem](sql-database-performance-guidance.md) .

## <a name="next-steps"></a>Další postup

- Pokud chcete povolit automatické ladění v Azure SQL Database a nechat funkci automatického ladění plně spravovat vaše zatížení, přečtěte si téma [Povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Pokud chcete použít ruční ladění, můžete si projít [doporučení pro ladění v Azure Portal](sql-database-advisor-portal.md) a ručně použít ty, které zlepšují výkon dotazů.
- Změna prostředků, které jsou k dispozici v databázi, změnou [Azure SQL Database úrovní služeb](sql-database-performance-guidance.md)
