---
title: Monitorování výkonu databáze pomocí Intelligent Insights
description: Intelligent Insights v Azure SQL Database a Azure SQL Managed instance používá integrované inteligentní funkce k nepřetržitému monitorování využití databáze prostřednictvím umělých inteligentních funkcí a detekci rušivých událostí, které způsobují špatný výkon.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 61033e3eb8264c1e462faac3e4553a855a1d06c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592073"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligent Insights používání AI k monitorování a řešení potíží s výkonem databáze (Preview)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Intelligent Insights v Azure SQL Database a Azure SQL Managed instance vám umožní zjistit, co se děje s výkonem vaší databáze.

Intelligent Insights pomocí integrovaných inteligentních funkcí průběžně monitoruje využití databáze prostřednictvím umělých inteligentních funkcí a detekuje rušivé události, které způsobují špatný výkon. Po zjištění se provede podrobná analýza, která generuje protokol prostředků Intelligent Insights (nazývaný SQLInsights) s inteligentním posouzením problému. Toto posouzení se skládá z analýzy hlavní příčiny problémů s výkonem databáze a tam, kde je to možné, doporučení pro zlepšení výkonu.

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

Intelligent Insights analyzuje výkon databáze porovnáním databázového zatížení za poslední hodinu a poslední sedm dní. Databázové úlohy se skládají z dotazů, které jsou pro výkon databáze nejdůležitější, jako je například nejpravděpodobnější a největší počet dotazů. Vzhledem k tomu, že každá databáze je jedinečná na základě její struktury, dat, využití a aplikace, jednotlivé standardní hodnoty zatížení jsou specifické a jedinečné pro tuto úlohu. Intelligent Insights, nezávisle na standardních hodnotách úloh, monitoruje také absolutní provozní prahové hodnoty a detekuje problémy s nadměrnými čekacími dobami, kritickými výjimkami a problémy s parameterizations dotazů, které mohou ovlivnit výkon.

Po zjištění problému snížení výkonu z několika pozorovaných metrik pomocí umělých inteligentních analýz se provede analýza. Diagnostický protokol se vygeneruje inteligentním přehledem o tom, co se děje s vaší databází. Intelligent Insights usnadňuje sledování problémů s výkonem databáze od jejich prvního vzhledu až do vyřešení. Každý zjištěný problém je sledován v rámci svého životního cyklu od prvotní detekce problému a ověření zlepšení výkonu po jeho dokončení.

![Pracovní postup analýzy výkonu databáze](./media/intelligent-insights-overview/intelligent-insights-concept.png)

