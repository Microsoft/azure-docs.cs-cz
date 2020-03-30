---
title: Sledování výkonu databáze pomocí inteligentních přehledů
description: Azure SQL Database Intelligent Insights využívá integrovanou inteligenci k průběžnému sledování využití databáze prostřednictvím umělé inteligence a zjišťování rušivých událostí, které způsobují nízký výkon.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d7b9ada17871dc7882209b7a8a449a8edcd61a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214074"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Inteligentní přehledy využívající umělou inteligenci ke sledování a odstraňování problémů s výkonem databáze (Preview)

Inteligentní přehledy Azure SQL Database vám umožní zjistit, co se děje s výkonem databáze.

Inteligentní přehledy využívají integrovanou inteligenci k průběžnému sledování využití databáze prostřednictvím umělé inteligence a detekci rušivých událostí, které způsobují nízký výkon. Po zjištění se provede podrobná analýza, která generuje protokol prostředků Intelligent Insights (nazývaný SQLInsights) s inteligentním vyhodnocením problému. Toto posouzení se skládá z analýzy hlavní příčiny problému s výkonem databáze a pokud je to možné, doporučení pro zlepšení výkonu.

## <a name="what-can-intelligent-insights-do-for-you"></a>Co pro vás inteligentní přehledy mohou udělat

Intelligent Insights je jedinečná funkce integrované inteligence Azure, která poskytuje následující hodnotu:

- Proaktivní monitorování
- Přehledy výkonu na míru
- Včasná detekce snížení výkonu databáze
- Analýza hlavní příčiny zjištěných problémů
- Doporučení ke zlepšení výkonu
- Škálování možností na stovkách tisíc databází
- Pozitivní dopad na zdroje DevOps a celkové náklady na vlastnictví

## <a name="how-does-intelligent-insights-work"></a>Jak inteligentní přehledy fungují

Inteligentní přehledy analyzuje výkon databáze porovnáním zatížení databáze z poslední hodiny s poslední sedmidenní směrné zatížení. Zatížení databáze se skládá z dotazů určených jako nejvýznamnější pro výkon databáze, jako jsou například nejvíce opakované a největší dotazy. Vzhledem k tomu, že každá databáze je jedinečná na základě své struktury, dat, využití a aplikace, je každý směrný plán úlohy, který je generován, specifický a jedinečný pro tuto úlohu. Inteligentní přehledy, nezávislé na směrné době pracovního vytížení, také monitoruje absolutní provozní prahové hodnoty a detekuje problémy s nadměrnými čekacími dobami, kritickými výjimkami a problémy s parametrizací dotazů, které mohou ovlivnit výkon.

Po zhoršení výkonu problém je zjištěn z více pozorovaných metrik pomocí umělé inteligence, analýza se provádí. Protokol diagnostiky je generován s inteligentní přehled o tom, co se děje s vaší databází. Inteligentní přehledy usnadňují sledování problému s výkonem databáze od prvního vzhledu až po vyřešení. Každý zjištěný problém je sledován v průběhu jeho životního cyklu od počátečního zjištění problému a ověření zlepšení výkonu až po jeho dokončení.

