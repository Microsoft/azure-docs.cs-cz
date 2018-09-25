---
title: Na základě protokolů a předem agregovaných metrik ve službě Azure Application Insights | Dokumentace Microsoftu
description: Proč použít na základě protokolů a předem agregovaných metrik ve službě Azure Application Insights
services: application-insights
keywords: ''
author: vgorbenko
ms.author: vitaly.gorbenko
ms.reviewer: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 136279a94d64b846247c436eb7a5fb487e05b41a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946897"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Na základě protokolů a předem agregovaných metrik ve službě Application Insights

Tento článek vysvětluje rozdíl mezi "tradiční" Application Insights metriky, které jsou založeny na protokoly a předem agregovaných metrik, které jsou aktuálně ve verzi public preview. Oba typy metriky jsou k dispozici pro uživatele služby Application Insights a přináší každou jedinečnou hodnotu v monitorování stavu aplikace, diagnostiku a analýzy. Vývojáři, kteří jsou instrumentace aplikací můžete rozhodnout, jaký typ metrika je nejlepší vhodné pro určitý scénář, v závislosti na velikosti aplikace, očekávaného objemu telemetrických dat a podnikových požadavků pro metriky přesnosti a upozorňování.

## <a name="log-based-metrics"></a>Metriky založené na protokolu

Dokud se nedávno, monitorování telemetrie datového modelu ve službě Application Insights aplikace byl výhradně založen na malý počet předdefinovaných typů událostí, jako jsou požadavky, výjimky, volání závislostí, zobrazení stránek, atd. Vývojáři můžou pomocí sady SDK tyto události buď vygenerovat ručně (napsáním kódu, který explicitně volá sada SDK) nebo můžou spolehnout na automatické shromažďování událostí z instrumentace automaticky. V obou případech se back-endu služby Application Insights ukládá všechny shromážděné události zaznamená jako protokoly a okna Application Insights na webu Azure Portal fungují jako nástroj pro analýzu a diagnostiky pro vizualizaci dat založené na události z protokolů.

Pomocí protokolů Pokud chcete zachovat kompletní sadu událostí můžou přinést větší hodnotu analýzy a diagnostiku. Například můžete získat přesný počet požadavků na konkrétní adresu URL s počtem jedinečných uživatelů, kteří provedli těchto volání. Nebo můžete získat podrobné diagnostické trasování, včetně výjimek a volání závislostí pro všechny uživatelské relace. Máte tento typ informací může výrazně zlepšit přehled o stavu aplikace a využití, tak snížení doby potřebné k diagnostice problémů s aplikací. 

Ve stejnou dobu shromažďování úplnou sadu událostí, které může být nepraktické (nebo dokonce nemožné) pro aplikace, která generují velké množství telemetrie. Pro situace, když objem událostí je příliš vysoká, Application Insights implementuje několik technik snížení volume telemetrii, jako [vzorkování](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) a [filtrování](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) , snižte počet shromážděných a uložených událostí. Bohužel snížení počtu uložených událostí také zmenšuje prostor nemusí být metriky, které na pozadí, musí provést doba dotazu agregací uložené v protokolech událostí.

> [!NOTE]
> Ve službě Application Insights se nazývají metriky, které jsou založeny na době dotazu agregaci událostí a měření uložené v protokolech metriky založené na protokolu. Tyto metriky obvykle mají mnoho dimenzí z vlastnosti události, které je vynikající pro analýzy, ale nemusí být tyto metriky bude negativně ovlivněna pomocí vzorkování a filtrování.

## <a name="pre-aggregated-metrics"></a>Předem agregovaných metrik

Kromě metriky založené na protokolu podzim 2018 se dodávají týmu Application Insights verze public preview služby metriky, které jsou uloženy v úložišti specializované, která je optimalizovaná pro časové řady. Nové metriky již nejsou zachovány jako jednotlivé události s velkým množstvím vlastnosti. Místo toho se ukládají jako předem agregovat časové řady a pouze s klíče dimenzí. Díky tomu nové metriky vynikající v době zpracování dotazu: načítání dat se stane mnohem rychleji a vyžaduje méně výpočetní výkon. To v důsledku umožňuje nové scénáře, jako například [téměř v reálném čase upozorňuje na ně dimenze metrik](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)rychleji reagující [řídicí panely](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards)a provádění dalších akcí.

