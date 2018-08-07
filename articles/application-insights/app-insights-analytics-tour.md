---
title: Prohlédněte si prostřednictvím analýzy ve službě Azure Application Insights | Dokumentace Microsoftu
description: Krátké ukázky všechny hlavní dotazy v Analytics, nástroj výkonné hledání Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: mbullwin
ms.openlocfilehash: 470779f80e998c3908cf28328cfb415d98c5e06c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579251"
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Seznámení s analýzami v nástroji Application Insights
[Analytics](app-insights-analytics.md) je součástí výkonné vyhledávání [Application Insights](app-insights-overview.md). Tyto stránky popisují dotazovací jazyk Log Analytics.

* **[Podívejte se na úvodní video](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Vyzkoušejte si analýzy na naše Simulovaná data](https://analytics.applicationinsights.io/demo)**  Pokud vaše aplikace neodesílá data do Application Insights ještě.
* **[SQL-uživatelů tahák](https://aka.ms/sql-analytics)**  přeloží nejběžnější idiomy.

Pojďme se provede některé základní dotazy, které vám pomůžou začít.

## <a name="connect-to-your-application-insights-data"></a>Připojit se k datům služby Application Insights
Otevřít analýzu z vaší aplikace [okno přehledu](app-insights-dashboards.md) ve službě Application Insights:

![Otevřené stránce portal.azure.com otevřete prostředek Application Insights a klikněte na tlačítko Analytics.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsiodocslanguage-referencetabular-operators-show-me-n-rows"></a>[Využijte](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators): Zobrazit n řádků
Datové body, kteří se přihlašují operace uživatelů (obvykle. žádosti HTTP přijaté vaší webové aplikace) jsou uložené v tabulce nazvané `requests`. Každý řádek představuje datový bod telemetrická data přijatá z Application Insights SDK do vaší aplikace.

Začněme tím, že kontroluje několik řádků ukázkové tabulky:

![výsledky](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Umístěte kurzor někam v příkazu před klepnutím na tlačítko Hledat. Příkaz můžete rozdělit přes více než jeden řádek, ale neumisťujte prázdných řádků v příkazu. Prázdné řádky jsou pohodlný způsob, jak udržovat několik samostatné dotazy v okně.
>
>

Vybrat sloupce, přetáhněte je seskupit podle sloupce a filtrování:

![Klikněte na výběr sloupců v pravé horní části výsledků](./media/app-insights-analytics-tour/030.png)

Rozbalte jakoukoli položku si můžete zobrazit podrobnosti:

![Vyberte tabulky a použít konfigurace sloupce](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Klikněte na záhlaví sloupce změnit pořadí výsledky, které jsou k dispozici ve webovém prohlížeči. Ale mějte na paměti, že pro sadu výsledků velký počet řádků stažena do prohlížeče je omezený. Řazení tímto způsobem jednoduše Seřadí sadu vrácený výsledek a není vždy zobrazí skutečné položky ani nejvyšší ani nejnižší. Chcete-li seřadit položky spolehlivě, použijte `top` nebo `sort` operátor.
>
>

## <a name="query-across-applications"></a>Dotazování napříč aplikacemi
Pokud chcete kombinovat data z více aplikací služby Application Insights, použijte **aplikace** – klíčové slovo k určení aplikací společně s názvem tabulky.  Tento dotaz kombinuje požadavky z dvou různých aplikací **sjednocení** příkazu.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsiodocslanguage-referencetabular-operatorstop-operator-and-sorthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssort-operator"></a>[Horní](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) a [řazení](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
`take` je užitečné pro získání rychlé ukázky výsledek, ale zobrazuje řádky z tabulky bez určitého pořadí. Seřazený zobrazení získáte pomocí `top` (pro ukázku) nebo `sort` (přes celé tabulky).

Zobrazit prvních n řádků, seřazené podle konkrétního sloupce:

```AIQL

    requests | top 10 by timestamp desc
```

* *Syntaxe:* většina operátory mají parametry – klíčové slovo jako například `by`.
* `desc` = sestupné řazení `asc` = vzestupně.

![](./media/app-insights-analytics-tour/260.png)

`top...` je další způsob výkonné o tom, že `sort ... | take...`. Mohli jste napsali jsme:

```AIQL

    requests | sort by timestamp desc | take 10
```

Výsledkem bude stejná, ale bude o něco pomaleji spuštěná. (Můžete také napsat `order`, což je alias pro `sort`.)

Záhlaví sloupců v zobrazení tabulky je také možné seřadit výsledky na obrazovce. Ale samozřejmě platí, že pokud jste použili `take` nebo `top` k načtení jenom část tabulky, že kliknete na záhlaví sloupce bude pouze přeuspořádat jste načtených záznamů.

## <a name="wherehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorswhere-operator-filtering-on-a-condition"></a>[Kde](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator): filtrování na podmínce

Podívejme se, pouze požadavky, které vrátily kód konkrétní výsledku:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

`where` Operátor používá logický výraz. Tady jsou některé klíčové body je:

* `and`, `or`: Logické operátory
* `==`, `<>`, `!=` : a nejsou rovny.
* `=~`, `!~` : malá a velká písmena řetězec rovná a není rovno. Existuje mnoho další operátory porovnání řetězců.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Najít neúspěšných žádostí

Řetězcovou hodnotu převést na celé číslo použít větší-než porovnání:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Čas

Ve výchozím nastavení vaše dotazy jsou omezené na poslední 24 hodin. Ale můžete změnit tohoto rozsahu:

![](./media/app-insights-analytics-tour/change-time-range.png)

Přepsat časové rozmezí napsáním jakýkoli dotaz, který uvádí `timestamp` v klauzuli where. Příklad:

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

Funkce rozsah času je ekvivalentní vkládá každý zmínka o jednu z tabulek zdroje klauzuli 'where'.

`ago(3d)` znamená, že tři dny před. Jiné jednotky doby zahrnují hodin (`2h`, `2.5h`), minuty (`25m`) a sekund (`10s`).

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

    // Between specific day/time range
    requests
    | where timestamp > datetime(2018-05-17T17:06:19.892Z) and timestamp <= datetime(2018-05-18T17:06:19.892Z)
    | where duration > 0

```

[Data a časy odkaz](https://docs.loganalytics.io/docs/Language-Reference/Data-types/datetime).


## <a name="projecthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorsproject-operator-select-rename-and-compute-columns"></a>[Projekt](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator): vyberte, přejmenovat a compute sloupce
Použití [ `project` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator) vybrat pouze sloupce, které chcete:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Můžete také přejmenování sloupců a definovat nové značky:

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

* Názvy sloupců může obsahovat mezery nebo symboly, pokud jsou uváděn tímto způsobem: `['...']` nebo `["..."]`
* `%` je obvykle operátor modulo.
* `1d` (to je jedna číslice o měl ") je časový interval literálu znamená jeden den. Tady jsou některé další literály časový rozsah: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) zaokrouhlí dolů na nejbližší násobek základní hodnotu můžete zadat hodnotu. Takže `floor(aTime, 1s)` zaokrouhlí čas dolů nejbližší druhé.

Výrazy můžou obsahovat běžných operátorů (`+`, `-`,...), a celou řadu užitečných funkcí.

## <a name="extend"></a>Rozšíření
Pokud chcete přidat sloupce pro ty stávající, použijte [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Pomocí [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator) je míň podrobné než [ `project` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator) Pokud chcete zachovat všechny existující sloupce.

### <a name="convert-to-local-time"></a>Převést na místní čas

Časová razítka jsou vždy ve standardu UTC. Takže pokud jste na pobřeží Tichomoří USA a je zima, místního času je hodnotu-8 hodin od času UTC, může být tímto způsobem:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```

## <a name="summarizehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssummarize-operator-aggregate-groups-of-rows"></a>[Shrnutí](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator): agregovat skupiny řádků
`Summarize` použije zadanou *agregační funkce* přes skupiny řádků.

Například čas vaší webové aplikace trvá odpověď na žádost je uvedená v poli `duration`. Podívejme se, Průměrná doba odezvy pro všechny požadavky:

![](./media/app-insights-analytics-tour/410.png)

Nebo jsme může rozdělit výsledek do požadavků odlišné názvy:

![](./media/app-insights-analytics-tour/420.png)

`Summarize` shromažďuje datových bodů v datovém proudu do skupin, u kterého `by` klauzule vyhodnotí stejně. Každá hodnota v `by` – každá je název jedinečný operace v předchozím příkladu – výsledek řádek v tabulce výsledků.

Nebo nám může seskupení výsledků podle času dne:

![](./media/app-insights-analytics-tour/430.png)

Všimněte si, jak využíváme `bin` – funkce (neboli `floor`). Pokud jsme použili `by timestamp`, každý vstupní řádek skončili byste do vlastní skupiny malý. Pro všechny průběžné skalární jako dobu nebo čísla, máme přerušit průběžné rozsahu spravovat počet jednotlivých hodnot. `bin` – což je právě v známé zaokrouhlení seznamu `floor` function - je nejjednodušší způsob, jak to udělat.

Můžeme použít stejný postup k omezení rozsahů řetězců:

![](./media/app-insights-analytics-tour/440.png)

Všimněte si, že můžete použít `name=` nastavit název sloupec výsledků, buď ve výrazech agregace nebo podle klauzuli.

## <a name="counting-sampled-data"></a>Vzorků dat, počítací
`sum(itemCount)` je doporučené agregaci na počet událostí. V mnoha případech itemCount = 1, takže funkci prostě počítá počet řádků ve skupině. Ale když [vzorkování](app-insights-sampling.md) je v provozu, jenom zlomek původního událostí, které jsou zachovány jako datových bodů ve službě Application Insights, tak, že pro každý datový bod zobrazí jsou `itemCount` události.

Například pokud vzorkování zahodí 75 % původní události, pak vlastnost itemCount == 4 v uchovávané záznamy – to znamená, pro každý záznam uchovanou došlo k původní čtyři záznamy.

Adaptivní vzorkování způsobí, že vlastnost itemCount bude vyšší během období, kdy vaše aplikace je se nejčastěji používá.

Sečetl itemCount proto poskytuje dobré odhady původní počet událostí.

![](./media/app-insights-analytics-tour/510.png)

K dispozici je také `count()` agregace (a počet operace), pro případy, kdy Opravdu chcete spočítat počet řádků ve skupině.

Je rozsah [agregační funkce](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions).

## <a name="charting-the-results"></a>Vytvoření grafu výsledků
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Ve výchozím nastavení zobrazí výsledky jako tabulka:

![](./media/app-insights-analytics-tour/225.png)

Můžeme udělat lépe než zobrazení tabulky. Podívejme se na výsledky v zobrazení diagramu s svislé panelu možnost:

![Klikněte na graf, pak zvolte svislé pruhový graf a přiřadit x a y osy](./media/app-insights-analytics-tour/230.png)

Všimněte si, že i když jsme neměli seřadit výsledky podle času (jak je vidět v zobrazení tabulky), zobrazení grafu vždy zobrazovat data a času ve správném pořadí.


## <a name="timecharts"></a>Timecharts
Zobrazit, že počet událostí se každou hodinu:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Vyberte možnost zobrazení grafu:

![Časový graf](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Více řad
Více výrazů v `summarize` klauzule vytvoří více sloupců.

Více výrazů v `by` klauzule vytváří více řádků, jeden pro každou kombinaci hodnot.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tabulka požadavků podle hodin a umístění](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Graf segmentovat podle dimenzí
Pokud jste tabulku, která se má sloupec řetězcového typu a číselný sloupec grafu, řetězec umožňuje rozdělit na samostatné řady bodů číselná data. Pokud existuje více než jeden sloupec řetězec, můžete zvolit sloupec chcete použít jako diskriminátor.

![Rozdělit graf analýzy](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Odrážení frekvence

Převeďte na řetězec použít jako diskriminátor, logická hodnota:

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

### <a name="display-multiple-metrics"></a>Zobrazit několik metrik
Pokud graf tabulku, která má více než jeden číselný sloupec, kromě časového razítka, můžete zobrazit libovolnou kombinaci.

![Rozdělit graf analýzy](./media/app-insights-analytics-tour/110.png)

Musíte vybrat **není rozdělení** mohli vybrat více číselné sloupce. Nelze rozdělit podle sloupce řetězec ve stejnou dobu jako zobrazení více než jeden číselný sloupec.

## <a name="daily-average-cycle"></a>Denní průměrný cyklu
Jak se liší využití přes průměrného dne?

Počet požadavků podle času modulo jeden den, rozdělený na intervaly na hodiny:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Spojnicový graf hodin průměrného dne](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Všimněte si, že máme aktuálně převést dob trvání na data a času mohla zobrazit na spojnicovém grafu.
>
>

## <a name="compare-multiple-daily-series"></a>Porovnat více denní řad
Jak využití se liší v čase den v různých zemích?

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

![Rozdělit podle client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Vykreslení distribuci
Kolik relací existují různých délek?

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

Poslední řádek je potřeba převést na typ datetime. Aktuálně osy x grafu se zobrazuje jako skalární, pouze pokud je datum a čas.

`where` Klauzule vyloučí konečný relace (sessionDuration == 0) a nastaví délku osy x.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsiodocslanguage-referenceaggregation-functionspercentiles"></a>[Percentil](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/percentiles())
Jaké oblasti dob trvání zahrnují různá procenta relace?

Pomocí výše uvedeného dotazu, ale nahraďte poslední řádek:

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

Rovněž jsme odebrali horního omezení v where – klauzule zajistí správné údaje včetně všechny relace s více než jeden požadavek na:

![výsledek](./media/app-insights-analytics-tour/180.png)

Odkud vidíme, že:

* 5 % relací trvají méně než tři minuty 34s;
* 50 % relací naposledy kratší než 36 minut;
* posledních 5 % relace delší než 7 dní

Získat samostatné rozdělení pro jednotlivé země, jsme jenom nutné, aby uvedl shrnout sloupci client_CountryOrRegion samostatně prostřednictvím oba operátory:

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
Máme přístup k několika tabulkám, včetně požadavků a výjimek.

Abyste mohli najít výjimky související s žádostí, který vrátí odpověď na selhání, jsme spojení tabulek na `operation_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


Je vhodné použít `project` vyberte jen sloupce, potřebujeme před provedením spojení.
V klauzulích stejné je přejmenovat sloupec časového razítka.

## <a name="lethttpsdocsloganalyticsiodocslanguage-referencequery-statementslet-statement-assign-a-result-to-a-variable"></a>[Umožní](https://docs.loganalytics.io/docs/Language-Reference/Query-statements/Let-statement): výsledek přiřaďte proměnné

Použití `let` k tomu oddělit součásti předchozím výrazem. Výsledky jsou beze změny:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> V klientovi Analytics neumisťujte prázdné řádky mezi částmi dotazu. Ujistěte se, že všechny jeho spuštění.
>

Použití `toscalar` pro převod jedné tabulky buňku na hodnotu:

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


### <a name="functions"></a>Functions

Použití *nechat* definice funkce:

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Přistupující objekty
Vnořené objekty lze snadno přistupovat. Například v datovém proudu výjimky můžete zobrazit strukturovaných objektů takto:

![výsledek](./media/app-insights-analytics-tour/520.png)

Můžete ji sloučit výběrem vlastnosti, které vás zajímají:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Všimněte si, že je třeba přetypovat výsledek, který má odpovídajícího typu.


## <a name="custom-properties-and-measurements"></a>Vlastní vlastnosti a měření
Pokud vaše aplikace připojuje [vlastní rozměry (Vlastnosti) a vlastní měření](app-insights-api-custom-events-metrics.md#properties) na události, pak se zobrazí v `customDimensions` a `customMeasurements` objekty.

Například pokud vaše aplikace obsahuje:

```csharp

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2.d2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

K extrakci těchto hodnot v Analytics:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type
```

Pokud chcete ověřit, zda vlastní dimenze je určitého typu:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

### <a name="special-characters"></a>Speciální znaky

Pro vyhrazení identifikátorů začínajících speciální znaky nebo klíčová slova jazyka v jejich názvy, budete potřebovat pro přístup k nim prostřednictvím `['` a `']` nebo pomocí `["` a `"]`.

```AIQL

    customEvents
    | extend p2d2 = customDimensions.['p2.d2'], ...
```

[Odkazovat na identifikátor pravidla pojmenování](https://docs.loganalytics.io/docs/Learn/References/Naming-principles)

## <a name="dashboards"></a>Řídicí panely
Vaše výsledky na řídicí panel můžete připnout k dejte dohromady všechna vaše nejdůležitější grafů a tabulek.

* [Sdílený řídicí panel Azure](app-insights-dashboards.md#share-dashboards): Kliknutím na ikonu připínáčku. Než to uděláte, musíte mít sdíleného řídicího panelu. Na webu Azure Portal otevřete nebo vytvořte řídicí panel a klikněte na sdílenou složku.
* [Řídicí panel Power BI](app-insights-export-power-bi.md): klikněte na exportovat, Power BI dotazu. Výhodou této alternativou je, že můžete zobrazit dotaz společně s další výsledky z celé řady zdrojů.

## <a name="combine-with-imported-data"></a>Kombinovat s importovanými daty

Analytické sestavy vypadat správně v řídicím panelu, ale někdy chcete přeložit data do více stravitelné formuláře. Předpokládejme například, že ověřeným uživatelům v telemetrii označené jako alias. Chcete zobrazit jejich skutečné názvy ve výsledcích. K tomuto účelu, budete potřebovat soubor CSV, který se mapuje aliasů skutečné názvy.

Můžete importovat soubor dat a používat ho stejným způsobem jako kterékoli z tabulek standardní (požadavky, výjimky a tak dále). Dotazování sama o sobě nebo spojení s jinými tabulkami. Například, pokud máte tabulku s názvem usermap a má sloupce `realName` a `userId`, můžete ho použít pro převod `user_AuthenticatedId` pole v požadavku telemetrická data:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Chcete-li importovat tabulku, v okně schématu v části **jiných zdrojů dat**, postupujte podle pokynů a přidejte nový zdroj dat tak, že nahrajete ukázková data. Potom můžete tuto definici k nahrání tabulky.

Funkce importu je aktuálně ve verzi preview, proto se zpočátku zobrazí odkaz "Kontaktujte nás" v části "Zdrojů dat." Tato možnost slouží k registraci do programu preview a odkaz se nahradí klikněte na tlačítko "Přidat nový zdroj dat".


## <a name="tables"></a>Tabulky
Datový proud telemetrických dat přijatých z vaší aplikace je přístupná prostřednictvím několika tabulek. Schéma vlastnosti, které jsou k dispozici pro každou tabulku je viditelný v levé části okna.

### <a name="requests-table"></a>Tabulka požadavků
Požadavky HTTP počet segmentů podle názvu stránky a webové aplikace:

![Počet požadavků segmentované podle názvu](./media/app-insights-analytics-tour/analytics-count-requests.png)

Požadované žádosti, které nesplní nejvíce:

![Počet požadavků segmentované podle názvu](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Vlastní události tabulky
Pokud používáte [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) k odesílání vlastních událostí, najdete je v této tabulce.

Vezměme si příklad kde obsahuje tyto řádky kódu vaší aplikace:

```csharp

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Zobrazí frekvenci těchto událostí:

![Zobrazit počet vlastních událostí](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Extrahování měření a dimenzí z události:

![Zobrazit počet vlastních událostí](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Vlastní metriky tabulky
Pokud používáte [metody TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) odeslat vlastní hodnoty metrik, jeho výsledky v zjistíte **customMetrics** datového proudu. Příklad:  

![Vlastní metriky v Application Insights analytics](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> V [Průzkumníka metrik](app-insights-metrics-explorer.md), všechny vlastní měření připojit k libovolnému typu telemetrická data pohromadě v okně s metrikami společně s metrikami odeslaných pomocí `TrackMetric()`. Ale v Analytics, vlastní měření stále připojen k libovolným typ telemetrie, které byly provedeny na - události nebo žádosti o atd. – když metrika odesílaných TrackMetric se zobrazí ve vlastní službě stream.
>
>

### <a name="performance-counters-table"></a>Tabulka čítače výkonu
[Čítače výkonu](app-insights-performance-counters.md) zobrazit základní systémové metriky pro vaši aplikaci, jako je například procesoru, paměti a využití sítě. Můžete nakonfigurovat sadu SDK k odesílání další čítače, včetně vlastní čítače.

**Čítače výkonu** zpřístupňuje schématu `category`, `counter` název, a `instance` název jednotlivých čítačů výkonu. Názvy instancí čítačů platí jenom pro některé čítače výkonu a obvykle signalizují název procesu, ke kterému se vztahuje počet. V telemetrii pro každou aplikaci zobrazí se vám jenom čítače pro tuto aplikaci. Například pokud chcete zobrazit jsou k dispozici co čítače:

![Čítače výkonu ve službě Application Insights analytics](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Pokud chcete získat grafu dostupnou paměť ve vybraném období:

![Paměť promítnu ve službě Application Insights analytics](./media/app-insights-analytics-tour/analytics-available-memory.png)

Další telemetrická data, jako jsou **čítače výkonu** má také sloupec `cloud_RoleInstance` , která určuje identitu hostitelský počítač, na kterém je spuštěná aplikace. Chcete-li například porovnat výkon vaší aplikace na různých počítačích:

![Výkon segmentované podle instance role ve službě Application Insights analytics](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Výjimky tabulky
[Výjimky hlášených aplikace](app-insights-asp-net-exceptions.md) jsou k dispozici v této tabulce.

K vyhledání požadavku HTTP, který vaše aplikace byla zpracování při byla výjimka vyvolána, připojte se k operation_Id:

![Připojte se k výjimky s žádostmi o operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Tabulka časování prohlížeče
`browserTimings` zobrazuje stránku zatížení data shromážděná v prohlížečích vašich uživatelů.

[Nastavit aplikaci pro telemetrii na straně klienta](app-insights-javascript.md) Chcete-li zobrazit tyto metriky.

Schéma obsahuje [metrika označující délky různých fázích načítání stránky](app-insights-javascript.md#page-load-performance). (Není označují dobu, kterou vaši uživatelé přečíst na stránce.)  

Zobrazit popularities různé stránky a načtení časy pro každou stránku:

![Časy načtení stránek v Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>Dostupnost výsledků tabulky
`availabilityResults` Zobrazuje výsledky vašich [webové testy](app-insights-monitor-web-app-availability.md). Každé spuštění testů z umístění každého testu se použije v hlášení samostatně.

![Časy načtení stránek v Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Tabulka závislosti
Obsahuje výsledky z volání, že vaše aplikace odešle databází a rozhraní REST API a další volání TrackDependency(). Zahrnuje také volání AJAX provedená z prohlížeče.

Volání AJAX v prohlížeči:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Volání závislostí ze serveru:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Vždy zobrazit výsledky závislostí na straně serveru `success==False` Pokud není nainstalován Agent nástroje Application Insights. Další data, ale jsou správné.

### <a name="traces-table"></a>Tabulka trasování
Obsahuje telemetrická data odesílaná aplikací pro použití metody TrackTrace(), nebo [jiných rozhraní protokolování](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Video 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Pokročilé dotazy:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>Další postup
* [Referenční informace k jazyku Analytics](app-insights-analytics-reference.md)
* [SQL-uživatelů tahák](https://aka.ms/sql-analytics) přeloží nejběžnější idiomy.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
