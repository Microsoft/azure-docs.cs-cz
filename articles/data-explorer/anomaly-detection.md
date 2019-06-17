---
title: Čas detekce anomálií řady a předpovědi v Průzkumníku dat Azure
description: Zjistěte, jak analyzovat data časových řad pro detekci anomálií a Prognózování pomocí Průzkumníka dat služby Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: f40350129a12c7865051bcae80b74b6f9c069179
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233538"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Detekce anomálií a prognózování v Průzkumníku dat Azure

Průzkumník služby Azure Data provádí probíhající shromažďování telemetrických dat ze zařízení IoT nebo cloudové služby. Tato data se analyzují pro různé přehledy, jako je monitorování stavu služby, fyzické výrobních procesů, trendy využití a předpověď zatížení. Analýza provádí v časové řadě vybrané metriky k vyhledání odchylku vzor metriky vzhledem ke způsobu typické normální směrného plánu. Průzkumník služby Azure Data obsahuje nativní podporu pro vytváření, manipulaci a analýze několika časovými řadami. To můžete vytvářet a analyzovat tisíce časových řad v sekundách, po povolení téměř reálném čase, monitorování řešeními a pracovními postupy.

Tento článek podrobně popisuje Průzkumníka služby Azure Data čas řady anomálií zjišťování a Prognózování možnosti. Použít časové řady funkce jsou založené na modelu robustní dobře známé rozložené, kde je každá Časová řada původní rozložit na sezónní, trend a plyne ze zbytkových součástí. Anomálie se zjišťují odlehlé hodnoty zbývající součásti, zatímco Prognózování provádí extrapolaci sezónním a trend komponenty. Průzkumník dat Azure implementace významně zvyšuje modelu základní rozložené sezónnosti automatické zjišťování, robustní pravdu analýzy a vektorizovaných provádění procesu tisíců časových řad v řádu sekund.

## <a name="prerequisites"></a>Požadavky

Čtení [časové řady analýzy v Průzkumníku dat Azure](/azure/data-explorer/time-series-analysis) přehledné informace o času řadu funkcí.

## <a name="time-series-decomposition-model"></a>Dekompoziční model časové řady

Azure nativní implementace Průzkumník dat pro předpověď časové řady a detekci anomálií používá dobře známé dekompoziční model. Tento model se použije na časové řady má manifest pravidelné spouštění i trend chování, například provoz služeb, komponenty prezenční signály a IoT pravidelná měření předpovídat budoucí hodnoty metrik a detekuje neobvyklé ty metriky. Předpoklad tento proces regrese, který je než dříve známé sezónní a trend chování, čas náhodně distribuuje řady. Můžete předpovídat budoucí hodnoty metrik z sezónním a trend komponenty, společně s názvem směrného plánu a přeskočit zbývající část. Můžete také zjistit neobvyklé hodnoty na základě pravdu analýzy s využitím pouze zbývající části.
Pokud chcete vytvořit dekompoziční model, použijte funkci [ `series_decompose()` ](/azure/kusto/query/series-decomposefunction). `series_decompose()` Funkce vezme sadu časových řad a automaticky rozložíme každé časové řady na jeho sezónní, trend, zbývající a základní komponenty. 

Například můžete rovněž rozložit provoz interní webové služby s použitím následujícího dotazu:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Rozložené řady čas](media/anomaly-detection/series-decompose-timechart.png)

* Původní časové řady má popisek **num** (červeně). 
* Spuštění procesu podle Automatická detekce sezónnosti pomocí funkce [ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) a extrahuje **sezónní** vzor (znázorněný fialovou barvou).
* Sezónním vzoru je odečtena od původní časové řady a lineární regrese spuštění pomocí funkce [ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction) najít **trend** komponentu (ve světle modrá).
* Funkce odečítá trend a zbytek je **plyne ze zbytkových** komponentu (zeleně).
* A konečně, funkce přidá sezónní a trend součásti pro generování **směrného plánu** (modře).

## <a name="time-series-anomaly-detection"></a>Čas detekce anomálií řady

Funkce [ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction) najde neobvyklé body na sadu časové řady. Tato funkce volá `series_decompose()` vytvářet dekompoziční model a pak spustí [ `series_outliers()` ](/azure/kusto/query/series-outliersfunction) na zbývající součásti. `series_outliers()` vypočítá anomálií skóre, které se pro každý bod plyne ze zbytkových součástí pomocí vaší Tukey ohrazení testu. Anomálií skóre nad 1.5 nebo pod-1.5 označení anomálií mírné zvýšení nebo zakázat v uvedeném pořadí. Anomálií skóre nad 3.0 nebo pod-3.0 znamená silné anomálií. 

Následující dotaz umožňuje zjišťovat anomálie v interní webový provoz služeb:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Čas detekce anomálií řady](media/anomaly-detection/series-anomaly-detection.png)

* Původní časové řady (červeně). 
* Směrný plán (sezónní + trendu) součásti (modře).
* Neobvyklé body (znázorněný fialovou barvou) nad rámec původní časové řady. Neobvyklé body se výrazně liší od Očekávaný standardní hodnoty.

## <a name="time-series-forecasting"></a>Vytváření prognóz časových řad

Funkce [ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction) předpovídá budoucí hodnoty sadu časové řady. Tato funkce volá `series_decompose()` sestavit dekompoziční model a potom u každé datové řady čas extrapolates komponentu směrný plán do budoucna.

Následující dotaz můžete předpovídat provoz příští týden webové služby:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![Vytváření prognóz časových řad](media/anomaly-detection/series-forecasting.png)

* Původní metriku (červeně). Budoucí hodnoty chybí a nastavit na hodnotu 0, ve výchozím nastavení.
* Potom údaje Extrapolujte základní součásti (modře) k předpovědi hodnot příští týden.

## <a name="scalability"></a>Škálovatelnost

Azure syntaxi jazyka dotazů v Průzkumníku dat umožňuje jednom volání zpracovat několika časovými řadami. Provádění jedinečný optimalizované umožňuje rychlý výkon, který je zásadní pro detekci anomálií efektivní a Prognózování při monitorování tisíce čítače v blízkosti scénáře v reálném čase.

Následující dotaz zobrazí zpracování tři časové řady současně:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Čas řady škálovatelnost](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Souhrn

Tento dokument podrobně popisuje nativní funkce Průzkumníka služby Azure Data pro čas detekce anomálií řady a předpovědi. Každá Časová řada původní je rozložit na sezónní, trend a zbývající součásti pro zjišťování anomálií a/nebo Prognózování. Tyto funkce lze použít pro téměř v reálném čase scénáře monitorování, jako je například zjišťování chyb, prediktivní údržbu a vyžádání a načíst Prognózování.

## <a name="next-steps"></a>Další postup

Další informace o [strojového učení](/azure/data-explorer/machine-learning-clustering) v Průzkumníku dat Azure.