> [!IMPORTANT]
> Metriky založené na protokolech i předem agregovat existovat vedle sebe ve službě Application Insights. K rozlišení dvou ve službě Application Insights UX předem agregovaná metrika se teď nazývají "Standardní metriky (preview)", přestože tradiční metriky z události, které byly přejmenovány na "metriky založené na protokolu".

Novější sady SDK ([Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK nebo novější pro .NET) rozjíždí předem agregovaná metrika během shromažďování před techniky snížení volume telemetrii. To znamená, že nemusí být nové metriky není ovlivněn vzorkování a filtrování při použití nejnovější sady Application Insights SDK.

Pro sady SDK, které Neimplementujte předběžnou agregací (to znamená, starší verze sady SDK služby Application Insights nebo pro prohlížeč instrumentace) na back-endu služby Application Insights stále naplní nové metriky na základě agregace událostí přijatých aplikací Koncový bod shromažďování událostí insights. To znamená, že když nechcete využívat snížení objemu dat přenášených po lince, můžete stále použít předem agregovaných metrik a prostředí lepší výkon a podporu téměř v reálném čase multidimenzionálním výstrah pomocí sad SDK, které ji nemají předem agregovaná metrika během shromažďování.

Stojí za zmínku, že koncový bod shromažďování předem agreguje událostí před vzorkování příjmu, což znamená, že [vzorkování příjmu](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) se nikdy dopad přesnost předem agregovaných metrik, bez ohledu na verzi sady SDK můžete použijte ve vaší aplikaci.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Předběžnou agregací pomocí vlastní metriky v Application Insights

Předběžnou agregací můžete použít s vlastní metriky. Dvě hlavní výhody se možnost nakonfigurovat a zobrazit výstrahu v dimenzi vlastních metrik a snižuje objem dat odesílaných ze sady SDK do koncového bodu kolekce Application Insights.

Existuje několik [způsoby, jak odeslat vlastní metriky v Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Pokud vaše verze sady SDK nabízí [GetMetric a TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) metody, jde preferovaný způsob posílá vlastní metriky, protože v tomto případě se stane předběžnou agregací v sadě SDK, nejen snížení objemu dat uložených v Azure, ale také objem dat přenášených ze sady SDK pro Application Insights. Jinak použijte [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) metodu, která předem agregovat metriky události při ingestování.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Vlastní metriky dimenze a předběžnou agregací

Všechny metriky, které jste odeslali pomocí [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) nebo [GetMetric a TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) volání rozhraní API se automaticky uložená v úložištích protokoly a metriky. Zatímco verze založené na protokolech vlastní metrika vždy uchovává všechny dimenze, předem agregovat verzi metriku uložené ve výchozím nastavení se žádné dimenze. Můžete zapnout v kolekci dimenzí vlastní metriky [využití a odhadované náklady](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) kartu tak, že zkontrolujete "Povolit výstrahy na vlastní metriky dimenze": 

![Využití a odhadované náklady](.\media\pre-aggregated-metrics-log-metrics\001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Proč je kolekce vlastních metrik dimenzí vypnuto ve výchozím nastavení?

Kolekce vlastních metrik dimenze je ve výchozím nastavení vypnuta vzhledem k tomu, že v budoucnu ukládání vlastní metriky s dimenzemi se budou účtovat zvlášť ze služby Application Insights ukládání-rozměrné vlastní metriky se budou i nadále zdarma (až se kvóta) . Informace o nadcházejících změnách cenový model na naši oficiální [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Vytváření grafů a zkoumání založené na protokolech a standardní předem agregovaných metrik

Pomocí Průzkumníka metrik služby Azure Monitor k vykreslení grafy z předem agregovat a založené na protokolech metriky a vytvořit řídicí panely s grafy. Po vybrání požadovaného prostředku Application Insights, pomocí nástroje pro výběr oboru názvů můžete přepínat mezi úrovněmi standard (preview) a metrikami založenými na protokolu, nebo vybrat vlastní metriky oboru názvů:

![Metriky oboru názvů](.\media\pre-aggregated-metrics-log-metrics\002-metric-namespace.png)

## <a name="next-steps"></a>Další postup

* [Téměř v reálném čase výstrahy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric a TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)
