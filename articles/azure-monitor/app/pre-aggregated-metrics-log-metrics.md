---
title: Metriky založené na protokolu a předem agregované metriky v Azure Application Insights | Microsoft Docs
description: Proč používat v Azure Application Insights a předem agregované metriky založené na protokolech
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 384e3c9032b324ee92762db9156c628a05e5e862
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406592"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Metriky založené na protokolech a předem agregované metriky ve službě Application Insights

Tento článek vysvětluje rozdíl mezi tradičními Application Insights metrikami, které jsou založené na protokolech, a předem agregované metriky, které jsou aktuálně ve verzi Public Preview. Oba typy metrik jsou k dispozici uživatelům Application Insights a každá z nich přináší jedinečnou hodnotu v monitorování stavu aplikací, diagnostiky a analýz. Vývojáři, kteří instrumentují aplikace, mohou rozhodnout, který typ metriky je nejvhodnější pro konkrétní scénář, v závislosti na velikosti aplikace, očekávaném objemu telemetrie a obchodních požadavcích na přesnost metrik a výstrahy.

## <a name="log-based-metrics"></a>Metriky založené na protokolu

Až do poslední doby, datový model telemetrie monitorování aplikací v Application Insights byl založen výhradně na malém počtu předdefinovaných typů událostí, jako jsou požadavky, výjimky, volání závislostí, zobrazení stránek atd. Vývojáři mohou použít sadu SDK k ručnímu vygenerování těchto událostí (napsáním kódu, který explicitně vyvolá sadu SDK), nebo mohou spoléhat na automatické shromažďování událostí z automatické instrumentace. V obou případech Application Insights back-end ukládá všechny shromážděné události jako protokoly a Application Insights okna v Azure Portal fungují jako analytické a diagnostické nástroje, které znázorňují data založená na událostech z protokolů.

Použití protokolů k uchování kompletní sady událostí může přinést skvělou analytickou a diagnostickou hodnotu. Můžete například získat přesný počet požadavků na konkrétní adresu URL s počtem různých uživatelů, kteří provedli tato volání. Nebo můžete získat podrobné trasování diagnostiky, včetně výjimek a volání závislostí pro jakoukoli relaci uživatele. Tento typ informací může významně zlepšit viditelnost stavu a využití aplikací, což umožňuje zkrátit dobu potřebnou k diagnostice problémů s aplikací. 

Současně může být shromažďování kompletní sady událostí pro aplikace, které generují spoustu telemetrie, nepraktické (nebo dokonce nemožné). V situacích, kdy je objem událostí příliš vysoký, Application Insights implementuje několik technik snižování objemu telemetrie, jako je [vzorkování](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) a [filtrování](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) , které omezují počet shromážděných a uložených událostí. Snížení počtu uložených událostí ale také snižuje přesnost metrik, které se na pozadí musí provádět v agregacích událostí uložených v protokolech.

> [!NOTE]
> V Application Insights metriky založené na agregačních událostech událostí a měřeních uložených v protokolech se nazývají metriky založené na protokolech. Tyto metriky mají obvykle mnoho dimenzí z vlastností události, které jsou pro analýzu nadřazené, ale přesnost těchto metrik je negativně ovlivněna vzorkováním a filtrováním.

## <a name="pre-aggregated-metrics"></a>Předem agregované metriky

Kromě metrik založených na protokolech v rozsahu 2018 Application Insights tým dodal veřejnou verzi Preview metrik, která je uložená ve specializovaném úložišti, které je optimalizované pro časové řady. Nové metriky se už neuchovávají jako jednotlivé události s velkým množstvím vlastností. Místo toho jsou uloženy jako předem agregované časové řady a pouze s dimenzemi Key. Díky tomu jsou nové metriky nadřazené době dotazu: načítání dat je mnohem rychlejší a vyžaduje méně výpočetní výkon. V důsledku toho to umožňuje nové scénáře, jako je například [upozorňování téměř v reálném čase, u dimenzí metrik](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), rychlejších [řídicích panelů](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)a dalších.

