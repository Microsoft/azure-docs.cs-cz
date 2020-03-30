---
title: Detekce anomálií časových řad & prognózování v Průzkumníku dat Azure
description: Zjistěte, jak analyzovat data časových řad pro detekci anomálií a prognózování pomocí Průzkumníka dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194153"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Detekce anomálií a prognózy v Průzkumníku dat Azure

Azure Data Explorer provádí on-li on-do on-date telemetrie dat z cloudových služeb nebo zařízení IoT. Tato data jsou analyzována pro různé poznatky, jako je monitorování stavu služby, fyzické výrobní procesy, trendy využití a prognóza zatížení. Analýza se provádí na časové řady vybraných metrik najít odchylku vzor metriky vzhledem k jeho typické normální základní vzor. Azure Data Explorer obsahuje nativní podporu pro vytváření, manipulaci a analýzu více časových řad. Dokáže vytvářet a analyzovat tisíce časových řad během několika sekund, což umožňuje téměř v reálném čase sledovat řešení a pracovní postupy.

Tento článek podrobně popisuje možnosti detekce a prognóz y časové řady časové řady Azure Data Explorer. Použitelné funkce časových řad jsou založeny na robustním dobře známém modelu rozkladu, kde se každá původní časová řada rozloží na sezónní, trendové a zbytkové složky. Anomálie jsou detekovány odlehlými hodnotami na zbytkové složce, zatímco prognózování se provádí extrapolací sezónních a trendových složek. Implementace Průzkumníka dat Azure výrazně vylepšuje základní model rozkladu automatickou detekcí sezónality, robustní odlehlou analýzou a vektorovou implementací pro zpracování tisíců časových řad během několika sekund.

## <a name="prerequisites"></a>Požadavky

Přečtěte si [analýzu časových řad v Průzkumníku dat Azure](/azure/data-explorer/time-series-analysis) a najdete přehled možností časových řad.

## <a name="time-series-decomposition-model"></a>Model rozkladu časových řad

Nativní implementace Průzkumníka dat Azure pro předpověď časových řad a detekci anomálií používá známý model rozkladu. Tento model se používá pro časové řady metrik, u kterých se očekává, že projeví periodické a trendové chování, jako je například provoz služby, prezenční signály komponent a pravidelná měření IoT, aby se předpověděly budoucí hodnoty metrik a zjistily neobvyklé hodnoty. Předpokladem tohoto regresního procesu je, že kromě dříve známého sezónního a trendového chování jsou časové řady náhodně distribuovány. Potom můžete předpovědět budoucí hodnoty metrik ze sezónních a trendových komponent souhrnně pojmenovaných směrného plánu a ignorovat zbývající část. Anomální hodnoty můžete také zjistit na základě analýzy odlehlejších hodnot pouze pomocí zbytkové části.
Chcete-li vytvořit model rozkladu, použijte funkci [`series_decompose()`](/azure/kusto/query/series-decomposefunction). Funkce `series_decompose()` přebírá sadu časových řad a automaticky rozkládá každou časovou řadu na sezónní, trendové, zbytkové a základní součásti. 

Můžete například rozložit provoz interní webové služby pomocí následujícího dotazu:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

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

![Time series decomposition](media/anomaly-detection/series-decompose-timechart.png)

* Původní časová řada je označena **červeně.** 
* Proces začíná automatickou detekcí sezónnosti pomocí [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) funkce a extrahuje **sezónní** vzor (fialovou).
* Sezónní vzor se odečte od původní časové řady a lineární regrese [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) je spuštěna pomocí funkce k nalezení **trendové** komponenty (světle modré).
* Funkce odečte trend a zbytek je **zbytková** složka (zeleně).
* Nakonec funkce přidá sezónní a trendové komponenty pro generování **základní linie** (modře).

## <a name="time-series-anomaly-detection"></a>Detekce anomálií časových řad

Funkce [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) vyhledá neobvyklé body v sadě časových řad. Tato funkce `series_decompose()` volá k sestavení modelu [`series_outliers()`](/azure/kusto/query/series-outliersfunction) rozkladu a potom běží na zbytkové součásti. `series_outliers()`vypočítá skóre anomálií pro každý bod zbytkové složky pomocí Tukeyho plotového testu. Skóre anomálií nad 1,5 nebo pod -1,5 naznačují mírný nárůst anomálií nebo pokles. Skóre anomálií nad 3,0 nebo pod -3,0 indikuje silnou anomálii. 

Následující dotaz umožňuje zjistit anomálie v provozu interní webové služby:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

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

![Detekce anomálií časových řad](media/anomaly-detection/series-anomaly-detection.png)

* Původní časová řada (červeně). 
* Základní složka (sezónní + trend) (modře).
* Anomální body (fialovou) nad původní časovou řadou. Anomální body se výrazně odchylují od očekávaných hodnot směrného plánu.

## <a name="time-series-forecasting"></a>Prognóza časových řad

Funkce [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) předpovídá budoucí hodnoty sady časových řad. Tato funkce `series_decompose()` volá k sestavení modelu rozkladu a potom pro každou časovou řadu extrapoluje komponentu směrného plánu do budoucnosti.

Následující dotaz umožňuje předpovědět provoz webových služeb příští týden:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![Prognóza časových řad](media/anomaly-detection/series-forecasting.png)

* Původní metrika (červeně). Budoucí hodnoty chybí a ve výchozím nastavení jsou nastaveny na 0.
* Extrapolujte základní složku (modře) a předpovědět hodnoty příští týden.

## <a name="scalability"></a>Škálovatelnost

Syntaxe jazyka dotazů Průzkumníka dat Azure umožňuje jedno volání ke zpracování více časových řad. Jeho jedinečná optimalizovaná implementace umožňuje rychlý výkon, což je důležité pro efektivní detekci anomálií a prognózování při sledování tisíců čítačů v téměř reálných scénářích.

Následující dotaz zobrazuje zpracování tří časových řad současně:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

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

![Škálovatelnost časových řad](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Souhrn

Tento dokument podrobně popisuje nativní funkce Průzkumníka dat Azure pro detekci a prognózování anomálií časových řad. Každá původní časová řada se rozloží na sezónní, trendové a zbytkové složky pro detekci anomálií a/nebo prognózování. Tyto funkce lze použít pro scénáře monitorování téměř v reálném čase, jako je například detekce chyb, prediktivní údržba a prognózy poptávky a zatížení.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [možnostech strojového učení](/azure/data-explorer/machine-learning-clustering) v Azure Data Exploreru.
