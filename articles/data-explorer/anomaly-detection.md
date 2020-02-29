---
title: Detekce anomálií časové řady & prognózování v Azure Průzkumník dat
description: Naučte se analyzovat data časových řad pro detekci anomálií a předpovědi pomocí Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194153"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Detekce anomálií a prognózování v Azure Průzkumník dat

Azure Průzkumník dat provádí shromažďování dat telemetrie z cloudových služeb nebo zařízení IoT. Tato data se analyzují pro různé přehledy, jako je monitorování stavu služby, fyzické výrobní procesy, trendy využití a prognóza zatížení. Analýza se provádí v časové řadě vybraných metrik za účelem nalezení vzorce odchylek metriky relativně k běžnému základnímu vzoru normálního směrného plánu. Azure Průzkumník dat obsahuje nativní podporu pro vytváření, manipulaci a analýzu více časových řad. Může vytvářet a analyzovat tisíce časových řad v řádu sekund, což umožňuje monitorování a pracovní postupy pro monitorování prakticky v reálném čase.

Tento článek podrobně popisuje možnosti detekce anomálií a předpovědi pro Azure Průzkumník dat Time Series. Příslušné funkce časové řady jsou založeny na robustním dobře známém modelu rozkladu, kde každá původní časová řada je rozdělena na sezónní, vývojové a reziduální komponenty. Anomálie se zjišťují na zbytkové komponentě, zatímco předpovědi se provádí extrapolací komponent sezón a trendů. Implementace Azure Průzkumník dat významně vylepšuje základní model rozkladu pomocí automatického zjišťování sezónnost, robustní analýzy izolované a vektorové implementace pro zpracování tisíců časových řad v řádu sekund.

## <a name="prerequisites"></a>Předpoklady

Přehled možností časových řad najdete v tématu věnovaném [analýze časových řad v Azure Průzkumník dat](/azure/data-explorer/time-series-analysis) .

## <a name="time-series-decomposition-model"></a>Model dekompozice časové řady

Nativní implementace Azure Průzkumník dat pro předpověď časových řad a detekce anomálií používá dobře známý model rozkladu. Tento model se aplikuje na časovou řadu metrik, které se očekávají při vytváření běžných a trendových chování, jako je například provoz služeb, prezenční signály komponent a pravidelná měření IoT k předpovědi budoucích hodnot metrik a detekce neobvyklé. Předpokládáme, že tento proces regrese je jiný než dříve známé chování sezónního a trendu, ale časová řada se náhodně distribuuje. Potom můžete předpovědět budoucí hodnoty metrik ze součástí sezónních a trendů, souhrnně pojmenovaného směrného plánu a zbývající část ignorovat. Můžete také zjistit hodnoty neobvyklé založené na analýze izolované pouze pomocí zbytkové části.
Chcete-li vytvořit rozloženou model, použijte funkci [`series_decompose()`](/azure/kusto/query/series-decomposefunction). Funkce `series_decompose()` přebírá sadu časových řad a automaticky převádí každou časovou řadu na své sezónní, vývojové, reziduální a základní komponenty. 

Můžete například rozložit přenos interní webové služby pomocí následujícího dotazu:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

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

![Dekompozice časové řady](media/anomaly-detection/series-decompose-timechart.png)

* Původní časová řada je označena jako **číslo** (červeně). 
* Proces začíná automatickým rozpoznáváním sezónnost pomocí funkce [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) a extrahuje vzor **sezónní** (fialová).
* Sezónní vzor se odečte od původní časové řady a spustí se lineární regrese s využitím funkce [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) k nalezení komponenty **trendu** (světle modrá).
* Funkce odečte trend a zbytek je **zbytkovou** komponentou (zelenou).
* Nakonec funkce přidá komponenty sezónní a trend pro vygenerování **směrného plánu** (modře).

## <a name="time-series-anomaly-detection"></a>Detekce anomálií časové řady

Funkce [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) vyhledá body neobvyklé v sadě časových řad. Tato funkce volá `series_decompose()` pro sestavení modelu rozkladu a poté spouští [`series_outliers()`](/azure/kusto/query/series-outliersfunction) na zbytkové součásti. `series_outliers()` vypočítá skóre anomálií pro každý bod zbytkové součásti pomocí testu plotu Tukey. Skóre anomálií nad 1,5 nebo nižší-1,5 znamenají mírné zvýšení anomálií nebo pokles. Výsledky anomálií nad 3,0 nebo nižší-3,0 označují silnou anomálii. 

Následující dotaz vám umožní detekovat anomálie při interním provozu webové služby:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

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

![Detekce anomálií časové řady](media/anomaly-detection/series-anomaly-detection.png)

* Původní časová řada (červeně). 
* Komponenta směrného plánu (sezónní + trend) (modře).
* Body neobvyklé (fialové) nad původní časovou řadou. Body neobvyklé se významně odchylují od očekávaných hodnot směrného plánu.

## <a name="time-series-forecasting"></a>Prognózování časových řad

Funkce [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) předpovídá budoucí hodnoty sady časových řad. Tato funkce volá `series_decompose()` pro sestavení modelu rozkladu a pak pro každou časovou řadu provede extrapolaci základní komponentu do budoucna.

Následující dotaz vám umožní předpovědět provoz webové služby Next Week:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

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

![Prognózování časových řad](media/anomaly-detection/series-forecasting.png)

* Původní metrika (červeně). Ve výchozím nastavení chybí budoucí hodnoty a jsou nastavené na 0.
* Odvodit základní komponentu (modře) pro předpověď hodnot příštího týdne.

## <a name="scalability"></a>Škálovatelnost

Syntaxe jazyka dotazů Azure Průzkumník dat umožňuje jedno volání zpracovat více časových řad. Jeho jedinečná optimalizovaná implementace umožňuje rychlý výkon, který je důležitý pro efektivní detekci anomálií a předpovědi při monitorování tisíců čítačů téměř v reálném čase.

Následující dotaz ukazuje zpracování tří časových řad současně:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

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

Tento dokument podrobně popisuje nativní funkce služby Azure Průzkumník dat pro detekci anomálií a předpovědi časových řad. Každá původní časová řada se rozloží na sezónní, vývojové a reziduální komponenty pro detekci anomálií a/nebo předpovědi. Tyto funkce se dají použít pro scénáře monitorování téměř v reálném čase, jako je detekce chyb, prediktivní údržba a prognózování poptávky a zatížení.

## <a name="next-steps"></a>Další kroky

Seznamte se s [možnostmi strojového učení](/azure/data-explorer/machine-learning-clustering) v Azure Průzkumník dat.
