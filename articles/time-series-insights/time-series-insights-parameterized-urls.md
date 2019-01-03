---
title: Sdílení vlastních zobrazení Azure Time Series Insights pomocí parametrizovaných adres URL | Dokumentace Microsoftu
description: Tento článek popisuje vývoj parametrizovaných adres URL ve službě Azure Time Series Insights za účelem snadného sdílení zobrazení zákazníka.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: 305d81d6614d1f0654e0d96b10f1759498683003
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558460"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Sdílení vlastního zobrazení pomocí parametrizovaných adres URL

Pokud chcete sdílet vlastní zobrazení v průzkumníku Time Series Insights, můžete prostřednictvím kódu programu vytvořit parametrizovanou adresu URL tohoto vlastního zobrazení.

Průzkumník Time Series Insights podporuje parametry dotazů adresy URL, které určují zobrazení v prostředí přímo v adrese URL.  Pouze pomocí adresy URL můžete například určit cílové prostředí, predikát vyhledávání a požadované časové období. Když uživatel klikne na přizpůsobenou adresu URL, v rozhraní se zobrazí přímý odkaz na příslušný prostředek na portálu Time Series Insights.  Platí zde zásady přístupu k datům. 

## <a name="environment-id"></a>ID prostředí

Parametr `environmentId=<guid>` určuje ID cílového prostředí.  Jedná se o součást plně kvalifikovaného názvu domény pro přístup k datům a najdete jej v pravém horním rohu přehledu prostředí na webu Azure Portal.  Je to vše před `env.timeseries.azure.com`. Příkladem parametru ID prostředí je `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Čas

V parametrizované adrese URL můžete zadat absolutní nebo relativní časové hodnoty.

### <a name="absolute-time-values"></a>Absolutní časové hodnoty

Pro absolutní časové hodnoty použijte parametry `from=<integer>` a `to=<integer>`. 

`from=<integer>` je hodnota v milisekundách JavaScriptu určující čas spuštění pro rozsah hledání.

`to=<integer>` je hodnota v milisekundách JavaScriptu určující čas ukončení pro rozsah hledání. 

Pokud chcete pro nějaké datum určit čas v milisekundách JavaScriptu, přečtěte si článek [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relativní časové hodnoty

Pro relativní časovou hodnotu použijte parametr `relativeMillis=<value>`, kde *value* je časové období nejnovějších dat v back-endu v milisekundách JavaScriptu.

Například `&relativeMillis=3600000` zobrazí data za posledních 60 minut.

Přijatelné hodnoty odpovídají nabídce **quick time** (rychlý čas) průzkumníka Time Series Insights a patří mezi ně následující hodnoty:

- 1800000 (posledních 30 minut)
- 3600000 (posledních 60 minut)
- 10800000 (poslední 3 hodiny)
- 21600000 (posledních 6 hodin)
- 43200000 (posledních 12 hodin)
- 86400000 (posledních 24 hodin)
- 604800000 (posledních 7 dnů)
- 2592000000 (posledních 30 dnů)

### <a name="optional-parameters"></a>Volitelné parametry

Parametr `timeSeriesDefinitions=<collection of term objects>` určuje podmínky zobrazení Time Series Insights, kde:

- "name":"<string>"
  - Název *podmínky*.
- "splitBy":"<string>"
  - Název sloupce, podle kterého se bude *rozdělovat*.
- "measureName":"<string>"
  - Název sloupce obsahujícího *míru*.
- "predicate":"<string>"
  - Klauzule *kde* pro filtrování na straně serveru.
- "useSum":"true"
  - Volitelný parametr určující používání součtu pro vaši míru.  Pokud je jako míra vybraná hodnota Events, je standardně vybraný počet.  Pokud není vybrána hodnota Events, je standardně vybraný průměr.  

Parametr 'multiChartStack=<true/false>' povoluje umístění grafu do zásobníku a parametr 'multiChartSameScale=<true/false>' umožňuje stejné měřítko osy Y v podmínkách volitelného parametru.  

- 'multiChartStack=false'
  - Ve výchozím nastavení je použita hodnota „true“. Pokud chcete graf umístit do zásobníku, nastavte „false“.
- 'multiChartStack=false&multiChartSameScale=true' 
  - Pokud chcete ve všech podmínkách použít stejné měřítko osy Y, musíte povolit umísťování do zásobníku.  Výchozí hodnotou je „false“. Pokud chcete tuto funkci povolit, nastavte hodnotu „true“.  
  
Parametr 'timeBucketUnit=<Unit>&timeBucketSize=<integer>' umožňuje upravit interval posuvníku tak, aby poskytoval podrobnější nebo plynulejší (agregovanější) zobrazení grafu.  
- 'timeBucketUnit=<Unit>&timeBucketSize=<integer>'
  - Jednotky = dny, hodiny, minuty, sekundy a milisekundy.  Pro jednotku použijte vždy velká písmena.
  - Počet jednotek se definuje předáním požadované celočíselné hodnoty parametru timeBucketSize.  Plynulost je možné nastavit až na 7 dnů.  
  
Parametr 'timezoneOffset=<integer>' umožňuje nastavit časové pásmo grafu tak, aby bylo možné ho zobrazit jako posun oproti času UTC.  
  - 'timezoneOffset=-<integer>'
    - Celočíselná hodnota je vždy v milisekundách.  
    - Tato funkce se trochu liší od možností povolených v průzkumníku TSI, kde můžete vybrat místní čas prohlížeče nebo čas UTC.  
 
### <a name="examples"></a>Příklady

Například pokud chcete jako parametr adresy URL přidat definice časových řad, můžete použít následující:

```https
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Pomocí těchto ukázkových definic časových řad pro 

- ID prostředí,
- data za posledních 60 minut,
- podmínky (F1PressureID, F2TempStation a F3VibrationPL), které tvoří volitelné parametry,
 
můžete pro zobrazení vytvořit následující parametrizovanou adresu URL:

```https
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Pokud jste k sestavení zobrazení popsaného předchozí adresou URL použili průzkumníka Time Series Insights, bude vypadat takto:

![Podmínky v průzkumníku Time Series Insights](media/parameterized-url/url1.png)

Úplné zobrazení (včetně grafu) bude vypadat takto:

![Zobrazení grafu](media/parameterized-url/url2.png)

## <a name="next-steps"></a>Další postup
[Dotazování dat pomocí jazyka C#](time-series-insights-query-data-csharp.md)
