---
title: Sdílení vlastních zobrazení s parametrizovanými adresami URL – Azure Time Series Insights | Microsoft Docs
description: Naučte se vytvářet parametrizované adresy URL pro snadné sdílení vlastních zobrazení v Průzkumníkovi v Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 9dfe499a7d6084a23fd71ab98db472befe71fc04
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024360"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Sdílení vlastního zobrazení pomocí parametrizovaných adres URL

Chcete-li sdílet vlastní zobrazení v Průzkumníkovi Time Series Insights, můžete programově vytvořit parametrizovanou adresu URL vlastního zobrazení.

Průzkumník Time Series Insights podporuje parametry dotazu URL pro určení zobrazení v prostředí přímo z adresy URL. Pouze pomocí adresy URL můžete například určit cílové prostředí, predikát vyhledávání a požadované časové období. Když uživatel vybere přizpůsobenou adresu URL, rozhraní poskytne odkaz přímo na tento prostředek na portálu Time Series Insights. Platí zde zásady přístupu k datům.

> [!TIP]
> * Podívejte se na [ukázku bezplatné Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Přečtěte si doprovodnou dokumentaci k [Time Series Insights Exploreru](./time-series-insights-explorer.md) .

## <a name="environment-id"></a>ID prostředí

Parametr `environmentId=<guid>` určuje ID cílového prostředí. Je to součást plně kvalifikovaného názvu domény pro přístup k datům a můžete ji najít v pravém horním rohu přehledu prostředí v Azure Portal. Je to vše před `env.timeseries.azure.com`.

Příkladem parametru ID prostředí je `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

V parametrizované adrese URL můžete zadat absolutní nebo relativní časové hodnoty.

### <a name="absolute-time-values"></a>Absolutní časové hodnoty

Pro absolutní časové hodnoty použijte parametry `from=<integer>` a `to=<integer>`.

* `from=<integer>` je hodnota v milisekundách JavaScriptu určující čas spuštění pro rozsah hledání.
* `to=<integer>` je hodnota v milisekundách JavaScriptu určující čas ukončení pro rozsah hledání.

> [!TIP]
> Pokud chcete data snadno překládat na JavaScriptové milisekundy, zkuste [převaděč časových razítek epocha & UNIX](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relativní časové hodnoty

Pro relativní časovou hodnotu použijte `relativeMillis=<value>`, kde je *hodnota* v jazyce JavaScript milisekund od nejnovějšího časového razítka přijatého z rozhraní API.

Například `&relativeMillis=3600000` zobrazí data za posledních 60 minut.

Přijaté hodnoty odpovídají nabídce **rychlý čas** v Průzkumníkovi Time Series Insights a zahrnují:

* `1800000` (posledních 30 minut)
* `3600000` (posledních 60 minut)
* `10800000` (poslední 3 hodiny)
* `21600000` (posledních 6 hodin)
* `43200000` (posledních 12 hodin)
* `86400000` (posledních 24 hodin)
* `604800000` (posledních 7 dní)
* `2592000000` (posledních 30 hodin)

### <a name="optional-parameters"></a>Volitelné parametry

Parametr `timeSeriesDefinitions=<collection of term objects>` určuje výrazy predikátu, které se zobrazí v zobrazení Time Series Insights:

| Parametr | Položka URL | Popis |
| --- | --- | --- |
| **name** | `\<string>` | Název *podmínky*. |
| **splitBy** | `\<string>` | Název sloupce, podle kterého se bude *rozdělovat*. |
| **measureName** | `\<string>` | Název sloupce obsahujícího *míru*. |
| **predikát** | `\<string>` | Klauzule *kde* pro filtrování na straně serveru. |
| **useSum** | `true` | Volitelný parametr, který určuje použití součtu pro míru. |

> [!NOTE]
> Pokud `Events` je vybraná míra **useSum** , ve výchozím nastavení je vybraná možnost počet.  
> Pokud není vybraná možnost `Events`, je ve výchozím nastavení vybrána možnost průměr. |

* Dvojice klíč-hodnota `multiChartStack=<true/false>` povoluje vrstvení v grafu.
* Dvojice klíč-hodnota `multiChartSameScale=<true/false>` umožňuje stejné škálování osy Y napříč podmínkami v rámci volitelného parametru.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>` vám umožňuje upravit posuvník intervalu, aby bylo možné podrobnější nebo hladší a agregované zobrazení grafu.  
* Parametr `timezoneOffset=<integer>` umožňuje nastavit časové pásmo, které se má v grafu zobrazit jako posun na čas UTC.

| Páry | Popis |
| --- | --- |
| `multiChartStack=false` | `true` je ve výchozím nastavení povolená, takže přejdou `false` do zásobníku. |
| `multiChartStack=false&multiChartSameScale=true` | Pokud chcete ve všech podmínkách použít stejné měřítko osy Y, musíte povolit umísťování do zásobníku.  Ve výchozím nastavení je `false`, takže předáváním `true` tuto funkci povolíte. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Units = `days`, `hours`, `minutes`, `seconds`, `milliseconds`.  Pro jednotku použijte vždy velká písmena. </br> Určete počet jednotek předáním požadovaného celého čísla pro **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | Celočíselná hodnota je vždy v milisekundách. |

> [!NOTE]
> hodnoty **timeBucketUnit** mohou být vyhlazené až na 7 dní.
> hodnoty **timezoneOffset** nejsou ani UTC ani místní čas.

### <a name="examples"></a>Příklady

Pokud chcete do Time Series Insightsho prostředí přidat definice časových řad jako parametr URL, přidejte:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Použijte příklad definice časových řad pro:

* ID prostředí
* Poslední 60 minut dat
* Výrazy (**F1PressureID**, **F2TempStation**a **F3VibrationPL**), které obsahují volitelné parametry

Pro zobrazení můžete vytvořit následující parametrizovanou adresu URL:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[Time Series Insights Parametrizovaná adresa URL aplikace ![Explorer](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Podívejte se na téma Průzkumník Live pomocí výše uvedeného příkladu [adresy URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) .

Výše uvedená adresa URL popisuje a zobrazí Time Series Insights zobrazení Průzkumníka s parametry. 

* Parametrizované predikáty.

  [![Time Series Insights Průzkumníkovi s parametrizovanými predikáty.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Sdílené zobrazení celého grafu.

  [![sdíleného zobrazení celého grafu.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [zadávat C#dotazy na data pomocí ](time-series-insights-query-data-csharp.md).

* Přečtěte si o [Time Series Insights Exploreru](./time-series-insights-explorer.md).