![Pracovní postup analýzy výkonu databáze](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Metriky používané k měření a zjišťování problémů s výkonem databáze jsou založeny na době trvání dotazu, požadavcích na časový limit, nadměrných čekacích dobách a chybných požadavcích. Další informace o metrikách naleznete v [tématu Metriky zjišťování](#detection-metrics).

Identifikované snížení výkonu databáze SQL jsou zaznamenány v protokolu SQLInsights s inteligentními položkami, které se skládají z následujících vlastností:

| Vlastnost | Podrobnosti |
| :------------------- | ------------------- |
| Informace o databázi | Metadata o databázi, ve které byl zjištěn přehled, například identifikátor URI prostředku. |
| Pozorovaný časový rozsah | Počáteční a koncový čas pro období zjištěného přehledu. |
| Ovlivněné metriky | Metriky, které způsobily generování přehledu: <ul><li>Prodloužení doby trvání dotazu [sekund].</li><li>Nadměrné čekání [sekund].</li><li>Požadavky na časový odpočet [procento].</li><li>Chybové požadavky [procento].</li></ul>|
| Hodnota dopadu | Hodnota měřené metriky. |
| Ovlivněné dotazy a kódy chyb | Kód hash dotazu nebo kód chyby. Ty lze snadno korelovat s ovlivněné dotazy. Metriky, které se skládají buď z prodloužení doby trvání dotazu, čekací doby, počtu časového limitu nebo kódů chyb. |
| Detekcí | Detekce identifikovaná v databázi v době události. Existuje 15 detekčních vzorů. Další informace naleznete [v tématu Poradce při potížích s výkonem databáze pomocí inteligentních přehledů](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Analýza původní příčiny | Analýza hlavní příčiny problému identifikovaného v formátu čitelném pro člověka. Některé přehledy mohou obsahovat doporučení ke zlepšení výkonu, kde je to možné. |
|||

Praktický přehled o používání inteligentních přehledů s Azure SQL Analytics a o typických scénářích využití najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Inteligentní přehledy září při zjišťování a řešení problémů s výkonem databáze SQL. Chcete-li k řešení problémů s výkonem databáze používat inteligentní přehledy, [přečtěte si článek Řešení potíží s výkonem databáze Azure SQL pomocí funkce Inteligentní přehledy](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Možnosti inteligentních přehledů

Možnosti inteligentních přehledů dostupné v Azure SQL Database jsou:

| Možnost Inteligentní přehledy | Podpora jedné databáze a sdružených databází | Podpora databáze instancí |
| :----------------------------- | ----- | ----- |
| **Konfigurace inteligentních přehledů** – Konfigurace analýzy inteligentních přehledů pro vaše databáze. | Ano | Ano |
| **Streamujte přehledy do Azure SQL Analytics** – streamujte přehledy do řešení monitorování Azure SQL Analytics pro Azure SQL Database. | Ano | Ano |
| **Streamujte přehledy do Centra událostí** – streamujte přehledy do centra událostí pro další vlastní integrace. | Ano | Ano |
| **Streamujte přehledy do Azure Storage** – streamujte přehledy do Azure Storage pro další analýzu a dlouhodobou archivaci. | Ano | Ano |

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Konfigurace exportu protokolu Inteligentní přehledy

Výstup inteligentních přehledů lze streamovat do jednoho z několika cílů pro analýzu:

- Výstup vysílaný do pracovního prostoru Log Analytics lze použít s [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) k zobrazení přehledů prostřednictvím uživatelského rozhraní portálu Azure. Toto je integrované řešení Azure a nejtypičtější způsob zobrazení přehledů.
- Výstup streamovaný do Centra událostí Azure se dá použít pro vývoj vlastních scénářů monitorování a upozorňování.
- Výstup vysílaný do Azure Storage se dá použít pro vývoj vlastních aplikací, například vlastní vytváření sestav, dlouhodobá archivace dat a tak dále.

Integrace Azure SQL Analytics, Azure Event Hub, Azure Storage nebo produktů třetích stran ke spotřebě se provádí prostřednictvím prvního povolení protokolování inteligentních přehledů (protokol "SQLInsights") v okně Nastavení diagnostiky databáze a potom konfigurace dat protokolu Intelligent Insights pro datové proudy do jednoho z těchto cílů.

Další informace o tom, jak povolit protokolování inteligentních přehledů a nakonfigurovat data protokolů metrik a prostředků, které mají být streamovány do náročného produktu, najdete v [tématu metriky azure sql database a protokolování diagnostiky](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Nastavení pomocí Azure SQL Analytics

Řešení Azure SQL Analytics poskytuje grafické uživatelské rozhraní, vytváření sestav a výstražné funkce pro výkon databáze pomocí dat protokolu prostředků Intelligent Insights.

Přidání Azure SQL Analytics na řídicí panel portálu Azure z tržiště a vytvoření pracovního prostoru najdete v [tématu konfigurace Azure SQL Analytics](../azure-monitor/insights/azure-sql.md#configuration)

Pokud chcete pomocí inteligentních přehledů s Azure SQL Analytics nakonfigurovat data protokolu Inteligentní přehledy tak, aby se streamovala do pracovního prostoru Azure SQL Analytics, který jste vytvořili v předchozím kroku, přečtěte si [informace o metrikách Azure SQL Database a protokolování diagnostiky](sql-database-metrics-diag-logging.md).

Následující příklad ukazuje inteligentní přehledy zobrazené prostřednictvím Azure SQL Analytics:

![Sestava Inteligentní přehledy](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Nastavení pomocí centra událostí

Pokud chcete inteligentní přehledy používat s centry událostí, nakonfigurujte data protokolu Intelligent Insights, která se mají streamovat do centra událostí, přečtěte si [informace o metrikách Azure DATABASE a protokolech diagnostiky diagnostiky](sql-database-metrics-diag-logging.md) a [protokolech diagnostiky Stream Azure do center událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md).

Pokud chcete pomocí centra událostí nastavit vlastní monitorování a výstrahy, přečtěte si informace o [tom, co dělat s protokoly metrik a diagnostiky v centru událostí](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Nastavení pomocí Azure Storage

Pokud chcete používat Inteligentní přehledy s úložištěm, nakonfigurujte data protokolu Intelligent Insights, která se mají streamovat do úložiště, přečtěte si informace [o metrikách Azure SQL Database a protokolování diagnostiky](sql-database-metrics-diag-logging.md) a [streamování do Úložiště Azure](sql-database-metrics-diag-logging.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Vlastní integrace protokolu Inteligentní přehledy

Informace o použití inteligentních přehledů s nástroji třetích stran nebo o vývoji vlastních výstrah a monitorování naleznete [v tématu Použití protokolu diagnostiky výkonu databáze Inteligentní přehledy](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Metriky zjišťování

Metriky používané pro modely detekce, které generují inteligentní přehledy, jsou založeny na monitorování:

- Doba trvání dotazu
- Požadavky na časový opojení
- Nadměrná čekací doba
- Chybované požadavky

Požadavky na dobu trvání dotazu a časový limit se používají jako primární modely při zjišťování problémů s výkonem zatížení databáze. Používají se, protože přímo měří, co se děje s pracovní zátěží. Chcete-li zjistit všechny možné případy snížení výkonu pracovního vytížení, nadměrné čekací doby a chybované požadavky se používají jako další modely k označení problémů, které ovlivňují výkon pracovního vytížení.

Systém automaticky bere v úvahu změny pracovního vytížení a změny v počtu požadavků na dotazy v databázi dynamicky určit normální a neobvyklé prahové hodnoty výkonu databáze.

Všechny metriky jsou považovány za společně v různých vztazích prostřednictvím vědecky odvozené datový model, který kategorizuje každý problém s výkonem zjištěných. Informace poskytované prostřednictvím inteligentního přehledu zahrnují:

- Podrobnosti o zjištěném problému s výkonem.
- Analýza hlavní příčiny zjištěného problému.
- Doporučení, jak zlepšit výkon monitorované databáze SQL, pokud je to možné.

## <a name="query-duration"></a>Doba trvání dotazu

Model degradace doby trvání dotazu analyzuje jednotlivé dotazy a detekuje zvýšení doby potřebné ke kompilaci a spuštění dotazu ve srovnání se směrným plánem výkonu.

Pokud sql database integrovaná inteligence zjistí významné zvýšení kompilace dotazu nebo čas spuštění dotazu, který ovlivňuje výkon pracovního vytížení, tyto dotazy jsou označeny jako problémy s snížením výkonu trvání dotazu.

Protokol diagnostiky Inteligentní přehledy vyhlásí hodnotu hash dotazu z hlediska výkonu. Hodnota hash dotazu označuje, zda snížení výkonu souviselo s kompilací dotazu nebo prodloužením doby spuštění, což prodloužilo dobu trvání dotazu.

## <a name="timeout-requests"></a>Požadavky na časový opojení

Časový limit požaduje snížení modelu analyzuje jednotlivé dotazy a zjistí jakékoli zvýšení časové limity na úrovni spuštění dotazu a celkové časové limity požadavku na úrovni databáze ve srovnání s obdobím směrného plánu výkonu.

Některé dotazy může časový modiše ještě před jejich dosažením fáze spuštění. Prostřednictvím prostředků přerušené pracovníky vs požadavky, SQL Database integrované inteligentní opatření a analyzuje všechny dotazy, které dosáhly databáze, zda se dostali do fáze provádění nebo ne.

Po počet časových limitů pro provedené dotazy nebo počet přerušených požadavků pracovníků překročí prahovou hodnotu spravované systémem, protokol diagnostiky je naplněn inteligentní přehledy.

Generované přehledy obsahují počet požadavků na časový rozsah a počet dotazů na časový rozsah. Indikace snížení výkonu souvisí s prodloužením časového oběhem ve fázi provádění nebo je k dispozici celková úroveň databáze. Při zvýšení časové osy je považováno za významné pro výkon databáze, tyto dotazy jsou označeny jako problémy snížení výkonu časového opona.

## <a name="excessive-wait-times"></a>Nadměrné čekací doby

Model nadměrné čekací doby monitoruje jednotlivé databázové dotazy. Detekuje neobvykle vysoké statistiky čekání dotazů, které překročily absolutní prahové hodnoty spravované systémem. Následující dotaz nadměrné čekací doby metriky jsou pozorovány pomocí nové funkce SQL Server, Dotaz Store Wait Statistiky (sys.query_store_wait_stats):

- Dosažení limitů prostředků
- Dosažení limitů prostředků elastického fondu
- Nadměrný počet podprocesů pracovníka nebo relace
- Nadměrné zamykání databáze
- Přetížení paměti
- Další statistiky čekání

Dosažení omezení prostředků nebo omezení prostředků elastického fondu označují, že spotřeba dostupných prostředků v předplatném nebo v elastickém fondu překročila absolutní prahové hodnoty. Tyto statistiky označují snížení výkonu pracovního vytížení. Nadměrný počet podprocesů pracovníka nebo relace označuje podmínku, ve které počet pracovních podprocesů nebo zahájených relací překročil absolutní prahové hodnoty. Tyto statistiky označují snížení výkonu pracovního vytížení.

Nadměrné uzamčení databáze označuje podmínku, ve které počet zámků v databázi překročil absolutní prahové hodnoty. Tento stav označuje snížení výkonu pracovního vytížení. Tlak paměti je podmínka, ve které počet podprocesů požadujících přidělení paměti překročil absolutní prahovou hodnotu. Tento stav označuje snížení výkonu pracovního vytížení.

Další zjišťování statistik čekání označuje podmínku, ve které různé metriky měřené prostřednictvím statistikčekání úložiště dotazů překročily absolutní prahovou hodnotu. Tyto statistiky označují snížení výkonu pracovního vytížení.

Po nadměrné čekací doby jsou zjištěny, v závislosti na dostupných dat, inteligentní insights výstupy protokolu protokolu hash ovlivňující a ovlivněné dotazy snížena výkonu, podrobnosti o metriky, které způsobují dotazy čekat při spuštění a měřenou dobu čekání.

## <a name="errored-requests"></a>Chybové požadavky

Chybové požadavky na model degradace monitoruje jednotlivé dotazy a zjistí zvýšení počtu dotazů, které došlo k chybě ve srovnání s obdobím směrného plánu. Tento model také monitoruje kritické výjimky, které překročily absolutní prahové hodnoty spravované integrovanou inteligencí SQL Database. Systém automaticky zohlední počet požadavků na dotazy do databáze a účty pro všechny změny pracovního vytížení ve sledovaném období.

Pokud je naměřené zvýšení chybových požadavků vzhledem k celkovému počtu požadavků považováno za významné pro výkon pracovního vytížení, jsou ovlivněné dotazy označeny jako chybové požadavky na snížení výkonu.

Protokol Intelligent Insights vyhlásí počet chybovaných požadavků. Označuje, zda snížení výkonu souviselo se zvýšením chybových požadavků nebo překročením sledované prahové hodnoty kritické výjimky a měřené doby snížení výkonu.

Pokud některá ze sledovaných kritických výjimek překročí absolutní prahové hodnoty spravované systémem, vygeneruje se inteligentní přehled s důležitými podrobnostmi o výjimce.

## <a name="next-steps"></a>Další kroky

- Naučte se [sledovat databázi SQL pomocí služby SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Přečtěte si, jak [řešit problémy s výkonem databáze SQL pomocí aplikace Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
