---
title: Monitorování a optimalizace výkonu – Azure SQL Database | Dokumentace Microsoftu
description: Tipy pro ladění ve službě Azure SQL Database prostřednictvím hodnocení a zlepšování výkonu.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/24/2019
ms.openlocfilehash: 2638617679e1aaadfdb18f1d8580f3aef28ab127
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884347"
---
# <a name="monitoring-and-performance-tuning"></a>Sledování a ladění výkonu

Azure SQL Database je služby data automaticky spravované a flexibilní, kde můžete snadno sledovat využití, přidání nebo odebrání prostředků (procesoru, paměti, vstupně-výstupní operace), najít doporučení, které může zlepšit výkon vaší databáze, nebo nechat databáze přizpůsobit vaší úloze a automaticky optimalizace výkonu.

## <a name="monitoring-database-performance"></a>Monitorování výkonu databáze

Monitorování výkonu databáze SQL v Azure začíná sledováním využití prostředků relativně ke zvolené úrovni výkonu databáze. Azure SQL Database vám pomůže identifikovat příležitosti, jak vylepšit a optimalizovat výkon dotazů beze změny zdroje kontrolou [doporučení pro optimalizaci výkonu](sql-database-advisor.md). Častým důvodem toho, že databáze je pomalá, jsou chybějící indexy a nedostatečně optimalizované dotazy. Můžete použít tato doporučení pro vyladění výkonu vašich úloh. Můžete také umožňují Azure SQL database a [automaticky optimalizace výkonu vašich dotazů](sql-database-automatic-tuning.md) použitím všechny určené doporučení a ověřuje, že pomáhají zvýšit výkon databáze.

Máte následující možnosti pro monitorování a řešení potíží s výkonem databáze:

