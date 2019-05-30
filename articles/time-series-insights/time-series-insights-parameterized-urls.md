---
title: Sdílení vlastních zobrazení Azure Time Series Insights pomocí parametrizovaných adres URL | Dokumentace Microsoftu
description: Tento článek popisuje vývoj parametrizovaných adres URL ve službě Azure Time Series Insights za účelem snadného sdílení zobrazení zákazníka.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: dfc04397b1d7e9f3256810cbe469067ae52c99bd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238976"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Sdílení vlastního zobrazení pomocí parametrizovaných adres URL

Sdílení vlastního zobrazení v Průzkumníku Time Series Insights, můžete prostřednictvím kódu programu vytvořit parametrizovanou adresu URL tohoto vlastního zobrazení.

Průzkumník Time Series Insights podporuje parametry dotazů adresy URL, které určují zobrazení v prostředí přímo z adresy URL. Pouze pomocí adresy URL můžete například určit cílové prostředí, predikát vyhledávání a požadované časové období. Když uživatel klikne na přizpůsobenou adresu URL, v rozhraní se zobrazí přímý odkaz na příslušný prostředek na portálu Time Series Insights. Platí zde zásady přístupu k datům.

> [!TIP]
> * Zobrazit bezplatnou [Time Series Insights ukázka](https://insights.timeseries.azure.com/samples).
> * Přečtěte si související [Průzkumník Time Series Insights](./time-series-insights-explorer.md) dokumentaci.

## <a name="environment-id"></a>ID prostředí

Parametr `environmentId=<guid>` určuje ID cílového prostředí. Jedná se o součást plně kvalifikovaný název domény přístup k datům a najdete ho v pravém horním rohu přehledu prostředí na webu Azure Portal. Je to vše před `env.timeseries.azure.com`.

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

Přijmout hodnoty odpovídají Průzkumníka služby Time Series Insights **krátké doby** nabídky a zahrnují:

* `1800000` (Posledních 30 minut)
* `3600000` (Posledních 60 minut)
* `10800000` (Poslední 3 hodiny)
* `21600000` (Posledních 6 hodin)
* `43200000` (Posledních 12 hodin)
* `86400000` (Posledních 24 hodin)
* `604800000` (Posledních 7 dní)
* `2592000000` (Posledních 30 dnů)

### <a name="optional-parameters"></a>Volitelné parametry

`timeSeriesDefinitions=<collection of term objects>` Parametr určuje podmínky zobrazení Time Series Insights:

| Parametr | Adresa URL položky | Popis |
| --- | --- | --- |
| **name** | `\<string>` | Název *podmínky*. |
| **splitBy** | `\<string>` | Název sloupce, podle kterého se bude *rozdělovat*. |
| **%{measurename/** | `\<string>` | Název sloupce obsahujícího *míru*. |
| **predicate** | `\<string>` | Klauzule *kde* pro filtrování na straně serveru. |
| **useSum** | `true` | Volitelný parametr určující používání součtu pro vaši míru. </br>  Mějte na paměti, pokud `Events` je vybranou míru count je standardně vybraná.  </br>  Pokud `Events` není vybrána, ve výchozím nastavení je vybraný průměr. |

* `multiChartStack=<true/false>` Páru klíč hodnota multichartstack v grafu.
* `multiChartSameScale=<true/false>` Páru klíč hodnota umožňuje stejné měřítko osy y napříč termíny v volitelný parametr.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>` Umožňuje upravit interval posuvníku poskytoval podrobnější nebo plynulejší agregovanější zobrazení grafu.  
* `timezoneOffset=<integer>` Parametr umožňuje nastavit časové pásmo grafu tak, aby se ho zobrazit jako posun oproti času UTC.

| Smluv | Popis |
| --- | --- |
| `multiChartStack=false` | `true` ve výchozím nastavení zapnutá umístit `false` do zásobníku. |
| `multiChartStack=false&multiChartSameScale=true` | Pokud chcete ve všech podmínkách použít stejné měřítko osy Y, musíte povolit umísťování do zásobníku.  Má `false` ve výchozím nastavení, takže předání 'true' tuto funkci povolí. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Jednotky = dny, hodiny, minuty, sekundy a milisekundy.  Pro jednotku použijte vždy velká písmena. </br> Počet jednotek se definuje předáním požadované celočíselné hodnoty parametru timeBucketSize.  Plynulost je možné nastavit až na 7 dnů.  |
| `timezoneOffset=-<integer>` | Celočíselná hodnota je vždy v milisekundách. </br> Tato funkce se poněkud liší od možností povolených v Průzkumníku Time Series Insights, kde můžete vybrat místní (čas prohlížeče) nebo čas UTC povolíme. |

### <a name="examples"></a>Příklady

Chcete-li do prostředí Time Series Insights jako parametr adresy URL přidat definice časových řad, přidejte:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Použití ukázkových definic časových řad pro:

* ID prostředí
* Posledních 60 minut dat.
* Podmínky (F1PressureID, F2TempStation a F3VibrationPL), které tvoří volitelné parametry

můžete vytvořit následující parametrizovanou adresu URL pro zobrazení:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Zobrazit v Průzkumníku live [pomocí adresy URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

Adresa URL výše popisuje a vytvoří zobrazení Průzkumník Time Series Insights:

[![Podmínky v Průzkumníku čas Series Insights](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Úplné zobrazení (včetně grafu):

[![Zobrazení grafu](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [dotazování dat pomocí C# ](time-series-insights-query-data-csharp.md).

* Další informace o [Time Series Insights Explorer](./time-series-insights-explorer.md).