Metriky používané k měření a detekci problémů s výkonem databáze jsou založeny na době trvání dotazů, požadavcích na vypršení časového limitu, nadměrné době čekání a chybných požadavcích. Další informace o metrikách najdete v tématu [metriky detekce](#detection-metrics).

Zjištěné snížení výkonu databáze se zaznamenávají do protokolu SQLInsights s inteligentními záznamy, které se skládají z následujících vlastností:

| Vlastnost | Podrobnosti |
| :------------------- | ------------------- |
| Informace o databázi | Metadata o databázi, na které se zjistil přehled, jako je například identifikátor URI prostředku. |
| Zjištěný časový rozsah | Počáteční a koncový čas období zjištěného přehledu |
| Ovlivněné metriky | Metriky, které způsobily vygenerování přehledu: <ul><li>Zvýšení doby trvání dotazu [sekundy].</li><li>Nadměrné čekání [sekundy].</li><li>Žádosti s časovým limitem [procenta].</li><li>Chybné požadavky [procento].</li></ul>|
| Hodnota dopadu | Hodnota měřené metriky. |
| Ovlivněné dotazy a chybové kódy | Hodnota hash dotazu nebo kód chyby. Můžete je použít ke snadnému koreluji s ovlivněnými dotazy. K dispozici jsou metriky, které se skládají z zvýšení doby trvání dotazu, doba čekání, počty časových limitů nebo chybové kódy. |
| Detekce | Detekce zjištěná v databázi během doby události. K dispozici jsou 15 vzory detekce. Další informace najdete v tématu [řešení potíží s výkonem databáze pomocí Intelligent Insights](intelligent-insights-troubleshoot-performance.md). |
| Analýza původní příčiny | Analýza příčiny problému zjištěného v uživatelsky čitelném formátu Některé přehledy můžou obsahovat doporučení na zlepšení výkonu, pokud je to možné. |
|||

Praktické přehledy o používání Intelligent Insights s Azure SQL Analytics a o typických scénářích použití najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights rozzjišťují a řeší problémy s výkonem databáze. Chcete-li použít Intelligent Insights k řešení potíží s výkonem databáze, přečtěte si téma [řešení potíží s výkonem pomocí Intelligent Insights](intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Možnosti Intelligent Insights

Dostupné možnosti Intelligent Insights jsou:

| Možnost Intelligent Insights | Podpora Azure SQL Database | Podpora spravované instance Azure SQL |
| :----------------------------- | ----- | ----- |
| **Nakonfigurujte Intelligent Insights** – nakonfigurujte Intelligent Insights analýzy pro vaše databáze. | Yes | Yes |
| **Stream Insights do Azure SQL Analytics** --Stream insights pro Azure SQL Analytics. | Yes | Yes |
| **Stream Insights do Azure Event Hubs** -Stream insights pro Event Hubs pro další vlastní integrace. | Yes | Yes |
| **Stream Insights do Azure Storage** -Stream Insights, které Azure Storage k další analýze a dlouhodobé archivaci. | Yes | Yes |

> [!NOTE]
> Inteligentní přehledy jsou funkce ve verzi Preview, která není dostupná v následujících oblastech: Západní Evropa, Severní Evropa, Západní USA 1 a Východní USA 1.

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Konfigurace exportu protokolu Intelligent Insights

Výstup Intelligent Insights může být streamování do jednoho z několika cílů pro účely analýzy:

- Výstup streamování do Log Analyticsho pracovního prostoru se dá použít s [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) k zobrazení přehledů prostřednictvím uživatelského rozhraní Azure Portal. Toto je integrované řešení Azure a nejběžnější způsob, jak zobrazit přehledy.
- Výstup streamování do Azure Event Hubs se dá použít pro vývoj vlastních scénářů monitorování a upozorňování.
- Výstup streamování do Azure Storage lze použít pro vlastní vývoj aplikací, například pro vlastní vytváření sestav, dlouhodobé archivaci dat a tak dále.

Integrace Azure SQL Analytics, Azure Event Hubs, Azure Storage nebo produktů třetích stran pro účely spotřeby se provádí prostřednictvím prvního povolení protokolu Intelligent Insights (protokol "SQLInsights") v okně nastavení diagnostiky databáze a následnou konfigurací Intelligent Insights dat protokolu pro streamování do jednoho z těchto cílů.

Další informace o tom, jak povolit protokolování Intelligent Insights a jak nakonfigurovat data metrik a protokolu prostředků pro streamování do náročného produktu, najdete v tématu [metriky a protokolování diagnostiky](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

### <a name="set-up-with-azure-sql-analytics"></a>Nastavení pomocí Azure SQL Analytics

Azure SQL Analytics řešení poskytuje grafické uživatelské rozhraní, funkce vytváření sestav a upozorňování na výkon databáze pomocí dat protokolu Intelligent Insights prostředků.

Přidání Azure SQL Analytics k řídicímu panelu Azure Portal z webu Marketplace a vytvoření pracovního prostoru najdete v tématu [konfigurace Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md#configuration)

Pokud chcete použít Intelligent Insights s Azure SQL Analytics, nakonfigurujte Intelligent Insights data protokolu pro streamování do pracovního prostoru Azure SQL Analytics, který jste vytvořili v předchozím kroku, v tématu [metriky a protokolování diagnostiky](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

Následující příklad ukazuje Intelligent Insights zobrazení prostřednictvím Azure SQL Analytics:

![Sestava Intelligent Insights](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Nastavení pomocí Event Hubs

Pokud chcete použít Intelligent Insights s Event Hubs, nakonfigurujte Intelligent Insights data protokolu pro streamování do Event Hubs, viz [metriky a protokolování diagnostiky](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) a [streamování protokolů Azure diagnostics na Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

Pokud chcete použít Event Hubs k nastavení vlastního monitorování a upozorňování, přečtěte si téma [co dělat s protokoly metrik a diagnostikami v Event Hubs](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Nastavení pomocí Azure Storage

Pokud chcete použít Intelligent Insights s úložištěm, nakonfigurujte data protokolu Intelligent Insights, která se mají streamovat do úložiště, v tématu [metriky a diagnostické protokolování](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) a [Stream do Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Vlastní integrace Intelligent Insightsho protokolu

Pokud chcete použít Intelligent Insights s nástroji třetích stran nebo pro vývoj vlastních výstrah a monitorování, přečtěte si téma [použití protokolu diagnostiky výkonu Intelligent Insights databáze](intelligent-insights-use-diagnostics-log.md).

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
- Doporučení pro zlepšení výkonu monitorované databáze, pokud je to možné.

## <a name="query-duration"></a>Doba trvání dotazu

Model degradování doby zpracování dotazu analyzuje jednotlivé dotazy a zjišťuje nárůst doby potřebné k zkompilování a spuštění dotazu v porovnání se směrným plánem výkonu.

Pokud integrované funkce inteligentních funkcí zjistí významné zvýšení doby provádění dotazů nebo dotazování, které mají vliv na výkon úloh, tyto dotazy jsou označeny jako problémy s snížením výkonu v době trvání dotazů.

Protokol diagnostiky Intelligent Insights vypíše výstup hodnoty hash dotazu pro dotaz, který byl snížen ve výkonu. Hodnota hash dotazu určuje, zda bylo snížení výkonu spojeno s nárůstem nebo časem spuštění dotazu, což zvyšuje dobu trvání dotazu.

## <a name="timeout-requests"></a>Žádosti o vypršení časového limitu

Model degradování požadavků na vypršení časového limitu analyzuje jednotlivé dotazy a detekuje případné zvýšení časových limitů na úrovni spouštění dotazů a Celkový časový limit požadavku na úrovni databáze v porovnání s obdobím standardních hodnot výkonu.

U některých dotazů může docházet k vypršení časového limitu dokonce ještě před dosažením fáze spuštění. Prostřednictvím prostředků zrušených pracovníků vs. provedených požadavků a analyzuje všechny dotazy, které databáze dorazily, ať už jsou ve fázi spuštění nebo ne.

Po překročení časového limitu pro provedené dotazy nebo počtu zrušených pracovních procesů, které překračují prahovou hodnotu spravovanou systémem, se protokol diagnostiky vyplní pomocí inteligentních přehledů.

Vygenerované přehledy obsahují počet požadavků s časovým limitem a počet dotazů s časovým limitem. Indikace snížení výkonu se týká zvýšení časového limitu ve fázi spuštění nebo je poskytnuta celková úroveň databáze. Pokud se zvýšení časových limitů považuje za významné pro výkon databáze, jsou tyto dotazy označeny jako problémy s snížením výkonu při vypršení časového limitu.

## <a name="excessive-wait-times"></a>Nadměrné doby čekání

Model nadměrné doby čekání sleduje jednotlivé databázové dotazy. Detekuje neobvykle vysoké statistiky čekání na dotaz, které překračují absolutní prahové hodnoty spravované systémem. Následující dotaz nenáročné metriky čekací doby v [úložišti dotazů (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql):

- Dosažení limitů prostředků
- Dosažení limitů prostředků elastického fondu
- Nadměrný počet vláken pracovních procesů nebo relací
- Nadměrné zamykání databáze
- Přetížení paměti
- Další statistiky čekání

Dosažení limitů prostředků nebo omezení prostředků elastického fondu znamená, že spotřeba dostupných prostředků v rámci předplatného nebo elastického fondu překročila absolutní prahové hodnoty. Tyto statistiky označují snížení výkonu úloh. Nadměrný počet vláken pracovního procesu nebo relace označuje podmínku, ve které počet pracovních vláken nebo relací iniciovaly předané absolutní prahové hodnoty. Tyto statistiky označují snížení výkonu úloh.

Nadměrné uzamčení databáze označuje podmínku, ve které počet zámků v databázi má překročené absolutní prahové hodnoty. Tato stata indikuje, že je degradace výkonu úloh. Tlak paměti je stav, ve kterém počet vláken požadujících paměť uděluje překročení absolutní prahové hodnoty. Tato stata indikuje, že je degradace výkonu úloh.

Jiné zjišťování statistik čekání indikuje stav, ve kterém různé metriky měřené v rámci úložiště dotazů vycházejí z absolutní prahové hodnoty. Tyto statistiky označují snížení výkonu úloh.

Po zjištění nadměrných čekacích dob v závislosti na dostupných datech vypíše protokol Diagnostika Intelligent Insights výstupy hodnot hash ovlivněných a ovlivněných dotazů, které způsobují, že dotazy čekají na spuštění a naměřenou dobu čekání.

## <a name="errored-requests"></a>Chybové požadavky

Model degradující chybné požadavky sleduje jednotlivé dotazy a zjišťuje nárůst počtu dotazů, u kterých došlo k chybě v porovnání se směrným obdobím. Tento model také monitoruje kritické výjimky, které překračují absolutní prahové hodnoty spravované integrovanými inteligentními funkcemi. Systém automaticky vezme v úvahu počet požadavků na dotazy provedených v databázi a účtech pro jakékoli změny úloh v monitorovaném období.

Pokud se měřené zvýšení chybných požadavků vzhledem k celkovému počtu vytvořených požadavků považuje za významné pro výkon úloh, jsou ovlivněné dotazy označeny jako problémy s chybou snížení výkonu.

Protokol Intelligent Insights výstupuje počet chybných požadavků. Označuje, zda bylo snížení výkonu v souvislosti se zvýšením chybných požadavků nebo překříženým monitorovanou prahovou hodnotou kritické výjimky a změřeným časem snížení výkonu.

Pokud některá z monitorovaných kritických výjimek překročí absolutní prahové hodnoty spravované systémem, vygeneruje se inteligentní přehled s podrobnostmi o kritických výjimkách.

## <a name="next-steps"></a>Další kroky

- Naučte se [monitorovat databáze pomocí SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Naučte [se řešit potíže s výkonem pomocí Intelligent Insights](intelligent-insights-troubleshoot-performance.md).