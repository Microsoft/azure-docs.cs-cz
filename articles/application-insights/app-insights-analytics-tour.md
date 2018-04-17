---
title: Prohlídka prostřednictvím analýzy ve službě Azure Application Insights | Microsoft Docs
description: Krátký ukázky všechny hlavní dotazů v analýzy, nástroj výkonné vyhledávání služby Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/06/2017
ms.author: mbullwin
ms.openlocfilehash: 4f82e436e25d01bbfa09ec1e8a2efcdf0be8c006
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Prohlídka Analytics ve službě Application Insights
[Analýza](app-insights-analytics.md) je výkonný vyhledávání funkcí [Application Insights](app-insights-overview.md). Tyto stránek popisují dotazovací jazyk analýzy protokolů.

* **[Podívejte se na úvodní video](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Vyzkoušejte Analytics na naše simulované data](https://analytics.applicationinsights.io/demo)**  Pokud aplikace není odesílání dat do služby Application Insights ještě.
* **[SQL-uživatelů tahák](https://aka.ms/sql-analytics)**  překládá nejběžnější idioms.

Podívejme procházení prostřednictvím některé základní dotazy, které vám pomůžou začít.

## <a name="connect-to-your-application-insights-data"></a>Připojit se k datům Application Insights
Otevřete Analytics z vaší aplikace [okno Přehled](app-insights-dashboards.md) ve službě Application Insights:

![Otevřete portal.azure.com otevřete prostředek Application Insights a klikněte na Analytics.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsioquerylanguagequerylanguagetakeoperatorhtml-show-me-n-rows"></a>[Trvat](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html): Zobrazit mi n řádků
Datové body, které protokolu operace uživatele (obvykle HTTP přijatých požadavků ve vaší webové aplikace) jsou uložené v tabulce s názvem `requests`. Každý řádek je telemetrie datový bod, který přijal od Application Insights SDK ve vaší aplikaci.

Začněme prověřením několik ukázkových řádky v tabulce:

![výsledky](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Umístěte kurzor někde v příkazu před kliknutím na Přejít. Příkaz můžete rozdělit přes více než jeden řádek, ale nemáte Vložit prázdné řádky v příkazu. Prázdné řádky jsou vhodné pro zachování několik samostatné dotazy v okně.
>
>

Vyberte sloupce, přetáhněte je seskupit podle sloupce a filtrování:

![Klikněte na výběr sloupců v pravé horní části výsledků](./media/app-insights-analytics-tour/030.png)

Rozbalením libovolné položky zobrazíte podrobností:

![Vyberte tabulky a použití konfigurace sloupců](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Klikněte na záhlaví sloupce, chcete-li změnit pořadí výsledky, které jsou dostupné ve webovém prohlížeči. Ale uvědomte si, že pro sadu výsledků velký počet řádků, které jsou staženy do prohlížeče je omezená. Řazení tímto způsobem jednoduše Seřadí sadu výsledků dotazu vrácená a není vždy zobrazí skutečné nejvyšší nebo nejnižší položky. Chcete-li řadit položky spolehlivě, použijte `top` nebo `sort` operátor.
>
>

## <a name="query-across-applications"></a>Dotazování mezi aplikací
Pokud chcete kombinovat data z více aplikací služby Application Insights, použijte **aplikace** – klíčové slovo zadat aplikaci společně s název tabulky.  Tento dotaz kombinuje požadavky ze dvou různých aplikací pomocí **sjednocení** příkaz.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsioquerylanguagequerylanguagetopoperatorhtml-and-sorthttpsdocsloganalyticsioquerylanguagequerylanguagesortoperatorhtml"></a>[Horní](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) a [řazení](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html)
`take` je užitečné k získání ukázku rychlé výsledků, ale zobrazuje řádky z tabulky seřazeny. Chcete-li získat seřazené zobrazení, použijte `top` (pro ukázku) nebo `sort` (přes celé tabulky).

Zobrazte první n řádky, seřazené podle konkrétního sloupce:

```AIQL

    requests | top 10 by timestamp desc
```

* *Syntaxe:* většina operátory mít parametry – klíčové slovo jako `by`.
* `desc` = sestupné řazení `asc` = vzestupně.

![](./media/app-insights-analytics-tour/260.png)

`top...` je další způsob původce o tom, že `sort ... | take...`. Budeme mít zapsat:

```AIQL

    requests | sort by timestamp desc | take 10
```

Výsledkem bude stejná, ale bude spuštěná trochu pomaleji. (Můžete také napsat `order`, což je zástupce `sort`.)

Záhlaví sloupců v tabulce zobrazení lze také seřadit výsledky na obrazovce. Ale samozřejmě platí, pokud jste použili `take` nebo `top` načíst jenom součást tabulky, kliknutím na záhlaví sloupce bude pouze změnit pořadí záznamy jste načíst.

## <a name="wherehttpsdocsloganalyticsioquerylanguagequerylanguagewhereoperatorhtml-filtering-on-a-condition"></a>[Kde](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html): filtrování na podmínce

Podíváme se, jenom požadavků, které vrátil kód konkrétní výsledku:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

`where` Operátor má logický výraz. Zde jsou některé klíčové body o nich:

* `and`, `or`: Logické operátory
* `==`, `<>`, `!=` : a nejsou rovny.
* `=~`, `!~` : velká a malá písmena řetězec stejná a není rovno. Existují mnoha další operátory porovnání řetězce.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Najít neúspěšných požadavků

Řetězcovou hodnotu převést na celé číslo větší používat-než porovnání:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Čas

Ve výchozím vaše dotazy jsou omezeny na poslední 24 hodin. Můžete však změnit tento rozsah:

![](./media/app-insights-analytics-tour/change-time-range.png)

Časové rozmezí potlačit zápis jakýkoli dotaz, který uvádí `timestamp` v klauzuli where. Příklad:

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

Funkci rozsah čas je ekvivalentní volání za každou zmínky jednoho ze zdrojové tabulky vložit klauzuli 'where'.

`ago(3d)` znamená tří dnů před. Jiné jednotky doby zahrnují hodin (`2h`, `2.5h`), minut (`25m`) a sekund (`10s`).

Další příklady:

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

```

[Dat a časů odkaz](https://docs.loganalytics.io/docs/Language-Reference/Data-types/datetime).


## <a name="projecthttpsdocsloganalyticsioquerylanguagequerylanguageprojectoperatorhtml-select-rename-and-compute-columns"></a>[Projekt](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html): vyberte, přejmenování a výpočetní sloupců
Použití [ `project` ](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) k výběru pouze sloupce, které chcete:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Také můžete přejmenovat sloupce a definovat nové:

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![výsledek](./media/app-insights-analytics-tour/270.png)

* Názvy sloupců může obsahovat mezery nebo symboly v případě jejich jsou v závorkách, jako to: `['...']` nebo `["..."]`
* `%` je obvyklé Operátor modulo.
* `1d` (která je číslice, pak měl ') je časový interval literálu znamená jeden den. Tady jsou některé další literály časový interval: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) zaokrouhlí dolů na nejbližší násobek hodnotu zadáte hodnotu. Proto `floor(aTime, 1s)` zaokrouhlí dolů nejbližší sekundu čas.

Výrazy může zahrnovat všechny běžných operátorů (`+`, `-`,...), a rozsah užitečné funkce.

## <a name="extend"></a>Rozšíření
Pokud chcete přidat sloupce do již existující, použijte [ `extend` ](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Pomocí [ `extend` ](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html) je míň podrobné než [ `project` ](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) Pokud chcete zachovat existující sloupce.

### <a name="convert-to-local-time"></a>Převést na místní čas

Časová razítka jsou vždycky ve standardu UTC. Pokud jste na pobřeží Tichomoří USA a se jedná o zimní, může to jako:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```


## <a name="summarizehttpsdocsloganalyticsioquerylanguagequerylanguagesummarizeoperatorhtml-aggregate-groups-of-rows"></a>[Shrnout](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html): agregovat skupiny řádků
`Summarize` použije zadanou *agregační funkce* přes skupiny řádků.

Například čas trvá odpovědět na požadavek webové aplikace je uvedená v poli `duration`. Podívejme se, Průměrná doba odezvy pro všechny požadavky:

![](./media/app-insights-analytics-tour/410.png)

Nebo výsledek jsme může oddělení do požadavků odlišné názvy:

![](./media/app-insights-analytics-tour/420.png)

`Summarize` shromažďuje datových bodů v datovém proudu do skupin, pro kterou `by` klauzule vyhodnotí stejně. Každá hodnota v `by` výrazu - každý je název jedinečný operace v předchozím příkladu - výsledkem řádek v tabulce výsledků.

Nebo jsme může seskupení výsledků podle denní dobu:

![](./media/app-insights-analytics-tour/430.png)

Všimněte si, jak používáme `bin` – funkce (neboli `floor`). Pokud jsme použili `by timestamp`, každý řádek vstupu by skončit ve své vlastní malé skupiny. Pro všechny průběžné skalárních jako časy nebo čísla, musíme průběžné rozsahu rozdělit spravovat počet jednotlivých hodnot. `bin` -který je právě známé zaokrouhlení nižší `floor` funkce – je nejjednodušší způsob, jak to udělat.

Můžeme použít stejný postup ke snížení rozsahy řetězců:

![](./media/app-insights-analytics-tour/440.png)

Všimněte si, že můžete použít `name=` nastavit název sloupec výsledků, buď ve výrazech agregace nebo klauzuli by.

## <a name="counting-sampled-data"></a>Počítání vzorků dat
`sum(itemCount)` je doporučené agregace počítat události. V mnoha případech itemCount = 1, takže funkce jednoduše spočítá počet řádků ve skupině. Ale když [vzorkování](app-insights-sampling.md) je v provozu se pouze část původní události zachová jako datových bodů ve službě Application Insights, aby byl pro každý datový bod, uvidíte, `itemCount` události.

Například pokud vzorkování zahodí 75 % původní události, pak itemCount == 4 v udržených záznamy – to znamená, pro každý záznam zachované existovaly čtyři původní záznamy.

Adaptivního vzorkování způsobí, že itemCount být v obdobích, kdy aplikace je se nejčastěji používá vyšší.

Shrnutí itemCount proto poskytuje dobrý odhad původní počet událostí.

![](./media/app-insights-analytics-tour/510.png)

K dispozici je také `count()` agregace (a počet operaci) pro případy, kdy Opravdu chcete počet řádků ve skupině.

Je rozsah [funkce agregace](https://docs.loganalytics.io/learn/tutorials/aggregations.html).

## <a name="charting-the-results"></a>Vytváření grafů výsledky
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Ve výchozím nastavení zobrazí výsledky jako tabulku:

![](./media/app-insights-analytics-tour/225.png)

Můžeme dělat líp, než tabulka zobrazení. Podívejme se na výsledky v zobrazení grafu se svislicí panel možnost:

![Klikněte na graf, vyberte svislý pruhový graf a přiřadit x a y osy](./media/app-insights-analytics-tour/230.png)

Všimněte si, že i když jsme nebyla seřadit výsledky podle času (jak je vidět v zobrazení tabulky), zobrazení grafu vždy zobrazuje data a času ve správném pořadí.


## <a name="timecharts"></a>Timecharts
Zobrazit, kolik události existuje se každou hodinu:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Vyberte možnost zobrazení grafu:

![timechart](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Více řad
Více výrazů v `summarize` klauzule vytvoří více sloupců.

Více výrazů v `by` klauzule vytvoří více řádků, jeden pro každou kombinaci hodnot.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tabulka požadavků podle hodin a umístění](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Rozdělit grafu pomocí dimenzí
Pokud jste grafu tabulku, která má sloupec řetězce a je číselný sloupec, řetězec slouží k rozdělení číselná data do samostatné řady bodů. Pokud existuje více než jeden sloupec řetězec, můžete použít jako diskriminátoru sloupec.

![Segment graf analýzy](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Odraz rychlost

Převeďte na logickou hodnotu na řetězec, můžete použít jako diskriminátoru:

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>Zobrazení více metriky
Pokud grafu tabulku, která má více než jeden číselný sloupec, kromě časové razítko, můžete zobrazit libovolnou kombinací.

![Segment graf analýzy](./media/app-insights-analytics-tour/110.png)

Je nutné vybrat **nemáte rozdělení** mohli vybrat více číselné sloupce. Nelze rozdělit sloupec řetězce ve stejnou dobu jako zobrazení více než jeden číselný sloupec.

## <a name="daily-average-cycle"></a>Průměrný denní cyklu
Jak se liší využití přes průměrného dne?

Počet požadavků podle času modulo jeden den, binned do hodiny:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Spojnicový graf hodin v průměrného dne](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Všimněte si, že máme aktuálně převést dobách trvání na data a času, aby bylo možné zobrazit na spojnicový graf.
>
>

## <a name="compare-multiple-daily-series"></a>Porovnání více denní řad
Jak využití liší přes čas, kdy v různých zemí?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![Rozdělení podle client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Vykreslení distribuce
Kolik relací dochází z různých délek?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

Poslední řádek je potřeba převést na typ datetime. Aktuálně osy x grafu se zobrazuje jako skalární hodnota, pouze pokud je hodnota datetime.

`where` Klauzule vyloučí jednorázové relací (sessionDuration == 0) a nastaví délku osy x.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsioquerylanguagequerylanguagepercentilesaggfunctionhtml"></a>[Percentily](https://docs.loganalytics.io/queryLanguage/query_language_percentiles_aggfunction.html)
Jaké rozsahy doby trvání zahrnují různá procenta relace?

Pomocí výše uvedeném dotazu, ale nahraďte poslední řádek:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Také jsme odebrali horní limit v where klauzule, aby bylo možné získat správné údaje včetně všechny relace s více než jeden požadavek:

![výsledek](./media/app-insights-analytics-tour/180.png)

Odkud jsme uvidíte, že:

* 5 % relací být méně než tři minuty 34s;
* 50 % relací poslední nejvýše 36 minut;
* 5 % relací poslední více než 7 dní

Získat samostatné rozpis pro každé země, jsme právě mít aby shrnout sloupci client_CountryOrRegion samostatně prostřednictvím obě operátory:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Spojit
Budeme mít přístup k několika tabulky, včetně požadavky a výjimkami.

Výjimky související s požadavek, který vrátil neplatnou odpověď najdete jsme se můžete zapojit do tabulky na `session_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


Je vhodné použít `project` vyberte právě sloupce je třeba před provedením spojení.
V klauzulích stejné jsme přejmenovat sloupec časového razítka.

## <a name="lethttpsdocsloganalyticsioquerylanguagequerylanguageletstatementhtml-assign-a-result-to-a-variable"></a>[Umožní](https://docs.loganalytics.io/queryLanguage/query_language_letstatement.html): přiřadit výsledek proměnné

Použití `let` oddělit části předchozí výrazu. Výsledky jsou stejné jako:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> V klientovi analýzy, umístěte prázdné řádky mezi částí dotazu. Ujistěte se, že všechny jeho spuštění.
>

Použití `toscalar` převést na hodnotu jedna buňka tabulky:

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Funkce

Použití *Let* definice funkce:

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Přístup k vnořených objektů
Vnořených objektů lze snadno přistupovat. V datovém proudu výjimky, můžete například zjistit strukturovaných objekty takto:

![výsledek](./media/app-insights-analytics-tour/520.png)

Můžete ho vyrovnání výběrem vlastnosti, které vás zajímají:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Všimněte si, že budete muset přetypování výsledek, který má příslušného typu.


## <a name="custom-properties-and-measurements"></a>Vlastní vlastnosti a měření
Pokud vaše aplikace připojí [vlastní dimenze (Vlastnosti) a vlastní měření](app-insights-api-custom-events-metrics.md#properties) na události, pak se zobrazí je v `customDimensions` a `customMeasurements` objekty.

Například pokud vaše aplikace obsahuje:

```csharp

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

K extrakci v Analytics tyto hodnoty:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type

```

Chcete-li ověřit, zda je vlastní dimenze určitého typu:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

## <a name="dashboards"></a>Řídicí panely
Aby bylo možné shromáždit všechny vaše nejdůležitější grafů a tabulek je budete moct připnout výsledky na řídicí panel.

* [Řídicí panel Azure sdílené](app-insights-dashboards.md#share-dashboards): Kliknutím na ikonu Připnutí. Než to uděláte, musíte mít sdílené řídicího panelu. Na portálu Azure otevřete nebo vytvořit řídicí panel a klikněte na sdílenou složku.
* [Řídicí panel Power BI](app-insights-export-power-bi.md): klikněte na exportovat, Power BI dotazu. Výhodou této alternativní je, že můžete zobrazit dotaz spolu s další výsledky z široké škály zdrojů.

## <a name="combine-with-imported-data"></a>Kombinovat s importovanými daty

Sestavy analýzy vypadat skvělé na řídicím panelu, ale někdy chcete přeložit data do více stravitelné formuláře. Předpokládejme například, že ověření uživatelé se budou identifikovat telemetrie podle alias. Chcete zobrazit jejich skutečné názvy ve výsledcích. Chcete-li to provést, musíte soubor CSV, který se mapuje na skutečné názvy z aliasy.

Můžete importovat soubor dat a použít ho stejně jako všechny standardní tabulek (požadavky, výjimky a tak dále). Buď dotaz na svůj vlastní nebo se připojte s jinou tabulkou. Například, pokud máte tabulku s názvem usermap a má sloupce `realName` a `userId`, můžete ho použít k překladu `user_AuthenticatedId` pole v požadavku telemetrie:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

V části Import tabulky, v okně schématu **jiných zdrojů dat**, postupujte podle pokynů a přidat nový zdroj dat, tím, že nahrajete ukázková data. Pak můžete použít tuto definici k nahrání tabulky.

Funkce importu je aktuálně ve verzi preview, takže původně uvidíte odkaz "Kontaktujte nás" v části "Jiné zdroje dat." Tato možnost slouží k zaregistrovat do programu preview a na odkaz se nahradí klikněte tlačítko "Přidat nový zdroj dat".


## <a name="tables"></a>Tabulky
Datový proud telemetrie přijaté z vaší aplikace je přístupná prostřednictvím několik tabulek. Schéma vlastnosti, které jsou k dispozici pro každou tabulku je viditelný v levé části okna.

### <a name="requests-table"></a>Tabulka požadavků
Počet HTTP žádosti na vaší webové aplikace a segment podle názvu stránky:

![Počet požadavků segmentované podle názvu](./media/app-insights-analytics-tour/analytics-count-requests.png)

Vyhledá požadavky, které nesplní většinu:

![Počet požadavků segmentované podle názvu](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Tabulka vlastní události
Pokud používáte [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) odesílat vlastní události, můžete přečíst je z této tabulky.

Podívejme příklad kde kódu aplikace obsahuje tyto řádky:

```csharp

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Zobrazí četnost tyto události:

![Zobrazení počet vlastní události](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Extrahujte měření a dimenze z události:

![Zobrazení počet vlastní události](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Vlastní metriky tabulky
Pokud používáte [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) k odeslání vlastní metriky hodnoty, najdete své výsledky v **customMetrics** datového proudu. Příklad:  

![Vlastní metriky v analytics Application Insights](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> V [Průzkumníku metrik](app-insights-metrics-explorer.md), všechny vlastní měření připojit k libovolnému typu telemetrie objeví spolu v okně metriky společně s metriky odeslaných pomocí `TrackMetric()`. Ale v analýzy, vlastní měření připojených stále libovolného typu byly provedeny na - události nebo požadavky a tak dále - při poslal TrackMetric metrika se zobrazí v vlastní datový proud telemetrie.
>
>

### <a name="performance-counters-table"></a>Tabulka čítače výkonu
[Čítače výkonu](app-insights-performance-counters.md) můžete zobrazit základní systémové metriky pro vaši aplikaci, například CPU, paměť a využití sítě. Můžete nakonfigurovat SDK k odesílání další čítače, včetně vlastní vlastní čítače.

**Čítače výkonu** schématu zpřístupní `category`, `counter` název, a `instance` název jednotlivých čítačů výkonu. Názvy instancí čítače platí jenom pro některé čítače výkonu a obvykle označení názvu procesu, k němuž se vztahuje na počet. V telemetrii pro každou aplikaci zobrazí se pouze čítačů pro tuto aplikaci. Například pokud chcete zobrazit jsou k dispozici co čítače:

![Čítače výkonu v analytics Application Insights](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Pokud chcete získat graf dostupné paměti za vybrané období:

![Paměť timechart v analytics Application Insights](./media/app-insights-analytics-tour/analytics-available-memory.png)

Jako další telemetrií **čítače výkonu** také má sloupec `cloud_RoleInstance` určující identitu hostitelský počítač, na kterém aplikace běží. Chcete-li například porovnat výkon vaší aplikace na různé počítače:

![Výkon oddělených instance role ve službě Application Insights analytics](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Výjimky tabulky
[Výjimky hlášených aplikace](app-insights-asp-net-exceptions.md) jsou k dispozici v této tabulce.

Pokud chcete najít požadavek HTTP, který aplikace byla zpracování, pokud byla vyvolána výjimka, připojení na operation_Id:

![Připojení výjimky s požadavky na operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Tabulka časování prohlížeče
`browserTimings` Zobrazuje data zatížení stránky shromažďují v prohlížečích vašich uživatelů.

[Nastavit aplikaci pro telemetrických dat na straně klienta](app-insights-javascript.md) Chcete-li zobrazit tyto metriky.

Schéma zahrnuje [metrika označující délek různých fázích načítání proces stránky](app-insights-javascript.md#page-load-performance). (Není indikují dobu, kterou uživatelé přečíst na stránce.)  

Zobrazit popularities různých stránek a načíst časy pro jednotlivé stránky:

![Časů načtení stránky v Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>Tabulky výsledků dostupnosti
`availabilityResults` Zobrazuje výsledky vaše [webové testy](app-insights-monitor-web-app-availability.md). Každé spuštění testů z umístění každého testu se hlásí samostatně.

![Časů načtení stránky v Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Tabulka závislosti
Obsahuje výsledky volání, že vaše aplikace vytvoří databáze a rozhraní REST API a dalších žádostí na TrackDependency(). Zahrnuje taky volání AJAX provedená z prohlížeče.

Volání AJAX z prohlížeče:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Závislost volání ze serveru:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Vždy zobrazovat výsledky v závislosti na straně serveru `success==False` Pokud není nainstalován Application Insights Agent. Dalších dat, ale jsou správné.

### <a name="traces-table"></a>Tabulka trasování
Obsahuje telemetrické zprávy odesílané vaší aplikace pomocí TrackTrace(), nebo [jiných rozhraní protokolování](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Video 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Pokročilé dotazy:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>Další postup
* [Referenční dokumentace jazyka Analytics](app-insights-analytics-reference.md)
* [SQL-uživatelů tahák](https://aka.ms/sql-analytics) překládá nejběžnější idioms.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
