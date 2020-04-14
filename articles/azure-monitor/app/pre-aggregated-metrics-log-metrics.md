---
title: Metriky založené na protokolu a předem agregované metriky v Přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Proč používat v Přehledech aplikací Azure a předem agregované metriky založené na protokolu a předem agregované metriky
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 30487eebed361e5b010df023a9b1a44f96590b14
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271076"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Metriky založené na protokolech a předem agregované metriky ve službě Application Insights

Tento článek vysvětluje rozdíl mezi "tradiční" Application Insights metriky, které jsou založeny na protokoly a pre-agregované metriky, které jsou aktuálně ve verzi Public Preview. Oba typy metrik jsou k dispozici uživatelům Application Insights a každý přináší jedinečnou hodnotu v monitorování stavu aplikací, diagnostiky a analýzy. Vývojáři, kteří jsou instrumentace aplikace můžete rozhodnout, který typ metriky je nejvhodnější pro konkrétní scénář, v závislosti na velikosti aplikace, očekávaný objem telemetrie a obchodní požadavky na přesnost metriky a výstrahy.

## <a name="log-based-metrics"></a>Metriky založené na protokolu

Až donedávna byl datový model monitorování aplikací v Application Insights založen výhradně na malém počtu předdefinovaných typů událostí, jako jsou požadavky, výjimky, volání závislostí, zobrazení stránek atd. Vývojáři mohou použít sadu SDK buď vyzařovat tyto události ručně (zápisem kódu, který explicitně vyvolá SDK) nebo se mohou spolehnout na automatické shromažďování událostí z automatické instrumentace. V obou případech back-end Application Insights ukládá všechny shromážděné události jako protokoly a okna Application Insights na portálu Azure fungují jako analytický a diagnostický nástroj pro vizualizaci dat založených na událostech z protokolů.

Použití protokolů k uchování kompletní sady událostí může přinést velkou analytickou a diagnostickou hodnotu. Můžete například získat přesný počet požadavků na konkrétní adresu URL s počtem různých uživatelů, kteří uskutečnili tato volání. Nebo můžete získat podrobné diagnostické trasování, včetně výjimek a volání závislostí pro všechny relace uživatele. S tímto typem informací může výrazně zlepšit přehled o stavu aplikace a využití, což umožňuje zkrátit čas potřebný k diagnostice problémů s aplikací.

Ve stejné době shromažďování úplnou sadu událostí může být nepraktické (nebo dokonce nemožné) pro aplikace, které generují velký objem telemetrie. V situacích, kdy je příliš vysoký objem událostí, application insights implementuje několik technik snížení objemu telemetrie, jako je [například vzorkování](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) a [filtrování,](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) které snižují počet shromážděných a uložených událostí. Bohužel snížení počtu uložených událostí také snižuje přesnost metriky, které na pozadí, musí provádět agregace čas dotazu událostí uložených v protokolech.

> [!NOTE]
> V Application Insights metriky, které jsou založeny na dotazování čas agregace událostí a měření uložených v protokolech se nazývají metriky založené na protokolu. Tyto metriky mají obvykle mnoho dimenzí z vlastností události, což je činí lepšími pro analýzy, ale přesnost těchto metrik je negativně ovlivněna vzorkováním a filtrováním.

## <a name="pre-aggregated-metrics"></a>Předem agregované metriky

Kromě metrik založených na protokolu, na konci roku 2018, application insights tým dodávány veřejné náhled metriky, které jsou uloženy ve specializovaném úložišti, který je optimalizován pro časové řady. Nové metriky se již neuchovávají jako jednotlivé události se spoustou vlastností. Místo toho jsou uloženy jako předem agregované časové řady a pouze s klíčovými dimenzemi. Díky nové metriky lepší v době dotazu: načítání dat probíhá mnohem rychleji a vyžaduje méně výpočetního výkonu. To následně umožňuje nové scénáře, jako je [téměř v reálném čase upozornění na dimenze metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), citlivější řídicí [panely](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)a další.

> [!IMPORTANT]
> Obě, založené na protokolu a preagregované metriky koexistovat v Application Insights. Chcete-li tyto dvě možnosti odlišit, v uživatelském prostředí Application Insights se nyní předagregované metriky nazývají "Standardní metriky (náhled)", zatímco tradiční metriky z událostí byly přejmenovány na "metriky založené na protokolu".

