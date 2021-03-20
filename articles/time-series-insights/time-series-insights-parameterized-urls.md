---
title: Sdílení vlastních zobrazení s parametrizovanými adresami URL – Azure Time Series Insights | Microsoft Docs
description: Naučte se vytvářet parametrizované adresy URL pro snadné sdílení vlastních zobrazení v Průzkumníkovi v Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 9bf857a66643b1e95ea2559601761a7217babad4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91665323"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Sdílení vlastního zobrazení pomocí parametrizovaných adres URL

Chcete-li sdílet vlastní zobrazení v Průzkumníkovi Azure Time Series Insights, můžete programově vytvořit parametrizovanou adresu URL vlastního zobrazení.

Azure Time Series Insights Explorer podporuje parametry dotazů URL pro určení zobrazení v prostředí přímo z adresy URL. Pouze pomocí adresy URL můžete například určit cílové prostředí, predikát vyhledávání a požadované časové období. Když uživatel vybere přizpůsobenou adresu URL, rozhraní poskytne odkaz přímo na tento prostředek na portálu Azure Time Series Insights. Platí zde zásady přístupu k datům.

> [!TIP]
>
> * Podívejte se na [ukázku bezplatné Azure Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Přečtěte si doprovodnou dokumentaci k [Azure Time Series Insights Exploreru](./time-series-insights-explorer.md) .

## <a name="environment-id"></a>ID prostředí

Parametr `environmentId=<guid>` určuje ID cílového prostředí. Je to součást plně kvalifikovaného názvu domény pro přístup k datům a můžete ji najít v pravém horním rohu přehledu prostředí v Azure Portal. Je vše, co předchází `env.timeseries.azure.com` .

Příkladem parametru ID prostředí je `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Čas

V parametrizované adrese URL můžete zadat absolutní nebo relativní časové hodnoty.

### <a name="absolute-time-values"></a>Absolutní časové hodnoty

Pro absolutní časové hodnoty použijte parametry `from=<integer>` a `to=<integer>`.

* `from=<integer>` je hodnota v milisekundách JavaScriptu určující čas spuštění pro rozsah hledání.
* `to=<integer>` je hodnota v milisekundách JavaScriptu určující čas ukončení pro rozsah hledání.

> [!TIP]
> Pokud chcete data snadno překládat na JavaScriptové milisekundy, zkuste [převaděč časových razítek epocha & UNIX](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relativní časové hodnoty

Pro relativní časovou hodnotu použijte `relativeMillis=<value>` , pokud je *hodnota* v jazyce JavaScript milisekund od nejnovějšího časového razítka přijatého z rozhraní API.

Například `&relativeMillis=3600000` zobrazí data za posledních 60 minut.

Přijaté hodnoty odpovídají nabídce **rychlý čas** v Průzkumníkovi Azure Time Series Insights a zahrnují:

* `1800000` (Posledních 30 minut)
* `3600000` (Posledních 60 minut)
* `10800000` (Poslední 3 hodiny)
* `21600000` (Posledních 6 hodin)
* `43200000` (Posledních 12 hodin)
* `86400000` (Posledních 24 hodin)
* `604800000` (Posledních 7 dnů)
* `2592000000` (Posledních 30 hodin)

### <a name="optional-parameters"></a>Volitelné parametry

`timeSeriesDefinitions=<collection of term objects>`Parametr určuje výrazy predikátu, které se zobrazí v zobrazení Azure Time Series Insights:

| Parametr | Položka URL | Description |
| --- | --- | --- |
| **Jméno** | `\<string>` | Název *podmínky*. |
| **splitBy** | `\<string>` | Název sloupce, podle kterého se bude *rozdělovat*. |
| **measureName** | `\<string>` | Název sloupce obsahujícího *míru*. |
| **predikát** | `\<string>` | Klauzule *kde* pro filtrování na straně serveru. |
| **useSum** | `true` | Volitelný parametr, který určuje použití součtu pro míru. |

> [!NOTE]
> Pokud `Events` je vybraná míra **useSum** , ve výchozím nastavení je vybrán počet.  
> Pokud `Events` políčko není zaškrtnuté, ve výchozím nastavení je vybraná možnost průměr. |

* `multiChartStack=<true/false>`Pár klíč-hodnota umožňuje skládání v grafu.
* `multiChartSameScale=<true/false>`Pár klíč-hodnota umožňuje stejné škálování osy Y napříč podmínkami v rámci volitelného parametru.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>`Umožňuje upravit posuvník intervalu, aby bylo možné podrobnější nebo hladší a agregované zobrazení grafu.  
* Tento `timezoneOffset=<integer>` parametr umožňuje nastavit časové pásmo, které se má u grafu zobrazit jako posun na čas UTC.

| Páry | Description |
| --- | --- |
| `multiChartStack=false` | `true` je ve výchozím nastavení povoleno, aby předával `false` zásobníku. |
| `multiChartStack=false&multiChartSameScale=true` | Pokud chcete ve všech podmínkách použít stejné měřítko osy Y, musíte povolit umísťování do zásobníku.  To je `false` ve výchozím nastavení, takže předávání `true` tuto funkci umožňuje. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Jednotky = `days` , `hours` , `minutes` , `seconds` , `milliseconds` .  Pro jednotku použijte vždy velká písmena. </br> Určete počet jednotek předáním požadovaného celého čísla pro **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | Celočíselná hodnota je vždy v milisekundách. |

> [!NOTE]
> hodnoty **timeBucketUnit** mohou být vyhlazené až na 7 dní.
> hodnoty **timezoneOffset** nejsou ani UTC ani místní čas.

### <a name="examples"></a>Příklady

Pokud chcete do Azure Time Series Insightsho prostředí přidat definice časových řad jako parametr adresy URL, přidejte:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Použijte příklad definice časových řad pro:

* ID prostředí
* Poslední 60 minut dat
* Výrazy (**F1PressureID**, **F2TempStation** a **F3VibrationPL**), které obsahují volitelné parametry

Pro zobrazení můžete vytvořit následující parametrizovanou adresu URL:

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Adresa URL s parametrem Azure Time Series Insights Explorer](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Podívejte se na téma Průzkumník Live pomocí výše uvedeného příkladu [adresy URL](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
) .

Výše uvedená adresa URL popisuje a zobrazí Azure Time Series Insights zobrazení Průzkumníka s parametry.

* Parametrizované predikáty.

  [![Azure Time Series Insights parametrické predikáty Průzkumníka.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Sdílené zobrazení celého grafu.

  [![Sdílené zobrazení celého grafu.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [zadávat dotazy na data pomocí jazyka C#](time-series-insights-query-data-csharp.md).

* Přečtěte si o [Azure Time Series Insights Exploreru](./time-series-insights-explorer.md).
