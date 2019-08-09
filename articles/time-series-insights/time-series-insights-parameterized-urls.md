---
title: Sdílení vlastních zobrazení Azure Time Series Insights pomocí parametrizovaných adres URL | Dokumentace Microsoftu
description: Tento článek popisuje vývoj parametrizovaných adres URL ve službě Azure Time Series Insights za účelem snadného sdílení zobrazení zákazníka.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: ce8c74e5c194dbd971ecb65659c4fc8a7150146d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882923"
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

Pokud chcete pro nějaké datum určit čas v milisekundách JavaScriptu, přečtěte si článek [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relativní časové hodnoty

Pro relativní časovou hodnotu použijte parametr `relativeMillis=<value>`, kde *value* je časové období nejnovějších dat v back-endu v milisekundách JavaScriptu.

Například `&relativeMillis=3600000` zobrazí data za posledních 60 minut.

Přijaté hodnoty odpovídají nabídce **rychlý čas** v Průzkumníkovi Time Series Insights a zahrnují:

* `1800000`(Posledních 30 minut)
* `3600000`(Posledních 60 minut)
* `10800000`(Poslední 3 hodiny)
* `21600000`(Posledních 6 hodin)
* `43200000`(Posledních 12 hodin)
* `86400000`(Posledních 24 hodin)
* `604800000`(Posledních 7 dnů)
* `2592000000`(Posledních 30 hodin)

### <a name="optional-parameters"></a>Volitelné parametry

`timeSeriesDefinitions=<collection of term objects>` Parametr určuje podmínku Time Series Insights zobrazení:

| Parametr | Položka URL | Popis |
| --- | --- | --- |
| **name** | `\<string>` | Název *podmínky*. |
| **splitBy** | `\<string>` | Název sloupce, podle kterého se bude *rozdělovat*. |
| **measureName** | `\<string>` | Název sloupce obsahujícího *míru*. |
| **predikát** | `\<string>` | Klauzule *kde* pro filtrování na straně serveru. |
| **useSum** | `true` | Volitelný parametr, který určuje použití součtu pro míru. </br>  Poznámka: Pokud `Events` je vybraná míra, ve výchozím nastavení je vybrán počet.  </br>  Pokud `Events` políčko není zaškrtnuté, ve výchozím nastavení je vybraná možnost průměr. |

* Pár `multiChartStack=<true/false>` klíč-hodnota umožňuje skládání v grafu.
* Pár `multiChartSameScale=<true/false>` klíč-hodnota umožňuje stejné škálování osy Y napříč podmínkami v rámci volitelného parametru.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>` Umožňuje upravit posuvník intervalu, aby bylo možné podrobnější nebo hladší a agregované zobrazení grafu.  
* Tento `timezoneOffset=<integer>` parametr umožňuje nastavit časové pásmo, které se má u grafu zobrazit jako posun na čas UTC.

| Páry | Popis |
| --- | --- |
| `multiChartStack=false` | `true`je ve výchozím nastavení povoleno, `false` aby předával zásobníku. |
| `multiChartStack=false&multiChartSameScale=true` | Pokud chcete ve všech podmínkách použít stejné měřítko osy Y, musíte povolit umísťování do zásobníku.  `false` Ve výchozím nastavení je to proto, že předáním hodnoty true tuto funkci povolí. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Jednotky = dny, hodiny, minuty, sekundy a milisekundy.  Pro jednotku použijte vždy velká písmena. </br> Počet jednotek se definuje předáním požadované celočíselné hodnoty parametru timeBucketSize.  Plynulost je možné nastavit až na 7 dnů.  |
| `timezoneOffset=-<integer>` | Celočíselná hodnota je vždy v milisekundách. </br> Všimněte si, že tato funkce se mírně liší od toho, co jsme povolili v Průzkumníkovi Time Series Insights, kde vám umožníme zvolit místní (čas v prohlížeči) nebo UTC. |

### <a name="examples"></a>Příklady

Pokud chcete do Time Series Insightsho prostředí přidat definice časových řad jako parametr URL, přidejte:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Použijte příklad definice časových řad pro:

* ID prostředí
* Poslední 60 minut dat
* Výrazy (F1PressureID, F2TempStation a F3VibrationPL), které obsahují volitelné parametry

Pro zobrazení můžete vytvořit následující parametrizovanou adresu URL:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Prohlédněte si Průzkumníka v reálném čase [pomocí adresy URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

Výše uvedená adresa URL popisuje a vytvoří zobrazení Průzkumníka Time Series Insights:

[![Time Series Insights – výrazy v Průzkumníkovi](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Úplné zobrazení (včetně grafu):

[![Zobrazení grafu](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Další postup

* Naučte se, jak [zadávat C#dotazy na data pomocí ](time-series-insights-query-data-csharp.md).

* Přečtěte si o [Time Series Insights Exploreru](./time-series-insights-explorer.md).
