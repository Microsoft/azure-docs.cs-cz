---
title: Sledování a ladění výkonu
description: Tipy pro ladění výkonu v Azure SQL Database prostřednictvím vyhodnocení a vylepšení.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: e77af00dc3352af3265da90685e58b34c96bee81
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382350"
---
# <a name="monitoring-and-performance-tuning"></a>Sledování a ladění výkonu

Azure SQL Database poskytuje nástroje a metody, pomocí kterých můžete snadno sledovat využití, přidávat nebo odebírat prostředky (například procesor, paměť nebo vstupně-výstupní operace), řešit potenciální problémy a dávat doporučení pro zlepšení výkonu databáze. Funkce v Azure SQL Database můžou automaticky opravovat problémy v databázích. 

Automatické ladění umožňuje, aby se databáze přizpůsobila k zatížení a automaticky optimalizoval výkon. Některé vlastní problémy ale můžou potřebovat řešení potíží. Tento článek vysvětluje některé osvědčené postupy a některé nástroje, které můžete použít k řešení problémů s výkonem.

Chcete-li zajistit, aby databáze běžela bez problémů, měli byste:
- [Monitorujte výkon databáze](#monitor-database-performance) a ujistěte se, že prostředky přiřazené k databázi mohou zpracovat úlohu. Pokud je databáze na omezení prostředků, zvažte následující:
   - Identifikace a optimalizace nejdůležitějších dotazů náročných na prostředky.
   - Přidání dalších prostředků [upgradem úrovně služby](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources).
- [Vyřešte problémy s výkonem](#troubleshoot-performance-problems) , abyste mohli zjistit, proč došlo k potenciálnímu problému, a identifikovat původní příčinu problému. Po zjištění hlavní příčiny proveďte kroky k vyřešení problému.

## <a name="monitor-database-performance"></a>Monitorování výkonu databáze

Pokud chcete monitorovat výkon databáze SQL v Azure, Začněte monitorováním využitých prostředků relativně k úrovni výkonu databáze, kterou jste zvolili. Monitorujte následující zdroje:
 - **Využití CPU**: Zkontrolujte, jestli databáze v delší době dosáhla 100% využití procesoru. Vysoké využití procesoru může znamenat, že potřebujete identifikovat a ladit dotazy, které využívají nejvyšší výpočetní výkon. Vysoké využití procesoru může také znamenat, že databázi nebo instanci byste měli upgradovat na vyšší úroveň služby. 
 - **Čekací Statistika**: pomocí [Sys. dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) určete, jak dlouho čekají dotazy. Dotazy můžou čekat na prostředky, čekání nebo externí čekání. 
 - **Použití v/** v: Zkontrolujte, jestli se databáze nesnaží o limity vstupně-výstupních operací základního úložiště.
 - **Využití paměti**: množství paměti dostupné pro databázi nebo instanci je úměrné počtu virtuální jádra. Ujistěte se, že je paměť pro úlohu dostačující. Životnost stránky očekávané je jedním z parametrů, které mohou určit, jak rychle se stránky odstraňují z paměti.

Služba Azure SQL Database zahrnuje nástroje a prostředky, které vám pomůžou při řešení problémů s výkonem a jejich řešení. Můžete identifikovat příležitosti pro zlepšení a optimalizaci výkonu dotazů bez změny prostředků, a to kontrolou [doporučení pro ladění výkonu](sql-database-advisor.md). 

Častým důvodem toho, že databáze je pomalá, jsou chybějící indexy a nedostatečně optimalizované dotazy. Pro zlepšení výkonu úlohy můžete použít doporučení pro ladění. Můžete také nechat Azure SQL Database [automaticky optimalizovat výkon dotazů](sql-database-automatic-tuning.md) , a to použitím všech identifikovaných doporučení. Pak ověřte, zda doporučení zlepšila výkon databáze.

> [!NOTE]
> Indexování je k dispozici pouze v izolovaných databázích a elastických fondech. Indexování není k dispozici ve spravované instanci.

Pro sledování a řešení potíží s výkonem databáze vyberte jednu z následujících možností:

- V [Azure Portal](https://portal.azure.com)vyberte databáze **SQL** a vyberte databázi. V grafu **monitorování** vyhledejte prostředky, které se blíží jejich maximálnímu využití. Ve výchozím nastavení se zobrazuje spotřeba DTU. Vyberte **Upravit** a změňte časový rozsah a zobrazené hodnoty.
- Nástroje, jako je SQL Server Management Studio, poskytují spoustu užitečných sestav, jako je [řídicí panel výkonu](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard). Pomocí těchto sestav můžete monitorovat využití prostředků a identifikovat nejdůležitější dotazy náročné na prostředky. [Úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) můžete použít k identifikaci dotazů, u kterých došlo k překročení výkonu.
- V [Azure Portal](https://portal.azure.com)použijte [Query Performance Insight](sql-database-query-performance.md) k identifikaci dotazů, které používají nejvíc prostředků. Tato funkce je k dispozici pouze v izolovaných databázích a elastických fondech.
- Pomocí [SQL Database Advisor](sql-database-advisor-portal.md) můžete zobrazit doporučení, která vám pomůžou vytvářet a odstraňovat indexy, parametrizovat dotazy a opravovat problémy s schématy. Tato funkce je k dispozici pouze v izolovaných databázích a elastických fondech.
- K automatickému monitorování výkonu databáze použijte [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) . Při zjištění problému s výkonem se vygeneruje diagnostický protokol. Protokol poskytuje podrobnosti a analýzu hlavní příčiny problému. Pokud je to možné, je k dispozici doporučení pro zlepšení výkonu.
- [Povolením automatického ladění](sql-database-automatic-tuning-enable.md) umožníte Azure SQL Database automaticky opravovat problémy s výkonem.
- Pomocí [zobrazení dynamické správy (zobrazení dynamické správy)](sql-database-monitoring-with-dmvs.md), [rozšířených událostí](sql-database-xevent-db-diff-from-svr.md)a [úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) získáte pomoc s podrobnějším řešením problémů s výkonem.

> [!TIP]
> Po zjištění potíží s výkonem si Projděte naše [Průvodce výkonem](sql-database-performance-guidance.md) , kde najdete techniky pro zlepšení výkonu Azure SQL Database.

## <a name="troubleshoot-performance-problems"></a>Řešení potíží s výkonem

Chcete-li diagnostikovat a vyřešit problémy s výkonem, začněte tím, že vyhledáte stav jednotlivých aktivních dotazů a podmínek, které způsobují problémy s výkonem, které jsou relevantní pro jednotlivé stavy zatížení. Chcete-li zlepšit výkon Azure SQL Database, je nutné pochopit, že každý aktivní požadavek na dotaz z aplikace je v běžícím nebo čekajícím stavu. Při odstraňování potíží s výkonem v Azure SQL Database mějte na paměti následující diagram.

![Stavy úlohy](./media/sql-database-monitor-tune-overview/workload-states.png)

Problémy s výkonem v úloze mohou být způsobeny kolizími PROCESORů (s *běžící* podmínkou) nebo jednotlivými dotazy, které čekají na něco (podmínka *související s čekáním* ).

Problémy související se spouštěním můžou způsobovat tyto příčiny:
- **Problémy s kompilací**: Optimalizátor dotazů SQL může vydávat neoptimální plán kvůli zastaralým statistikám, nesprávnému odhadu počtu zpracovaných řádků nebo nepřesnému odhadu požadované paměti. Pokud víte, že dotaz byl proveden rychleji v minulosti nebo v jiné instanci (buď spravovaná instance, nebo SQL Server instance), porovnejte skutečné plány spuštění a zjistěte, zda se liší. Zkuste použít pomocný parametr dotazu nebo znovu sestavit statistiku nebo indexy, abyste získali lepší plán. Povolte automatickou opravu plánu v Azure SQL Database, aby se tyto problémy automaticky zmírnily.
- **Problémy s spuštěním**: Pokud je plán dotazu optimální, je pravděpodobné, že dojde k překročení limitů prostředků databáze, jako je například propustnost zápisu protokolu. Nebo může používat fragmentované indexy, které by se měly znovu sestavit. Problémy s prováděním mohou nastat i v případě, že velký počet souběžných dotazů potřebuje stejné prostředky. K problémům *souvisejícím s čekáním* obvykle dochází v souvislosti s problémy se spouštěním, protože dotazy, které se nespouštějí efektivně, pravděpodobně čekají na některé prostředky.

Problémy související s čekáním můžou způsobovat tyto příčiny:
- **Blokování**: jeden dotaz může uchovávat zámek objektů v databázi, zatímco se jiné snaží získat přístup ke stejným objektům. Blokování dotazů můžete identifikovat pomocí nástrojů pro zobrazení dynamické správy nebo monitorování.
- **Vstupně-výstupní problémy**: dotazy můžou čekat na zápis stránek do dat nebo souborů protokolu. V takovém případě se podívejte `INSTANCE_LOG_RATE_GOVERNOR`na DMV, `WRITE_LOG`nebo `PAGEIOLATCH_*` Statistika čekání v.
- **Problémy**s databází tempdb: Pokud úloha používá dočasné tabulky nebo v plánech dochází k únikům databází tempdb, dotazy mohou mít problémy s propustností databáze tempdb. 
- **Problémy související s pamětí**: Pokud úloha nemá dostatek paměti, může dojít k poklesu životnosti stránky očekávané a dotazy můžou dostat méně paměti, než potřebují. V některých případech integrované inteligentní funkce v nástroji pro optimalizaci dotazů vyřeší problémy související s pamětí.
 
Následující části vysvětlují, jak identifikovat a řešit potíže s některými typy problémů.

## <a name="performance-problems-related-to-running"></a>Problémy s výkonem související se spouštěním

Obecně platí, že pokud je využití procesoru konzistentně na úrovni 80 nebo vyšší než%, je problém s výkonem spojený s výkonem. Problémy související se systémem mohou být způsobeny nedostatečnými prostředky procesoru. Nebo může souviset s jednou z následujících podmínek:

- Příliš mnoho spuštěných dotazů
- Příliš mnoho dotazů kompilace
- Jeden nebo více spuštěných dotazů, které používají optimální plán dotazu

Pokud narazíte na běžící problémy s výkonem, je vaším cílem určit přesný problém pomocí jedné nebo více metod. Tyto metody jsou nejběžnějšími způsoby, jak identifikovat problémy související se spouštěním:

- Pomocí [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) můžete monitorovat procento využití procesoru.
- Použijte následující [zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md):

  - [Sys. dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV vrací CPU, vstupně-výstupní operace a spotřebu paměti pro databázi SQL. U každého intervalu 15 sekund existuje jeden řádek, a to i v případě, že databáze neobsahuje žádné aktivity. Historická data se uchovávají po dobu jedné hodiny.
  - [Sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV vrátí využití CPU a data úložiště pro Azure SQL Database. Data se shromažďují a agregují v intervalech po pěti minutách.

> [!IMPORTANT]
> Informace o řešení problémů s využitím procesoru u dotazů T-SQL, které používají rozhraní sys. dm_db_resource_stats a sys. resource_stats zobrazení dynamické správy, najdete v tématu [Identifikace problémů s výkonem procesoru](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Dotazy, které mají problémy PSP

Když Optimalizátor dotazů vygeneruje plán spouštění dotazů, který je optimální jenom pro konkrétní hodnotu parametru (nebo sadu hodnot), a plán v mezipaměti není optimální pro hodnoty parametrů, které se používají v průběhu po sobě. spuštění. Plány, které nejsou optimální, můžou následně způsobit problémy s výkonem dotazů a snižovat celkovou propustnost úloh. 

Další informace o sledování parametrů a zpracování dotazů naleznete v [příručce k architektuře zpracování dotazů](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Několik alternativních řešení může zmírnit problémy PSP. Každé řešení má Spojené kompromisy a nevýhody:

- Použijte [znovu zkompilování](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) nápovědy pro dotaz při každém spuštění dotazu. Toto řešení usnadňuje dobu kompilace a zvýšenou kapacitu procesoru pro lepší kvalitu plánu. Možnost `RECOMPILE` není často dostupná pro úlohy, které vyžadují vysokou propustnost.
- Použijte pomocný parametr dotazu [Option (optimize for.](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) ..) pro přepsání skutečné hodnoty parametru s typickou hodnotou parametru, která vytvoří plán, který je dostatečně dobrý pro většinu možností hodnoty parametru. Tato možnost vyžaduje dobrou představu o optimálních hodnotách parametrů a přidružených vlastnostech plánu.
- Použijte pomocný parametr dotazu [Option (optimalizovat pro neznámý)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pro přepsání skutečné hodnoty parametru a místo toho použijte průměr vektoru hustoty. To lze provést také zachycením hodnot příchozích parametrů v místních proměnných a následným použitím místních proměnných v predikátech namísto použití samotných parametrů. Pro tuto opravu musí být průměrná hustota *dostatečně dobrá*.
- Pomocí pomocného parametru dotazu [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) zakažte pouze sledování parametrů.
- Použijte pomocný parametr dotazu [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) k zabránění rekompilacím v mezipaměti. V tomto alternativním řešení se předpokládá, že v mezipaměti již existuje dobrý společný plán. Můžete také zakázat automatické aktualizace statistiky, aby se snížila pravděpodobnost, že bude dobrý plán vyřazený a bude zkompilován nový špatný plán.
- Vynutit plán explicitním použitím pomocného parametru dotazu [použít plán](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) tak, že přepíšete dotaz a přidáte pomocný parametr do textu dotazu. Nebo nastavte konkrétní plán pomocí úložiště dotazů nebo povolením [automatického ladění](sql-database-automatic-tuning.md).
- Nahraďte jeden postup vnořenou sadou procedur, které mohou být použity na základě podmíněné logiky a přidružených hodnot parametrů.
- Vytváření alternativ dynamického provádění řetězce pro definici statické procedury.

Další informace o řešení problémů s PSP najdete v těchto blogových příspěvcích:

- [Mám v pachu parametr](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [Conor vs. dynamické SQL vs. postupy vs. plánování kvality pro parametrizované dotazy](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Techniky optimalizace dotazů SQL v SQL Server: sledování parametrů](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Aktivita kompilace způsobila nesprávné Parametrizace

Pokud má dotaz literály, buď databázový stroj automaticky parameterizes příkaz, nebo uživatel výslovně parameterizes příkaz pro snížení počtu kompilací. Vysoký počet kompilací pro dotaz používající stejný vzor, ale různé hodnoty literálu může mít za následek vysoké využití procesoru. Podobně platí, že pokud pouze částečně parametrizovat dotaz, který nadále má literály, databázový stroj nebude dotaz dále parametrizovat.  

Tady je příklad částečně parametrizovaného dotazu:

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

V tomto příkladu `t1.c1` přebírá `@p1`, ale `t2.c2` nadále přebírají GUID jako literál. V takovém případě, pokud změníte hodnotu pro `c2`, dotaz je považován za jiný dotaz a dojde k nové kompilaci. Chcete-li snížit kompilace v tomto příkladu, můžete také parametrizovat identifikátor GUID.

Následující dotaz znázorňuje počet dotazů podle hodnoty hash dotazu k určení, jestli je dotaz správně parametrizovaný:

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

### <a name="factors-that-affect-query-plan-changes"></a>Faktory ovlivňující změny plánu dotazů

Opětovná kompilace plánu spuštění dotazu může mít za následek vygenerovaný plán dotazu, který se liší od původního plánu v mezipaměti. Existující původní plán se může automaticky znovu zkompilovat z různých důvodů:
- Na změny ve schématu odkazuje dotaz.
- Na změny dat v tabulkách se odkazuje dotaz. 
- Možnosti kontextu dotazu se změnily.

Kompilovaný plán může být vysunut z mezipaměti z různých důvodů, například:

- Instance se restartuje.
- Změny konfigurace v rozsahu databáze.
- Tlak paměti.
- Explicitní požadavky na vymazání mezipaměti

Použijete-li pomocný parametr RECOMPILE, plán nebude uložen do mezipaměti.

Opětovná kompilace (nebo nová kompilace po vyřazení mezipaměti) může stále vést k vytvoření plánu provádění dotazů, který je totožný s původní. Při změně plánu z předchozího nebo původního plánu jsou tato vysvětlení pravděpodobná:

- **Změna fyzického návrhu**: například nově vytvořené indexy efektivněji pokrývají požadavky dotazu. Nové indexy lze použít pro novou kompilaci, pokud se nástroj Optimalizátor dotazů rozhodne, že použití tohoto nového indexu je více optimální než použití struktury dat, která byla původně vybrána pro první verzi provedení dotazu.  Všechny fyzické změny v odkazovaných objektech mohou mít za následek novou volbu plánu v době kompilace.

- **Rozdíly mezi prostředky serveru**: Pokud se plán v jednom systému liší od plánu v jiném systému, může být dostupnost prostředků, například počet dostupných procesorů, ovlivněna tím, který plán se vygeneruje.  Pokud má například jeden systém více procesorů, může být zvolen paralelní plán. 

- **Různé statistiky**: statistiky spojené s odkazovanými objekty se možná změnily nebo můžou být v nepodstatném rozdílu od statistik původních systémů.  Pokud se změní Statistika a provede se opětovná kompilace, používá Optimalizátor dotazů statistiku, která začíná od okamžiku jejich změny. Změny distribuce a četnosti dat revidované statistiky se mohou lišit od původní kompilace.  Tyto změny slouží k vytvoření odhadů mohutnosti. (*Odhady mohutnosti* jsou počet řádků, které se mají přesměrovat do logického stromu dotazů.) Změny odhadů mohutnosti můžou vést k volbě různých fyzických operátorů a přidružených objednávek operací.  I drobné změny statistik můžou mít za následek změnu plánu spuštění dotazu.

- **Změnila se úroveň kompatibility databáze nebo verze Estimator**: změny úrovně kompatibility databáze mohou umožňovat nové strategie a funkce, které mohou mít za následek jiný plán spouštění dotazů.  Kromě úrovně kompatibility databáze může mít zakázaný nebo povolený příznak trasování 4199 nebo změněný stav QUERY_OPTIMIZER_HOTFIXES konfigurace s rozsahem databáze také v době kompilace vliv výběru plánu spouštění dotazů.  Tento plán ovlivňují také příznaky trasování 9481 (vynutit starší verze CE) a 2312 (vynutit výchozí CE). 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Řešení problémů s dotazy nebo poskytnutí dalších prostředků

Po zjištění problému můžete buď vyladit dotazy na problém nebo upgradovat výpočetní velikost nebo úroveň služby, aby se zvýšila kapacita vaší databáze SQL, aby se zvýšila nároky na procesor. 

Další informace najdete v tématu [škálování jednotlivých prostředků databáze ve Azure SQL Database](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu v Azure SQL Database](sql-database-elastic-pool-scale.md). Informace o škálování spravované instance najdete v tématu [omezení prostředků na úrovni služby](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="performance-problems-caused-by-increased-workload-volume"></a>Problémy s výkonem způsobené větším objemem úloh

Zvýšení provozu aplikací a objemu úloh může způsobit zvýšené využití CPU. Pro správné diagnostiku tohoto problému je ale nutné být opatrní. Když se zobrazí problém s vysokým využitím procesoru, odpovězte na tyto otázky, abyste zjistili, jestli se zvýšení způsobilo změnami svazku zatížení:

- Jsou dotazy z aplikace příčinou potíží s vysokým využitím procesoru?
- Pro nejčastější dotazy náročné na procesor, které můžete identifikovat:

   - Bylo k jednomu dotazu přidruženo více plánů spuštění? Pokud ano, proč?
   - U dotazů se stejným plánem spuštění byly časy spuštění konzistentní? Zvýšil se počet spuštění? Pokud ano, zvýšení zatížení bude nejspíš způsobovat problémy s výkonem.

Pokud se plán spouštění dotazů nespustí jinak, ale využití procesoru se zvýšilo společně s počtem spuštění, je problém s výkonem pravděpodobným zvýšením zatížení.

Nemusíte vždycky snadno identifikovat změnu objemu úloh, která je příčinou potíží procesoru. Vezměte v úvahu tyto faktory: 

- **Změnilo se využití prostředků**: můžete například zvážit situaci, kdy se využití CPU v delší době zvýšilo na 80 procent.  Samotný využití procesoru neznamená, že se změnil objem úloh. Regrese v plánu provádění dotazů a změny v distribuci dat můžou přispět i k využívání prostředků i v případě, že aplikace provádí stejnou úlohu.

- **Vzhled nového dotazu**: aplikace může v různých časech zařídit novou sadu dotazů.

- **Zvýšení nebo snížení počtu požadavků**: Tento scénář je nejzjevnější míra zatížení. Počet dotazů nemusí vždy odpovídat více využití prostředků. Tato metrika je však stále významným signálem za předpokladu, že jiné faktory nejsou změněny.

## <a name="waiting-related-performance-problems"></a>Čekající problémy s výkonem 

Pokud jste si jistí, že váš problém s výkonem nesouvisí s vysokým využitím procesoru nebo v provozu, váš problém se týká čekání. Prostředky vašeho procesoru se totiž nepoužívají efektivně, protože procesor čeká na jiný prostředek. V takovém případě určete, na co vaše prostředky CPU čekají. 

Tyto metody se běžně používají k zobrazení hlavních kategorií typů čekání:

- Pomocí [úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) můžete najít statistiku čekání pro každý dotaz v průběhu času. V úložišti dotazů jsou typy čekání kombinovány do kategorií čekání. Můžete najít mapování kategorií čekání na typy čekání v [Sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Pomocí [Sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) vracet informace o všech čekáních zjištěných vlákny, které byly během operace provedeny. Pomocí tohoto agregovaného zobrazení můžete diagnostikovat problémy s výkonem Azure SQL Database a také s konkrétními dotazy a dávkami.
- Pomocí [Sys. dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) můžete vracet informace o frontě úloh, které čekají na určitý prostředek.

V případě scénářů s vysokým využitím procesoru se v úložišti dotazů a v statistikách čekání nemusí projevit využití CPU, pokud:

- Vysoce náročné dotazy využívající procesor jsou pořád spuštěné.
- Dotazy s vysokým využitím procesoru byly spuštěny, když došlo k převzetí služeb při selhání.

Zobrazení dynamické správy, které sledují úložiště dotazů a statistiky čekání zobrazují výsledky pouze pro úspěšně dokončené a časované dotazy. Nezobrazují data pro aktuálně zpracovávané příkazy, dokud se nedokončí příkazy. Pomocí zobrazení dynamické správy [Sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) můžete sledovat aktuálně zpracovávané dotazy a přidruženou dobu pracovního procesu.

Graf na začátku tohoto článku znázorňuje, že nejběžnější čekání:

- Zámky (blokování)
- I/O
- Spor týkající se databáze TempDB
- Přidělení paměti čeká

> [!IMPORTANT]
> Sadu dotazů T-SQL, které používají zobrazení dynamické správy k řešení problémů souvisejících s čekáním, najdete v těchto tématech:
>
> - [Identifikace problémů s výkonem v/v](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifikace čekání na udělení paměti](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox čekání a zámky](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>Zvýšení výkonu databáze pomocí dalších prostředků

Pokud nemůžete zvýšit výkon databáze, můžete změnit množství prostředků, které jsou k dispozici v Azure SQL Database. Přiřaďte více prostředků změnou [úrovně služby DTU](sql-database-service-tiers-dtu.md) v jedné databázi. Nebo zvyšte eDTU elastického fondu kdykoli. Případně, pokud používáte [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md), změňte úroveň služby nebo zvyšte prostředky přidělené vaší databázi.

U izolovaných databází můžete [změnit úrovně služeb nebo výpočetní prostředky](sql-database-single-database-scale.md) na vyžádání, aby se zlepšil výkon databáze. U více databází zvažte možnost použití [elastických fondů](sql-database-elastic-pool-guidance.md) k automatickému škálování prostředků.

## <a name="tune-and-refactor-application-or-database-code"></a>Ladit a Refaktorovat kód aplikace nebo databáze

Můžete optimalizovat kód aplikace pro databázi, změnit indexy, vynutit plány nebo použít pokyny k ručnímu přizpůsobení databáze pro vaše zatížení. Informace o ručním vyladění a přepisu kódu najdete v tématu [pokyny k ladění výkonu](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Další kroky

- Pokud chcete povolit automatické ladění v Azure SQL Database a nechat funkci automatického ladění plně spravovat vaše zatížení, přečtěte si téma [Povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Pokud chcete použít ruční ladění, přečtěte si téma [optimalizace doporučení v Azure Portal](sql-database-advisor-portal.md). Ručně použijte doporučení, která zlepšují výkon dotazů.
- Změňte prostředky, které jsou k dispozici v databázi, změnou [Azure SQL Database úrovní služeb](sql-database-performance-guidance.md).
