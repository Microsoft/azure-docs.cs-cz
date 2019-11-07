---
title: Monitorování výkonu databáze pomocí Intelligent Insights-Azure SQL Database
description: Azure SQL Database Intelligent Insights pomocí integrovaných inteligentních funkcí průběžně monitoruje využití databáze prostřednictvím umělých inteligentních funkcí a detekuje rušivé události, které způsobují špatný výkon.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: c073b535271ba8f9f11e17ba5203b13e257e211b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689676"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>Intelligent Insights používání AI k monitorování a řešení potíží s výkonem databáze

Azure SQL Database Intelligent Insights vám umožní zjistit, co se děje s vaším SQL Database a výkonem databáze spravované instance.

Intelligent Insights pomocí integrovaných inteligentních funkcí průběžně monitoruje využití databáze prostřednictvím umělých inteligentních funkcí a detekuje rušivé události, které způsobují špatný výkon. Po zjištění se provede podrobná analýza, která generuje diagnostický protokol s inteligentním posouzením problému. Toto posouzení se skládá z analýzy hlavní příčiny problémů s výkonem databáze a tam, kde je to možné, doporučení pro zlepšení výkonu.

## <a name="what-can-intelligent-insights-do-for-you"></a>Co může Intelligent Insights

Intelligent Insights je jedinečná funkce integrovaných inteligentních funkcí Azure, která poskytuje následující hodnotu:

- Proaktivní monitorování
- Přehledy výkonu s upraveným výkonem
- Předčasné zjištění snížení výkonu databáze
- Zjistila se analýza problémů hlavní příčiny.
- Doporučení pro zlepšení výkonu
- Možnosti horizontálního navýšení kapacity na stovkách tisíc databází
- Kladný dopad na prostředky DevOps a celkové náklady na vlastnictví

## <a name="how-does-intelligent-insights-work"></a>Jak funguje Intelligent Insights

Intelligent Insights analyzuje výkon databáze porovnáním databázového zatížení za poslední hodinu a poslední sedm dní. Databázové úlohy se skládají z dotazů, které jsou pro výkon databáze nejdůležitější, jako je například nejpravděpodobnější a největší počet dotazů. Vzhledem k tomu, že každá databáze je jedinečná na základě její struktury, dat, využití a aplikace, jednotlivé standardní hodnoty úloh jsou specifické a jedinečné pro jednotlivé instance. Intelligent Insights, nezávisle na standardních hodnotách úloh, monitoruje také absolutní provozní prahové hodnoty a detekuje problémy s nadměrnými čekacími dobami, kritickými výjimkami a problémy s parameterizations dotazů, které mohou ovlivnit výkon.

Po zjištění problému snížení výkonu z několika pozorovaných metrik pomocí umělých inteligentních analýz se provede analýza. Diagnostický protokol se vygeneruje inteligentním přehledem o tom, co se děje s vaší databází. Intelligent Insights usnadňuje sledování problémů s výkonem databáze od jejich prvního vzhledu až do vyřešení. Každý zjištěný problém je sledován v rámci svého životního cyklu od prvotní detekce problému a ověření zlepšení výkonu po jeho dokončení.