> [!IMPORTANT]
> V Application Insights existují i předem agregované metriky založené na protokolu a. Pro odlišení těchto dvou Application Insights v uživatelském prostředí předběžně agregované metriky nyní označují "Standardní metriky (Preview)", zatímco tradiční metriky z událostí byly přejmenovány na "metriky založené na protokolu".

Novější sady SDK ([Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK nebo novější pro .NET) předem agregované metriky během shromažďování před tím, než vychází technik pro snížení objemu telemetrie. To znamená, že přesnost nových metrik není ovlivněná vzorkováním a filtrováním při použití nejnovějších sad SDK pro Application Insights.

Pro sady SDK, které neimplementují předagregační (to znamená starší verze sady Application Insights SDK nebo instrumentace prohlížeče) Application Insights back-end pořád naplní nové metriky agregací událostí přijatých aplikací. Koncový bod shromažďování událostí pro přehledy To znamená, že i když nebudete mít omezený objem dat přenášených po síti, můžete stále používat předem agregované metriky a využívat lepší výkon a podporu pro multidimenzionální upozorňování v reálném čase pomocí sad SDK, které ne předem agregované metriky během shromažďování.

Je třeba uvést, že koncový bod kolekce před pokračováním vzorkování předem agreguje události, což znamená, že [vzorkování](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) ingest nebude nikdy ovlivňovat přesnost předagregovaných metrik bez ohledu na verzi sady SDK, kterou používáte spolu s vaší aplikací.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Použití předagregačních s Application Insights vlastními metrikami

Můžete použít předběžnou agregaci s vlastními metrikami. Tyto dvě hlavní výhody jsou možností konfigurace a upozornění na dimenzi vlastní metriky a snížení objemu dat odesílaných ze sady SDK do koncového bodu kolekce Application Insights.

Existuje několik [způsobů, jak odesílat vlastní metriky ze sady SDK Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Pokud vaše verze sady SDK nabízí metody [getmetric a TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) , jedná se o preferovaný způsob, jak odesílat vlastní metriky, protože v tomto případě předagregace probíhá v rámci sady SDK, nejen zmenšuje objem dat uložených v Azure, ale také objem dat přenesených ze sady sdk do Application Insights. V opačném případě použijte metodu [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) , která bude během příjmu dat předem agregovat události metriky.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Vlastní metriky dimenzí a předběžné agregace

Všechny metriky, které odesíláte pomocí volání [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) nebo [Getmetric a TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) API, se automaticky ukládají do obou protokolů a úložišť metrik. Nicméně zatímco verze vlastní metriky založené na protokolech vždycky uchovává všechny dimenze, předem agregovaná verze metriky se ve výchozím nastavení uloží bez dimenzí. Můžete zapnout shromažďování dimenzí vlastních metrik na kartě [využití a odhadované náklady](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) zaškrtnutím možnosti Povolit upozorňování u vlastních dimenzí metriky: 

![Využití a odhadované náklady](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Proč je shromažďování vlastních dimenzí metrik ve výchozím nastavení vypnuté?

Kolekce vlastních dimenzí metrik je ve výchozím nastavení vypnutá, protože v budoucích ukládání vlastních metrik s dimenzemi se bude účtovat nezávisle na Application Insights, zatímco ukládání nedimenzionálních vlastních metrik zůstane bezplatné (až po kvótě). . O nadcházejících změnách cenového modelu se můžete seznámit na naší oficiální [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Vytváření grafů a zkoumání předem agregovaných metrik na základě protokolů a standardu

Pomocí [Azure Monitor Průzkumník metrik](../platform/metrics-getting-started.md) můžete kreslit grafy z předem agregovaných metrik a metriky založené na protokolu a vytvářet řídicí panely pomocí grafů. Po výběru požadovaného prostředku Application Insights použijte výběr oboru názvů pro přepínání mezi standardem (Preview) a metrikami založenými na protokolu nebo vyberte vlastní obor názvů metriky:

![Obor názvů metriky](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Další kroky

* [Upozorňování téměř v reálném čase](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [Getmetric a TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)