Novější sady SDK ([Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK nebo novější pro rozhraní .NET) předagregují metriky během shromažďování před spuštěním techniky snížení objemu telemetrie. To znamená, že přesnost nových metrik není ovlivněna vzorkování a filtrování při použití nejnovější chod sdsad s s vyznavačů aplikací.

Pro sady SDK, které neimplementují předběžnou agregaci (tj. starší verze sad SDK Application Insights nebo pro instrumentaci prohlížeče) back-end Application Insights stále naplní nové metriky agregací událostí přijatých koncovým bodem kolekce událostí Application Insights. To znamená, že i když nemáte prospěch ze sníženého objemu dat přenášených po drátě, můžete stále používat předem agregované metriky a zažít lepší výkon a podporu téměř v reálném čase dimenzionální výstrahy s sadami SDK, které nejsou pre-agregovat metriky během kolekce.

Stojí za zmínku, že koncový bod kolekce předem agreguje události před vzorkování ingestování, což znamená, že [vzorkování ingestování](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) nikdy neovlivní přesnost předem agregované metriky, bez ohledu na verzi sady SDK, kterou používáte s vaší aplikací.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Použití předběžné agregace s vlastními metrikami Application Insights

Pre-agregace můžete použít s vlastní metriky. Dvě hlavní výhody jsou možnost konfigurace a výstrahy na dimenzi vlastní metriky a snížení objemu dat odeslaných z sady SDK do koncového bodu kolekce Application Insights.

Existuje několik [způsobů odesílání vlastních metrik z sady Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Pokud vaše verze sady SDK nabízí [Metody GetMetric a TrackValue,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) je to upřednostňovaný způsob odesílání vlastních metrik, protože v tomto případě se v rámci sady SDK provádí předběžná agregace, a to nejen snížení objemu dat uložených v Azure, ale také objemu dat přenášených z sady SDK do sady Application Insights. V opačném případě použijte [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) metodu, která bude pre-agregovat metrické události během ingestování dat.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Rozměry vlastních metrik a předběžná agregace

Všechny metriky, které odesíláte pomocí [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) nebo [GetMetric a TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) API volání jsou automaticky uloženy v protokolech a metriky obchody. Zatímco však verze vlastní metriky založená na protokolu vždy zachová všechny dimenze, předagregovaná verze metriky je ve výchozím nastavení uložena bez dimenzí. Můžete zapnout kolekci dimenzí vlastních metrik na kartě [Využití a odhadované náklady](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) zaškrtnutím políčka Povolit upozornění na vlastní dimenze metriky: 

![Využití a odhadované náklady](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Proč je kolekce vlastních dimenzi metrik ve výchozím nastavení vypnutá?

Kolekce vlastních metrik dimenze je vypnutý ve výchozím nastavení, protože v budoucnu ukládání vlastní metriky s dimenzemi se budou účtovat odděleně od Application Insights, zatímco ukládání nedimenzionální vlastní metriky zůstane zdarma (až do kvóty). O nadcházejících změnách cenového modelu se dozvíte na naší oficiální [stránce o cenách](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Vytváření grafů a zkoumání protokolových a standardních předem agregovaných metrik

Průzkumník [metrik monitorování Azure](../platform/metrics-getting-started.md) slouží k vykreslení grafů z předem agregovaných metrik a metrik založených na protokolu a k vytváření řídicích panelů pomocí grafů. Po výběru požadovaného prostředku Application Insights můžete pomocí výběru oboru názvů přepínat mezi standardními (náhledovými) a protokolovými metrikami nebo vybrat vlastní obor názvů metriky:

![Obor názvů metriky](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Cenové modely pro metriky Přehledy aplikací

Ingestování metrikdo Application Insights, ať už založené na protokolu nebo předem agregované, bude generovat náklady na základě velikosti ingestovaných dat, jak je popsáno [zde](https://docs.microsoft.com/azure/azure-monitor/app/pricing#pricing-model). Vaše vlastní metriky, včetně všech jeho dimenzí, jsou vždy uloženy v úložišti protokolu Application Insights; navíc předem agregovaná verze vlastnímetriky (bez dimenzí) je předána do úložiště metrik ve výchozím nastavení.

Výběrmožnost [povolit upozornění na vlastní dimenze metriky](#custom-metrics-dimensions-and-pre-aggregation) možnost ukládat všechny dimenze pre-agregované metriky v úložišti metriky, může generovat **dodatečné** náklady na základě [vlastní metriky cen](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Další kroky

* [Upozornění téměř v reálném čase](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric a TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)