---
title: Sdílení Azure Time Series Insights vlastních zobrazení s parametrizovanými adresami URL | Microsoft Docs
description: Tento článek popisuje, jak ve Azure Time Series Insights vyvíjet parametrizované adresy URL, aby bylo možné snadno sdílet zobrazení zákazníka.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: cad57e3e7e52ec291819110bab9d8d79f51e5a2f
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958157"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Sdílení vlastního zobrazení pomocí parametrizované adresy URL

Chcete-li sdílet vlastní zobrazení v Průzkumníkovi Time Series Insights, můžete programově vytvořit parametrizovanou adresu URL vlastního zobrazení.

Průzkumník Time Series Insights podporuje parametry dotazu URL pro určení zobrazení v prostředí přímo z adresy URL. Například jenom pomocí adresy URL můžete zadat cílové prostředí, predikát vyhledávání a požadovaný časový rozsah. Když uživatel vybere přizpůsobenou adresu URL, rozhraní poskytne odkaz přímo na tento prostředek na portálu Time Series Insights. Platí zásady přístupu k datům.

> [!TIP]
> * Podívejte se na [ukázku bezplatné Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Přečtěte si doprovodnou dokumentaci k [Time Series Insights Exploreru](./time-series-insights-explorer.md) .

## <a name="environment-id"></a>ID prostředí

Parametr `environmentId=<guid>` určuje ID cílového prostředí. Je to součást plně kvalifikovaného názvu domény pro přístup k datům a můžete ji najít v pravém horním rohu přehledu prostředí v Azure Portal. Je vše, co předchází `env.timeseries.azure.com`.

Příkladem parametru ID prostředí je `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Interval

Můžete zadat absolutní nebo relativní hodnoty času s parametrizovanou adresou URL.

### <a name="absolute-time-values"></a>Absolutní časové hodnoty

U absolutních hodnot času použijte parametry `from=<integer>` a `to=<integer>`.

* `from=<integer>` je hodnota v milisekundách jazyka JavaScript v čase spuštění rozsahu hledání.
* @no__t – 0is hodnotu v milisekundách koncového času pro rozsah hledání.

Chcete-li identifikovat dobu v jazyce JavaScript milisekund pro datum, přečtěte si téma [převaděč časových razítek epocha & UNIX](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relativní časové hodnoty

Pro relativní časovou hodnotu použijte `relativeMillis=<value>`, kde je *hodnota* v jazyce JavaScript milisekund od nejnovějších dat v back-endu.

Například `&relativeMillis=3600000` zobrazuje nejnovější 60 minut dat.

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

Parametr `timeSeriesDefinitions=<collection of term objects>` určuje výrazy Time Series Insightsho zobrazení:

| Parametr | Položka URL | Popis |
| --- | --- | --- |
| **Jméno** | `\<string>` | Název *termínu*. |
| **splitBy** | `\<string>` | Název sloupce, podle kterého se má *rozdělit*. |
| **measureName** | `\<string>` | Název sloupce *míry* |
| **predikát** | `\<string>` | Klauzule *WHERE* pro filtrování na straně serveru. |
| **useSum** | `true` | Volitelný parametr, který určuje použití součtu pro míru. </br>  Poznámka: Pokud je vybraná míra `Events`, ve výchozím nastavení je vybraná možnost počet.  </br>  Pokud není vybraná možnost `Events`, je ve výchozím nastavení vybrána možnost průměr. |

* Pár klíč-hodnota `multiChartStack=<true/false>` povoluje vrstvení v grafu.
* Pár klíč-hodnota `multiChartSameScale=<true/false>` umožňuje stejné škálování osy Y napříč podmínkami v rámci volitelného parametru.  
* @No__t-0 umožňuje upravit posuvník intervalu tak, aby bylo možné podrobnější nebo hladší a agregované zobrazení grafu.  
* Parametr `timezoneOffset=<integer>` umožňuje nastavit časové pásmo, které se má v grafu zobrazit jako posun na čas UTC.

| Páry | Popis |
| --- | --- |
| `multiChartStack=false` | `true` je ve výchozím nastavení povolený, takže předejte `false` do zásobníku. |
| `multiChartStack=false&multiChartSameScale=true` | Pro použití stejného měřítka osy Y v rámci podmínek musí být povoleno skládání.  Ve výchozím nastavení je to `false`, takže při předání hodnoty true se tato funkce povoluje. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Jednotky = dny, hodiny, minuty, sekundy, milisekundy.  Jednotku vždy velkými písmeny. </br> Určete počet jednotek předáním požadovaného celého čísla pro timeBucketSize.  Všimněte si, že budete hladké až 7 dní.  |
| `timezoneOffset=-<integer>` | Celé číslo je vždy v milisekundách. </br> Všimněte si, že tato funkce se mírně liší od toho, co jsme povolili v Průzkumníkovi Time Series Insights, kde vám umožníme zvolit místní (čas v prohlížeči) nebo UTC. |

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

[@no__t – výrazy pro 1Time Series Insights Explorer](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Úplné zobrazení (včetně grafu):

[@no__t – zobrazení 1Chart](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [zadávat C#dotazy na data pomocí ](time-series-insights-query-data-csharp.md).

* Přečtěte si o [Time Series Insights Exploreru](./time-series-insights-explorer.md).
