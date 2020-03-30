---
title: Sdílení vlastních zobrazení s parametrizovanými adresami URL – Přehledy azure time series | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit parametrizované adresy URL pro snadné sdílení přizpůsobených zobrazení průzkumníka v Azure Time Series Insights.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024360"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Sdílení vlastního zobrazení pomocí parametrizovaných adres URL

Chcete-li sdílet vlastní zobrazení v Průzkumníku přehledů časových řad, můžete programově vytvořit parametrizovanou adresu URL vlastního zobrazení.

Průzkumník přehledů časové řady podporuje parametry dotazu URL k určení zobrazení v prostředí přímo z adresy URL. Pouze pomocí adresy URL můžete například určit cílové prostředí, predikát vyhledávání a požadované časové období. Když uživatel vybere přizpůsobenou adresu URL, rozhraní poskytuje odkaz přímo na tento prostředek na portálu Time Series Insights. Platí zde zásady přístupu k datům.

> [!TIP]
> * Prohlédněte si bezplatnou [ukázku Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Přečtěte si doprovodnou dokumentaci [průzkumníka Time Series Insights Explorer.](./time-series-insights-explorer.md)

## <a name="environment-id"></a>ID prostředí

Parametr `environmentId=<guid>` určuje ID cílového prostředí. Je součástí hlavního přístupu k datům a najdete ho v pravém horním rohu přehledu prostředí na webu Azure Portal. Je to vše před `env.timeseries.azure.com`.

Příkladem parametru ID prostředí je `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

V parametrizované adrese URL můžete zadat absolutní nebo relativní časové hodnoty.

### <a name="absolute-time-values"></a>Absolutní časové hodnoty

Pro absolutní časové hodnoty použijte parametry `from=<integer>` a `to=<integer>`.

* `from=<integer>` je hodnota v milisekundách JavaScriptu určující čas spuštění pro rozsah hledání.
* `to=<integer>` je hodnota v milisekundách JavaScriptu určující čas ukončení pro rozsah hledání.

> [!TIP]
> Chcete-li snadno překládat data do milisekund javascriptu, zkuste [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relativní časové hodnoty

Pro relativní časovou `relativeMillis=<value>`hodnotu použijte , kde je *hodnota* v milisekundách JavaScriptu z posledního časového razítka přijatého z rozhraní API.

Například `&relativeMillis=3600000` zobrazí data za posledních 60 minut.

Přijaté hodnoty odpovídají rychlé **nabídce rychlého času** průzkumníka Time Series Insights a zahrnují:

* `1800000`(Posledních 30 minut)
* `3600000`(Posledních 60 min)
* `10800000`(Poslední 3 hodiny)
* `21600000`(Posledních 6 hodin)
* `43200000`(Posledních 12 hodin)
* `86400000`(Posledních 24 hodin)
* `604800000`(Posledních 7 dní)
* `2592000000`(Posledních 30 hodin)

### <a name="optional-parameters"></a>Volitelné parametry

Parametr `timeSeriesDefinitions=<collection of term objects>` určuje predikátové termíny, které se zobrazí v zobrazení Přehledy časových řad:

| Parametr | Položka adresy URL | Popis |
| --- | --- | --- |
| **Jméno** | `\<string>` | Název *podmínky*. |
| **splitBy** | `\<string>` | Název sloupce, podle kterého se bude *rozdělovat*. |
| **název_míry** | `\<string>` | Název sloupce obsahujícího *míru*. |
| **Predikátu** | `\<string>` | Klauzule *kde* pro filtrování na straně serveru. |
| **useSum** | `true` | Volitelný parametr, který určuje použití součtu pro míru. |

> [!NOTE]
> Pokud `Events` je vybraná míra **useSum,** je ve výchozím nastavení vybrána míra počítání.  
> Pokud `Events` není vybrána možnost Průměr, je vybrána ve výchozím nastavení. |

* Dvojice `multiChartStack=<true/false>` klíč hodnota umožňuje stohování v grafu.
* Dvojice `multiChartSameScale=<true/false>` klíč-hodnota umožňuje stejné měřítko osy Y napříč podmínkami v rámci volitelného parametru.  
* Umožňuje `timeBucketUnit=<Unit>&timeBucketSize=<integer>` upravit posuvník intervalu tak, aby poskytoval podrobnější nebo hladší a agregovanější zobrazení grafu.  
* Parametr `timezoneOffset=<integer>` umožňuje nastavit časové pásmo pro graf, který má být zobrazen v jako posun na Čas UTC.

| Dvojice (y) | Popis |
| --- | --- |
| `multiChartStack=false` | `true`je ve výchozím `false` nastavení povolena, takže přejděte do zásobníku. |
| `multiChartStack=false&multiChartSameScale=true` | Pokud chcete ve všech podmínkách použít stejné měřítko osy Y, musíte povolit umísťování do zásobníku.  Je ve `false` výchozím nastavení, `true` takže předávání umožňuje tuto funkci. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Jednotky `days`= `hours` `minutes`, `seconds` `milliseconds`, , , .  Pro jednotku použijte vždy velká písmena. </br> Definujte počet jednotek předáním požadované holčičí číslo pro **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | Celočíselná hodnota je vždy v milisekundách. |

> [!NOTE]
> **hodnoty timeBucketUnit** lze vyhlazovat až 7 dní.
> hodnoty **timezoneOffset** nejsou ani UTC, ani místní čas.

### <a name="examples"></a>Příklady

Chcete-li přidat definice časových řad do prostředí Time Series Insights jako parametr URL, přidejte:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Použijte příklad definice časových řad pro:

* ID prostředí
* Posledních 60 minut dat
* Termíny (**F1PressureID**, **F2TempStation**a **F3VibrationPL**), které obsahují volitelné parametry

Pro zobrazení můžete vytvořit následující parametrizovanou adresu URL:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Parametrizovaná adresa URL průzkumníka Time Series Insights](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Podívejte se na živý explorer pomocí výše [uvedeného příkladu adresy URL.](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}])

Výše uvedená adresa URL popisuje a zobrazuje zobrazení průzkumníka Time Series Insights. 

* Parametrizované predikáty.

  [![Time Series Insights explorer parametrizované predikáty.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Sdílené úplné zobrazení grafu.

  [![Sdílené úplné zobrazení grafu.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [dotazovat data pomocí jazyka C#](time-series-insights-query-data-csharp.md).

* Další informace o [Průzkumníku přehledů časových řad](./time-series-insights-explorer.md).
