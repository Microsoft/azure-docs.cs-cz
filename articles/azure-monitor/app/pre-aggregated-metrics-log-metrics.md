---
title: Metriky založené na protokolu a předem agregované metriky v Azure Application Insights | Microsoft Docs
description: Proč používat v Azure Application Insights a předem agregované metriky založené na protokolech
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: acbe535d740eb527d165be1675f31e759851a987
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717821"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Metriky založené na protokolech a předem agregované metriky ve službě Application Insights

Tento článek vysvětluje rozdíl mezi tradičními Application Insights metrikami, které jsou založené na protokolech, a předem agregované metriky. Oba typy metrik jsou k dispozici uživatelům Application Insights a každá z nich přináší jedinečnou hodnotu v monitorování stavu aplikací, diagnostiky a analýz. Vývojáři, kteří instrumentují aplikace, mohou rozhodnout, který typ metriky je nejvhodnější pro konkrétní scénář, v závislosti na velikosti aplikace, očekávaném objemu telemetrie a obchodních požadavcích na přesnost metrik a výstrahy.

## <a name="log-based-metrics"></a>Metriky založené na protokolu

V minulosti byl datový model telemetrie monitorování aplikací v Application Insights výhradně založen na malém počtu předdefinovaných typů událostí, jako jsou požadavky, výjimky, volání závislostí, zobrazení stránek atd. Vývojáři mohou použít sadu SDK k ručnímu vygenerování těchto událostí (napsáním kódu, který explicitně vyvolá sadu SDK), nebo mohou spoléhat na automatické shromažďování událostí z automatické instrumentace. V obou případech Application Insights back-end ukládá všechny shromážděné události jako protokoly a Application Insights okna v Azure Portal fungují jako analytické a diagnostické nástroje, které znázorňují data založená na událostech z protokolů.

Použití protokolů k uchování kompletní sady událostí může přinést skvělou analytickou a diagnostickou hodnotu. Můžete například získat přesný počet požadavků na konkrétní adresu URL s počtem různých uživatelů, kteří provedli tato volání. Nebo můžete získat podrobné trasování diagnostiky, včetně výjimek a volání závislostí pro jakoukoli relaci uživatele. Tento typ informací může významně zlepšit viditelnost stavu a využití aplikací, což umožňuje zkrátit dobu potřebnou k diagnostice problémů s aplikací.

Současně může být shromažďování kompletní sady událostí pro aplikace, které generují velký objem telemetrie, nepraktické (nebo dokonce nemožné). V situacích, kdy je objem událostí příliš vysoký, Application Insights implementuje několik technik snižování objemu telemetrie, jako je [vzorkování](./sampling.md) a [filtrování](./api-filtering-sampling.md) , které omezují počet shromážděných a uložených událostí. Snížení počtu uložených událostí ale také snižuje přesnost metrik, které se na pozadí musí provádět v agregacích událostí uložených v protokolech.

> [!NOTE]
> V Application Insights metriky založené na agregačních událostech událostí a měřeních uložených v protokolech se nazývají metriky založené na protokolech. Tyto metriky mají obvykle mnoho dimenzí z vlastností události, které jsou pro analýzu nadřazené, ale přesnost těchto metrik je negativně ovlivněna vzorkováním a filtrováním.

## <a name="pre-aggregated-metrics"></a>Předem agregované metriky

Kromě metrik založených na protokolu v 2018 se tým Application Insights dodal ve verzi Public Preview metrik, které jsou uložené ve specializovaném úložišti, které je optimalizované pro časové řady. Nové metriky se už neuchovávají jako jednotlivé události s velkým množstvím vlastností. Místo toho jsou uloženy jako předem agregované časové řady a pouze s dimenzemi Key. Díky tomu jsou nové metriky nadřazené době dotazu: načítání dat je mnohem rychlejší a vyžaduje méně výpočetní výkon. V důsledku toho to umožňuje nové scénáře, jako je například [upozorňování téměř v reálném čase, u dimenzí metrik](../alerts/alerts-metric-near-real-time.md), rychlejších [řídicích panelů](./overview-dashboard.md)a dalších.