![Pracovní postup analýzy výkonu databáze](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Metriky používané k měření a detekci problémů s výkonem databáze jsou založeny na době trvání dotazů, požadavcích na vypršení časového limitu, nadměrné době čekání a chybných požadavcích. Další informace o metrikách najdete v části [metriky detekce](sql-database-intelligent-insights.md#detection-metrics) v tomto dokumentu.

Identifikované SQL Database snížení výkonu se zaznamenávají do diagnostického protokolu s inteligentními záznamy, které se skládají z následujících vlastností:

| Vlastnost             | Podrobnosti              |
| :------------------- | ------------------- |
| Informace o databázi | Metadata o databázi, na které se zjistil přehled, jako je například identifikátor URI prostředku. |
| Zjištěný časový rozsah | Počáteční a koncový čas období zjištěného přehledu |
| Ovlivněné metriky | Metriky, které způsobily vygenerování přehledu: <ul><li>Zvýšení doby trvání dotazu [sekundy].</li><li>Nadměrné čekání [sekundy].</li><li>Žádosti s časovým limitem [procenta].</li><li>Chybné požadavky [procento].</li></ul>|
| Hodnota dopadu | Hodnota měřené metriky. |
| Ovlivněné dotazy a chybové kódy | Hodnota hash dotazu nebo kód chyby. Můžete je použít ke snadnému koreluji s ovlivněnými dotazy. K dispozici jsou metriky, které se skládají z zvýšení doby trvání dotazu, doba čekání, počty časových limitů nebo chybové kódy. |
| Detekce | Detekce zjištěná v databázi během doby události. K dispozici jsou 15 vzory detekce. Další informace najdete v tématu [řešení potíží s výkonem databáze pomocí Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Analýza původní příčiny | Analýza příčiny problému zjištěného v uživatelsky čitelném formátu Některé přehledy můžou obsahovat doporučení na zlepšení výkonu, pokud je to možné. |
|||

Praktické přehledy o používání Intelligent Insights s Azure SQL Analytics a o typických scénářích použití najdete ve vloženém videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights rozzjišťují a řeší problémy SQL Database problémů s výkonem. Chcete-li použít Intelligent Insights k řešení potíží s výkonem databáze SQL Database a spravované instance, přečtěte si téma [řešení potíží s výkonem Azure SQL Database s Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="configure-intelligent-insights"></a>Konfigurace Intelligent Insights

Výstupem Intelligent Insights je protokol diagnostiky inteligentního výkonu. Tento protokol můžete využívat několika způsoby – prostřednictvím streamování IT na Azure SQL Analytics, Azure Event Hubs a Azure Storage nebo produktu třetí strany.

- Pomocí produktu s [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) můžete zobrazit přehledy prostřednictvím uživatelského rozhraní Azure Portal. Toto je integrované řešení Azure a nejběžnější způsob, jak zobrazit přehledy.
- Použití produktu s Azure Event Hubs pro vývoj vlastních scénářů monitorování a upozorňování
- Použijte produkt se službou Azure Storage pro vývoj vlastních aplikací, například vlastní vytváření sestav, dlouhodobé archivace dat a tak dále.

Integrace Intelligent Insights s dalšími produkty Azure SQL Analytics, Azure Event hub, Azure Storage nebo produkty třetích stran pro spotřebu se provádí prostřednictvím prvního povolení Intelligent Insights protokolování (protokol "SQLInsights") v diagnostice. okno nastavení databáze a pak nakonfigurujte data protokolu Intelligent Insights pro streamování do jednoho z těchto produktů.

Další informace o tom, jak povolit protokolování Intelligent Insights a nakonfigurovat data protokolu pro streamování do náročného produktu, najdete v tématu [Azure SQL Database metriky a protokolování diagnostiky](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Nastavení pomocí Azure SQL Analytics

Azure SQL Analytics řešení poskytuje grafické uživatelské rozhraní, funkce vytváření sestav a upozorňování na výkon databáze spolu s daty protokolu Intelligent Insights diagnostiky.

> [!TIP]
> Rychlé zprovoznění: Nejjednodušší způsob, jak se při používání Intelligent Insights využít, je jeho použití společně s Azure SQL Analytics, která poskytuje grafické uživatelské rozhraní pro problémy s výkonem databáze. Přidejte Azure SQL Analytics řešení z Marketplace, vytvořte v tomto řešení pracovní prostor a pak pro každou databázi, kterou chcete povolit Intelligent Insights, nakonfigurujte streamování protokolu "SQLInsights" v okně nastavení diagnostiky databáze na pracovní prostor Azure SQL Analytics.
>

Představte si, že Azure SQL Analytics přidat do vašeho řídicího panelu Azure Portal z webu Marketplace a vytvořit pracovní prostor, přečtěte si téma [konfigurace Azure SQL Analytics](../azure-monitor/insights/azure-sql.md#configuration)

Pokud chcete použít Intelligent Insights s Azure SQL Analytics, nakonfigurujte Intelligent Insights data protokolu pro streamování do pracovního prostoru Azure SQL Analytics, který jste vytvořili v předchozím kroku, v tématu [Azure SQL Database metriky a protokolování diagnostiky](sql-database-metrics-diag-logging.md).

Následující příklad ukazuje Intelligent Insights zobrazení prostřednictvím Azure SQL Analytics:

![Sestava Intelligent Insights](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Nastavení pomocí Event Hubs

Pokud chcete použít Intelligent Insights s Event Hubs, nakonfigurujte Intelligent Insights data protokolu pro streamování do Event Hubs. Další informace najdete v tématu [streamování protokolů Azure Diagnostics do Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md).

Pokud chcete použít Event Hubs k nastavení vlastního monitorování a upozorňování, přečtěte si téma [co dělat s protokoly metrik a diagnostikami v Event Hubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Nastavení pomocí Azure Storage

Pokud chcete použít Intelligent Insights s úložištěm, nakonfigurujte data protokolu Intelligent Insights, která se mají streamovat do úložiště, a přečtěte si téma [streamování do Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Vlastní integrace Intelligent Insightsho protokolu

Pokud chcete použít Intelligent Insights s nástroji třetích stran nebo pro vývoj vlastních výstrah a monitorování, přečtěte si téma [použití protokolu diagnostiky výkonu Intelligent Insights databáze](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Metriky detekce

Metriky používané pro modely detekce, které generují Intelligent Insights, jsou založené na monitorování:

- Doba trvání dotazu
- Žádosti o vypršení časového limitu
- Nadměrný čas čekání
- Chybné požadavky

Při zjišťování problémů s výkonem úloh databáze se jako primární modely používají požadavky na dobu trvání dotazu a časový limit. Používají se, protože přímo měří, co se děje s úlohou. K detekci všech možných případů snížení výkonu úloh se používají nadměrné čekací doby a požadavky na chybovou práci jako další modely k označení problémů, které mají vliv na výkon úloh.

Systém automaticky bere v úvahu změny úlohy a změny v počtu požadavků na dotazy provedených v databázi, aby dynamicky určily normální prahové hodnoty výkonu a neobvyklého výkonu databáze.

Všechny metriky se v různých vztazích berou v úvahu prostřednictvím vědecky odvozeného datového modelu, který zařadí do kategorií jednotlivé zjištěné problémy s výkonem. Mezi informace poskytované prostřednictvím inteligentního přehledu patří:

- Podrobnosti zjištěného problému s výkonem.
- Zjistila se analýza problému hlavní příčinou.
- Doporučení k vylepšení výkonu monitorované databáze SQL, pokud je to možné.

## <a name="query-duration"></a>Doba trvání dotazu

Model degradování doby zpracování dotazu analyzuje jednotlivé dotazy a zjišťuje nárůst doby potřebné k zkompilování a spuštění dotazu v porovnání se směrným plánem výkonu.

Pokud SQL Database integrovaných inteligentních funkcí zjistí významné zvýšení doby spuštění dotazu nebo dotazu, která má vliv na výkon úlohy, tyto dotazy jsou označeny jako problémy s snížením výkonu v době trvání dotazu.

Protokol diagnostiky Intelligent Insights vypíše výstup hodnoty hash dotazu pro dotaz, který byl snížen ve výkonu. Hodnota hash dotazu určuje, zda bylo snížení výkonu spojeno s nárůstem nebo časem spuštění dotazu, což zvyšuje dobu trvání dotazu.

## <a name="timeout-requests"></a>Žádosti o vypršení časového limitu

Model degradování požadavků na vypršení časového limitu analyzuje jednotlivé dotazy a detekuje případné zvýšení časových limitů na úrovni spouštění dotazů a Celkový časový limit požadavku na úrovni databáze v porovnání s obdobím standardních hodnot výkonu.

U některých dotazů může docházet k vypršení časového limitu dokonce ještě před dosažením fáze spuštění. Prostřednictvím prostředků zrušených pracovníků vs. provedli jsme SQL Database integrovaných analytických opatření a analyzují všechny dotazy, které databáze dosáhly, ať už jsou ve fázi spuštění nebo ne.

Po překročení časového limitu pro provedené dotazy nebo počtu zrušených pracovních procesů, které překračují prahovou hodnotu spravovanou systémem, se protokol diagnostiky vyplní pomocí inteligentních přehledů.

Vygenerované přehledy obsahují počet požadavků s časovým limitem a počet dotazů s časovým limitem. Indikace snížení výkonu se týká zvýšení časového limitu ve fázi spuštění nebo je poskytnuta celková úroveň databáze. Pokud se zvýšení časových limitů považuje za významné pro výkon databáze, jsou tyto dotazy označeny jako problémy s snížením výkonu při vypršení časového limitu.

## <a name="excessive-wait-times"></a>Nadměrné doby čekání

Model nadměrné doby čekání sleduje jednotlivé databázové dotazy. Detekuje neobvykle vysoké statistiky čekání na dotaz, které překračují absolutní prahové hodnoty spravované systémem. Následující dotaz nenáročné metriky čekací doby jsou pozorovány pomocí nové funkce SQL Server, Statistika čekání úložiště dotazů (sys. query_store_wait_stats):

- Dosažení limitů prostředků
- Dosažení limitů prostředků elastického fondu
- Nadměrný počet vláken pracovních procesů nebo relací
- Nadměrné zamykání databáze
- Tlak paměti
- Další statistiky čekání

Dosažení limitů prostředků nebo omezení prostředků elastického fondu znamená, že spotřeba dostupných prostředků v rámci předplatného nebo elastického fondu překročila absolutní prahové hodnoty. Tyto statistiky označují snížení výkonu úloh. Nadměrný počet vláken pracovního procesu nebo relace označuje podmínku, ve které počet pracovních vláken nebo relací iniciovaly předané absolutní prahové hodnoty. Tyto statistiky označují snížení výkonu úloh.

Nadměrné uzamčení databáze označuje podmínku, ve které počet zámků v databázi má překročené absolutní prahové hodnoty. Tato stata indikuje, že je degradace výkonu úloh. Tlak paměti je stav, ve kterém počet vláken požadujících paměť uděluje překročení absolutní prahové hodnoty. Tato stata indikuje, že je degradace výkonu úloh.

Jiné zjišťování statistik čekání indikuje stav, ve kterém různé metriky měřené v rámci úložiště dotazů vycházejí z absolutní prahové hodnoty. Tyto statistiky označují snížení výkonu úloh.

Po zjištění nadměrných dob čekání v závislosti na dostupných datech vypíše protokol Diagnostika Intelligent Insights výstupy hodnot hash ovlivněných a ovlivněných dotazů, které způsobují, že dotazy čekají na spuštění, a změřená čekací doba.

## <a name="errored-requests"></a>Chybové požadavky

Model degradující chybné požadavky sleduje jednotlivé dotazy a zjišťuje nárůst počtu dotazů, u kterých došlo k chybě v porovnání se směrným obdobím. Tento model také monitoruje kritické výjimky, které překračují absolutní prahové hodnoty, které jsou spravovány SQL Database integrovanými inteligentními funkcemi. Systém automaticky vezme v úvahu počet požadavků na dotazy provedených v databázi a účtech pro jakékoli změny úloh v monitorovaném období.

Pokud se měřené zvýšení chybných požadavků vzhledem k celkovému počtu vytvořených požadavků považuje za významné pro výkon úloh, jsou ovlivněné dotazy označeny jako problémy s chybou snížení výkonu.

Protokol Intelligent Insights výstupuje počet chybných požadavků. Označuje, zda bylo snížení výkonu v souvislosti se zvýšením chybných požadavků nebo překříženým monitorovanou prahovou hodnotou kritické výjimky a změřeným časem snížení výkonu.

Pokud některá z monitorovaných kritických výjimek překročí absolutní prahové hodnoty spravované systémem, vygeneruje se inteligentní přehled s podrobnostmi o kritických výjimkách.

## <a name="next-steps"></a>Další kroky

- Naučte [se řešit problémy s výkonem SQL Database Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Použijte [protokol Intelligent Insights SQL Database Performance Diagnostics](sql-database-intelligent-insights-use-diagnostics-log.md).
- Naučte se [monitorovat SQL Database pomocí SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Naučte se [shromažďovat a využívat data protokolu z vašich prostředků Azure](../azure-monitor/platform/resource-logs-overview.md).