- V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **databází SQL**, vyberte databázi a pak použít graf monitorování k vyhledání prostředků blíží jejich maximální. Ve výchozím nastavení se zobrazí spotřeba DTU. Klikněte na tlačítko **upravit** změnit časový rozsah a hodnoty.
- Použití [Query Performance Insight](sql-database-query-performance.md) identifikovat dotazy, které tráví nejvíce prostředků.
- Použití [služby SQL Database Advisor](sql-database-advisor-portal.md) Chcete-li zobrazit doporučení pro vytváření a vyřazení indexů, parametrické dotazy a řešení problémů schématu.
- Použití [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pro automatické monitorování výkonu vaší databáze. Po zjištění problému s výkonem se vygeneruje protokol diagnostiky s podrobnostmi a kořenové příčiny analýzy RCA () problému. Pokud je to možné, poskytuje doporučení pro zlepšení výkonu.
- [Povolení automatického ladění](sql-database-automatic-tuning-enable.md) a nechat Azure SQL database automaticky opravě zjištěných problémů s výkonem.
- Použití [zobrazení dynamické správy (DMV)](sql-database-monitoring-with-dmvs.md), [rozšířených událostí](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)a [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) podrobný Poradce při potížích s problémy s výkonem.

> [!TIP]
> Zobrazit [Průvodce výkonem](sql-database-performance-guidance.md) najít techniky, které můžete použít ke zlepšení výkonu služby Azure SQL Database po identifikaci problému výkonu pomocí jednoho nebo více z výše uvedených metod.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorování databází na portálu Azure

V [webu Azure portal](https://portal.azure.com/), můžete monitorovat využití izolované databáze s výběrem databáze a kliknutím na **monitorování** grafu. Zobrazí se okno **Metrika**, které můžete upravit kliknutím na **Upravit graf**. Přidejte následující metriky:

- Procento CPU
- Procento DTU
- Procento datových V/V
- Procento velikosti databáze

Jakmile přidáte tyto metriky, můžete pokračovat jejich zobrazením v **monitorování** graf s dalšími informacemi o **metrika** okna. Tyto čtyři metriky uvádějí průměrné využití v procentech vzhledem k hodnotě **DTU** vaší databáze. Zobrazit [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) články pro další informace o úrovních služeb.  

![Monitorování výkonu databáze v rámci úrovně služeb](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Můžete také nastavit upozornění na výkonové metriky. Klikněte na tlačítko **Přidat upozornění** v okně **Metrika**. Nastavte upozornění podle pokynů průvodce. Můžete určit, zda chcete být upozorněni na překročení zadané prahové hodnoty, nebo naopak když metrika poklesne pod zadanou mez.

Například pokud očekáváte nárůst zatížení databáze, můžete nastavit e-mailové upozornění pro případ, že databáze překročí 80 % kterékoli výkonové metriky. Můžete to použít jako včasné varování zjistit, když bude pravděpodobně nutné přepnout na další nejvyšší velikost výpočetní prostředky.

Metriky výkonu také můžete zjistit, zda je možné nižší výpočty velikosti. Předpokládejme, že používáte databáze S2 v úrovni Standard a všechny metriky ukazují, že databáze v průměru nevyužívá více než 10 % dostupného výkonu. Je pravděpodobné, že databáze bude dobře fungovat i v úrovni Standard S1. Nezapomínejte, úloh, které výskytu špiček nebo náhlého před provedením kolísání nižší výpočty velikosti.

## <a name="troubleshoot-performance-issues"></a>Řešení potíží s výkonem

K diagnostikování a vyřešení problémů s výkonem, začněte tím, že vysvětlení stavu každý aktivní dotaz a podmínek, které způsobují problémy s výkonem relevantní pro každý stav úlohy. Kvůli zvýšení výkonu Azure SQL Database se pochopit, že každý požadavek aktivní dotaz z vaší aplikace je buď ve stavu čekání nebo spuštěné. Při řešení problému s výkonem ve službě Azure SQL Database, mějte v následujícím grafu jako jste si tento článek k diagnostikování a vyřešení problémů s výkonem.

![Stavy úlohy](./media/sql-database-monitor-tune-overview/workload-states.png)

Pro úlohy s problémy s výkonem, tyto problémy s výkonem pravděpodobně z důvodu kolize procesoru ( **týkající se spouštění** podmínky) nebo jednotlivé dotazy čekají na libovolnou položku ( **související čekání** podmínku ).

## <a name="running-related-performance-issues"></a>Problémy s výkonem souvisejících s spuštění

V rámci obecných pokynů Pokud vaše využití procesoru je trvale dosahovalo nebo přesahovalo 80 %, je nutné problém s výkonem souvisejících s spuštěná. Pokud máte potíže se související se spuštěná, může být způsobena nedostatkem prostředků procesoru nebo může mít relaci k jednomu z následujících podmínek:

- Moc velký počet spuštěných dotazů
- Příliš mnoho kompilaci dotazů
- Jeden nebo více provádění dotazů používají plán neoptimálním průběhem dotazu

Pokud zjistíte, že máte problém s výkonem souvisejících s běží, je vaším cílem je k jeho identifikaci přesné pomocí jedné nebo několika metod. Nejběžnější metody k identifikaci problémů souvisejících se spuštění jsou následující:

- Použití [webu Azure portal](#monitor-databases-using-the-azure-portal) k monitorování procenta využití procesoru.
- Pomocí následujících [zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md):

  - [Sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) vrátí spotřeby procesoru, vstupně-výstupní operace a paměti pro databázi Azure SQL Database. Jeden řádek existuje pro každých 15 sekund, i v případě, že neexistuje žádná aktivita v databázi. Historická data se udržují za jednu hodinu.
  - [Sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) vrátí data o využití a úložiště CPU pro službu Azure SQL Database. Data se shromažďují a agregují v pětiminutovém intervalu.

> [!IMPORTANT]
> Sada dotazů T-SQL pomocí těchto zobrazení dynamické správy k řešení potíží využití procesoru, naleznete v tématu [procesoru identifikovat problémy s výkonem](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="troubleshoot-queries-with-parameter-sensitive-query-execution-plan-issues"></a>Řešení potíží s dotazy s problémy plán spuštění závislé na parametru dotazu

Potíže s parametrem citlivé plán (PSP) odkazuje na scénáři, kde Optimalizátor dotazů generuje plán provádění dotazu, který je ideální jenom pro konkrétní parametr (nebo sadu hodnot) a bude plánů v mezipaměti optimální zadání hodnot parametrů používaných pro počet po sobě jdoucích spuštění. Optimální jiné plány může pak způsobit problémy s výkonem dotazů a celkovou propustnost snížení zatížení.

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

- To [cítit parametr](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) blogový příspěvek
- To [parametr pro analýzu sítě problému a alternativní řešení](https://blogs.msdn.microsoft.com/turgays/2013/09/10/parameter-sniffing-problem-and-possible-workarounds/) blogový příspěvek
- To [Slon a pro analýzu sítě myši parametr](https://www.brentozar.com/archive/2013/06/the-elephant-and-the-mouse-or-parameter-sniffing-in-sql-server/) blogový příspěvek
- To [dynamické srovnání plánu kvality pro parametrizované dotazy sql](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) blogový příspěvek

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Řešení potíží s aktivitu kompilace z důvodu nesprávné Parametrizace

Když má dotaz literály, databázový stroj zvolí možnost automaticky parametrizovat příkaz nebo uživatel můžete explicitně parametrizovat kvůli snížení počtu zkompiluje. Vysoké využití procesoru může způsobit vysoký počet zkompiluje dotazu pomocí stejného vzoru, ale jiné hodnoty literálu. Podobně pokud jen částečně parametrizace dotazu, který se bude mít literály, databázový stroj parametrizovat ho dále.  Níže je příklad částečně parametrizovaného dotazu:

```sql
select * from t1 join t2 on t1.c1=t2.c1
where t1.c1=@p1 and t2.c2='961C3970-0E54-4E8E-82B6-5545BE897F8F'
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

- [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) poskytuje statistiky čekání každý dotaz v čase. Na Query Store čekání typy jsou sloučeny do kategorií čekání. Je k dispozici v mapování kategorií wait čekat typy [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql?view=sql-server-2017#wait-categories-mapping-table).
- [Sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) vrátí informace o všech čeká tak vlákna, která provedených během operace. Toto souhrnné zobrazení můžete použít k diagnostice problémů s výkonem s Azure SQL Database a také s konkrétní dotazy a dávek.
- [Sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) vrátí informace o frontě čekání úlohy, které čekají na některých prostředků.

Ve scénářích vysoké využití procesoru Query Store a statistiky čekání nemusí pokaždé odpovídat využití procesoru pro dvě z těchto důvodů:

- Využívání dotazy vysoké využití procesoru může být stále provádí a dotazy nebylo dokončeno.
- Vysoké využití procesoru náročné dotazy byly spuštěny při došlo k chybě převzetí služeb při selhání

Query Store a zobrazení dynamické správy sledování statistiky čekání pouze zobrazit výsledky pro dotazy na úspěšně dokončené a časového limitu a nezobrazují data pro aktuálně spouští příkazy (až do dokončení).  Zobrazení dynamické správy [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) umožňuje sledovat právě spouští dotazy a přidružené pracovní doby.

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

## <a name="improving-database-performance-with-more-resources"></a>Zlepšení výkonu databáze s více prostředky

Nakonec pokud nebyly nalezeny žádné užitečné položky, které může zlepšit výkon vaší databáze, můžete změnit množství prostředků, které jsou k dispozici ve službě Azure SQL Database. Můžete přiřadit více prostředků tak, že změníte [jednotek DTU úrovně](sql-database-service-tiers-dtu.md) izolované databáze nebo zvýšení Edtu elastického fondu v každém okamžiku. Případně pokud používáte [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md), můžete změnit úroveň služby nebo zvýšit prostředky přidělené k vaší databázi.

1. Pro izolované databáze můžete [změna úrovní služeb](sql-database-service-tiers-dtu.md) nebo [výpočetní prostředky](sql-database-service-tiers-vcore.md) na vyžádání ke zlepšení výkonu databáze.
2. Pro více databází, zvažte použití [elastické fondy](sql-database-elastic-pool-guidance.md) automaticky škálovat prostředky.

## <a name="tune-and-refactor-application-or-database-code"></a>Ladění a Refaktorujte aplikace nebo kódu databáze

Můžete změnit kód aplikace více optimálně použít databázi, změňte indexy, vynutit plány nebo používání pomocné parametry ručně přizpůsobit databáze vaší úloze. Najdete některé pokyny a tipy pro ruční optimalizace a přepsáním kódu v [tématu pokyny k výkonu](sql-database-performance-guidance.md) článku.

## <a name="next-steps"></a>Další postup

- Povolení automatického ladění ve službě Azure SQL Database a nechat funkce automatického ladění plně spravovat vaše úlohy najdete v tématu [povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Chcete-li použít ruční ladění, můžete zkontrolovat [doporučení na webu Azure portal pro optimalizaci](sql-database-advisor-portal.md) a aplikovat ručně ty, které zlepšují výkon vašich dotazů.
- Změnit prostředky, které jsou k dispozici v databázi tak, že změníte [úrovně služby Azure SQL Database](sql-database-performance-guidance.md)