> [!IMPORTANT]
> V Application Insights existují i předem agregované metriky založené na protokolu a. Pro odlišení těchto dvou Application Insights v uživatelském prostředí předběžně agregované metriky nyní označují "Standardní metriky (Preview)", zatímco tradiční metriky z událostí byly přejmenovány na "metriky založené na protokolu".

Novější sady SDK ([Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK nebo novější pro .NET) předem agregované metriky během shromažďování. To platí pro  [Standardní metriky, které jsou ve výchozím nastavení posílány](../essentials/metrics-supported.md#microsoftinsightscomponents) , takže se vzorkováním ani filtrováním neovlivní přesnost. Vztahuje se také na vlastní metriky odesílané pomocí [Getmetric](./api-custom-events-metrics.md#getmetric) , což vede k menšímu příjmu dat a snížení nákladů.

Pro sady SDK, které neimplementují předagregační (to znamená starší verze sady Application Insights SDK nebo instrumentace prohlížeče) Application Insights back-end stále naplní nové metriky agregací událostí přijatých koncovým bodem shromažďování událostí Application Insights. To znamená, že i když nebudete využívat redukovaný objem dat přenášených po síti, můžete stále používat předem agregované metriky a využívat lepší výkon a podporu pro multidimenzionální upozorňování v reálném čase pomocí sad SDK, které během shromažďování nemají předem agregované metriky.

Je třeba uvést, že koncový bod kolekce před pokračováním vzorkování předem agreguje události, což znamená, že [vzorkování](./sampling.md) ingest nebude nikdy ovlivňovat přesnost předagregovaných metrik bez ohledu na verzi sady SDK, kterou používáte spolu s vaší aplikací.  

### <a name="sdk-supported-pre-aggregated-metrics-table"></a>Sada SDK podporovala předem agregované tabulky metrik

| Aktuální provozní sady SDK | Standardní metriky (předběžný agregační sada SDK) | Vlastní metriky (bez předem agregované sady SDK) | Vlastní metriky (s předem agregovanou sadou SDK)|
|------------------------------|-----------------------------------|----------------------------------------------|---------------------------------------|
| .NET Core a .NET Framework | Podporováno (V 2.13.1 +)| Podporováno prostřednictvím [TrackMetric](api-custom-events-metrics.md#trackmetric)| Podporováno (V 2.7.2 +) prostřednictvím [Getmetric](get-metric.md) |
| Java                         | Nepodporuje se       | Podporováno prostřednictvím [TrackMetric](api-custom-events-metrics.md#trackmetric)| Nepodporuje se                           |
| Node.js                      | Nepodporuje se       | Podporováno prostřednictvím  [TrackMetric](api-custom-events-metrics.md#trackmetric)| Nepodporuje se                           |
| Python                       | Nepodporuje se       | Podporováno                                 | Částečně podporováno přes [OpenCensus. stats](opencensus-python.md#metrics) |  

> [!NOTE]
>  Implementace metriky pro Python používá OpenCensus. stat se liší od getmetric. Podrobnosti najdete v [dokumentaci k Pythonu o metrikách](./opencensus-python.md#metrics).

### <a name="codeless-supported-pre-aggregated-metrics-table"></a>Předem agregovaná tabulka metrik podporuje bez kódování

| Aktuální provozní sady SDK | Standardní metriky (předběžný agregační sada SDK) | Vlastní metriky (bez předem agregované sady SDK) | Vlastní metriky (s předem agregovanou sadou SDK)|
|-------------------------|--------------------------|-------------------------------------------|-----------------------------------------|
| ASP.NET                 | Podporované <sup> 1<sup>    | Nepodporuje se                             | Nepodporuje se                           |
| ASP.NET Core            | Podporované <sup> 2<sup>    | Nepodporuje se                             | Nepodporuje se                           |
| Java                    | Nepodporuje se            | Nepodporuje se                             | [Podporováno](java-in-process-agent.md#metrics) |
| Node.js                 | Nepodporuje se            | Nepodporuje se                             | Nepodporuje se                           |

1. ASP.NETelné připojení neelné na App Service generuje metriky jenom v režimu úplného monitorování. ASP.NET připojení bez kódu na App Service, VM/VMSS a místní emitují standardní metriky bez dimenzí. Sada SDK je vyžadována pro všechny dimenze.
2. ASP.NET Core připojení bez kódu v App Service emituje standardní metriky bez dimenzí. Sada SDK je vyžadována pro všechny dimenze.

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Použití předagregačních s Application Insights vlastními metrikami

Můžete použít předběžnou agregaci s vlastními metrikami. Tyto dvě hlavní výhody jsou možností konfigurace a upozornění na dimenzi vlastní metriky a snížení objemu dat odesílaných ze sady SDK do koncového bodu kolekce Application Insights.

Existuje několik [způsobů, jak odesílat vlastní metriky ze sady SDK Application Insights](./api-custom-events-metrics.md). Pokud vaše verze sady SDK nabízí metody [getmetric a TrackValue](./api-custom-events-metrics.md#getmetric) , jedná se o preferovaný způsob, jak odesílat vlastní metriky, protože v tomto případě předagregace probíhá v rámci sady SDK, nejen zmenšuje objem dat uložených v Azure, ale také objem dat přenesených ze sady sdk do Application Insights. V opačném případě použijte metodu [trackMetric](./api-custom-events-metrics.md#trackmetric)  , která bude během příjmu dat předem agregovat události metriky.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Vlastní metriky dimenzí a předběžné agregace

Všechny metriky, které odesíláte pomocí volání [trackMetric](./api-custom-events-metrics.md#trackmetric) nebo [Getmetric a TrackValue](./api-custom-events-metrics.md#getmetric) API, se automaticky ukládají do obou protokolů a úložišť metrik. Nicméně zatímco verze vlastní metriky založené na protokolech vždycky uchovává všechny dimenze, předem agregovaná verze metriky se ve výchozím nastavení uloží bez dimenzí. Můžete zapnout shromažďování dimenzí vlastních metrik na kartě [využití a odhadované náklady](./pricing.md) zaškrtnutím možnosti Povolit upozorňování u vlastních dimenzí metriky: 

![Využití a odhadované náklady](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Proč je shromažďování vlastních dimenzí metrik ve výchozím nastavení vypnuté?

Kolekce vlastních dimenzí metrik je ve výchozím nastavení vypnutá, protože v budoucích ukládání vlastních metrik s dimenzemi se bude účtovat nezávisle na Application Insights, zatímco ukládání nedimenzionálních vlastních metrik zůstane bezplatné (až po kvótě). O nadcházejících změnách cenového modelu se můžete seznámit na naší oficiální [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Vytváření grafů a zkoumání předem agregovaných metrik na základě protokolů a standardu

Pomocí [Azure Monitor Průzkumník metrik](../essentials/metrics-getting-started.md) můžete kreslit grafy z předem agregovaných metrik a metriky založené na protokolu a vytvářet řídicí panely pomocí grafů. Po výběru požadovaného prostředku Application Insights použijte výběr oboru názvů pro přepínání mezi standardem (Preview) a metrikami založenými na protokolu nebo vyberte vlastní obor názvů metriky:

![Obor názvů metriky](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Cenové modely pro Application Insights metriky

Ingestování metrik do Application Insights, ať už na základě protokolu nebo předem agregované, vygeneruje náklady na základě velikosti přijatých dat, jak je popsáno [zde](./pricing.md#pricing-model). Vaše vlastní metriky, včetně všech jejích rozměrů, se vždycky ukládají do úložiště Application Insightsho protokolu. Kromě toho je ve výchozím nastavení předávána předem agregovaná verze vlastních metrik (bez dimenzí) do úložiště metrik.

Když vyberete možnost [Povolit upozorňování u vlastních dimenzí metriky](#custom-metrics-dimensions-and-pre-aggregation) a uložíte tak všechny dimenze předagregované metriky v úložišti metrik, může se vygenerovat **Další** náklady na základě [vlastních cen metrik](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Další kroky

* [Upozorňování téměř v reálném čase](../alerts/alerts-metric-near-real-time.md)
* [Getmetric a TrackValue](./api-custom-events-metrics.md#getmetric)
