---
title: Application Insights API pro vlastní události a metriky | Microsoft Docs
description: Po zadání několika řádků kódu vložte do vaší aplikace nebo plochy zařízení, webové stránky nebo služby, sledovat využití a diagnostikovat problémy.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: e93b3348c933f65067114bfce4ac517f1204af34
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights API pro vlastní události a metriky

Vložte po zadání několika řádků kódu v aplikaci a zjistěte, co uživatelé dělají s ním nebo pro usnadnění diagnostiky problémů. Odesílat telemetrická data z aplikace zařízení a vzdálené ploše, webovými klienty a webové servery. Použití [Azure Application Insights](app-insights-overview.md) základní rozhraní API telemetrie k odeslání vlastní události a metriky a vlastní verzích standardní telemetrie. Toto rozhraní API je stejné rozhraní API, které použít standardní sběrače dat Application Insights.

## <a name="api-summary"></a>Souhrn rozhraní API
Rozhraní API je uniform pro všechny platformy kromě několik malé rozdíly.

| Metoda | Použít pro |
| --- | --- |
| [`TrackPageView`](#page-views) |Stránky, obrazovky, okna nebo formuláře. |
| [`TrackEvent`](#trackevent) |Akce uživatelů a dalších událostí. Používá ke sledování chování uživatele nebo k monitorování výkonu. |
| [`TrackMetric`](#trackmetric) |Měření výkonu, jako je například délky front, které nesouvisí s konkrétní události. |
| [`TrackException`](#trackexception) |Protokolování výjimky pro diagnostiku. Sledování, kde k nim dojde ve vztahu k jiné událostí a zkontrolujte trasování zásobníku. |
| [`TrackRequest`](#trackrequest) |Protokolování četnost a dobu trvání požadavky serveru pro analýzu výkonu. |
| [`TrackTrace`](#tracktrace) |Zprávy protokolů diagnostiky. Také můžete zaznamenat protokoly třetích stran. |
| [`TrackDependency`](#trackdependency) |Protokolování doba trvání a četnost volání na externí komponenty, které závisí aplikace na. |

Můžete [připojení vlastnosti a metriky](#properties) pro většinu těchto volání telemetrie.

## <a name="prep"></a>Než začnete
Pokud nemáte k dispozici odkaz na Application Insights SDK ještě:

* Přidejte Application Insights SDK do projektu:

  * [Projekt ASP.NET](app-insights-asp-net.md)
  * [Projektu Java](app-insights-java-get-started.md)
  * [Projekt Node.js](app-insights-nodejs.md)
  * [JavaScript v každé webové stránky](app-insights-javascript.md) 
* V zařízení nebo webový server kódu patří:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`
    
    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Získat TelemetryClient instance
Získat instanci `TelemetryClient` (s výjimkou v jazyce JavaScript webové stránky):

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();
    
*Node.js*

    var telemetry = applicationInsights.defaultClient;


TelemetryClient je bezpečné pro přístup z více vláken.

Pro projekty ASP.NET a Java jsou automaticky zachytit příchozí požadavky HTTP. Můžete chtít vytvořit další instance TelemetryClient pro ostatní moduly vaší aplikace. Například můžete mít jednu instanci TelemetryClient v třídě middleware na sestavu obchodní logiky události. Můžete nastavit vlastnosti, například ID uživatele a DeviceId identifikovat počítač. Tyto informace je připojený k všechny události, které odesílá instance. 

*C#*

    TelemetryClient.Context.User.Id = "...";
    TelemetryClient.Context.Device.Id = "...";

*Java*

    telemetry.getContext().getUser().setId("...);
    telemetry.getContext().getDevice().setId("...");

V projektech, Node.js, můžete použít `new applicationInsights.TelemetryClient(instrumentationKey?)` vytvořit novou instanci, ale to se doporučuje jenom pro scénáře, které vyžadují izolované konfiguraci z singleton `defaultClient`.

## <a name="trackevent"></a>TrackEvent
Ve službě Application Insights *vlastní události* je datový bod, který můžete zobrazit v [Průzkumníku metrik](app-insights-metrics-explorer.md) jako agregovaného počtu a v [diagnostické vyhledávání](app-insights-diagnostic-search.md) jako jednotlivé události. (Není souvisejících s MVC nebo jiných framework "události.")

Vložit `TrackEvent` volá ve vašem kódu počítat různé události. Jak často uživatelé vybrat konkrétní funkce, jak často budou dosáhnout určité cíle nebo možná četnosti provádění konkrétní typy chyb.

Herní aplikace, například odeslání události vždy, když uživatel wins hra:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");
    
*Node.js*

    telemetry.trackEvent({name: "WinGame"});

### <a name="view-your-events-in-the-microsoft-azure-portal"></a>Zobrazit události na portálu Microsoft Azure
Chcete-li zobrazit počet událostí, otevřete [Průzkumníku metrik](app-insights-metrics-explorer.md) okně přidejte nový graf a vyberte **události**.  

![Zobrazí počet vlastní události](./media/app-insights-api-custom-events-metrics/01-custom.png)

Pro porovnání počty různé události, nastavte typ grafu **mřížky**a skupinu podle názvu událostí:

![Nastavte typ grafu a seskupení](./media/app-insights-api-custom-events-metrics/07-grid.png)

V mřížce klikněte na tlačítko prostřednictvím název události zobrazíte jednotlivé výskyty této události. Chcete-li zobrazit více podrobností – klikněte na kterýkoli z výskytů v seznamu.

![Procházení událostí](./media/app-insights-api-custom-events-metrics/03-instances.png)

Umožňuje zaměřit se na konkrétní události ve vyhledávání nebo Průzkumníku metrik, nastavte v okně filtru na názvy událostí, které vás zajímají:

![Otevřete filtry, rozbalte název události a vyberte jednu nebo více hodnot](./media/app-insights-api-custom-events-metrics/06-filter.png)

### <a name="custom-events-in-analytics"></a>Vlastní události v Analytics

Je k dispozici v telemetrii `customEvents` tabulky v [Application Insights Analytics](app-insights-analytics.md). Každý řádek představuje volání `trackEvent(..)` ve vaší aplikaci. 

Pokud [vzorkování](app-insights-sampling.md) je v provozu, vlastnost itemCount zobrazuje hodnotu větší než 1. Pro příklad itemCount == 10 znamená, že 10 volání trackEvent(), proces vzorkování přenášena pouze jeden z nich. Chcete-li získat správný počet vlastních událostí, použijte proto použít kód jako `customEvent | summarize sum(itemCount)`.


## <a name="trackmetric"></a>TrackMetric

Application Insights můžete grafu metriky, které nejsou připojeny ke konkrétní události. Délka fronty může například sledovat v pravidelných intervalech. O metriky jednotlivými měřeními jsou méně důležité než variace a trendy a proto statistické grafy jsou užitečné.

Aby bylo možné odesílat metriky do služby Application Insights, můžete použít `TrackMetric(..)` rozhraní API. Existují dva způsoby, jak odeslat metriky: 

* Jednu hodnotu. Pokaždé, když provedete měření ve vaší aplikaci, odešlete s odpovídající hodnotou Application Insights. Předpokládejme například, že máte metriky popisující počet položek v kontejneru. V konkrétním časovém období můžete poprvé tři položky do kontejneru a potom odeberte dvě položky. Podle toho by volání `TrackMetric` dvakrát: první předání hodnota `3` a potom hodnotu `-2`. Application Insights ukládá obě hodnoty vaším jménem. 

* Agregace. Při práci s metriky, je každé jedno měření zájmu zřídka. Místo toho je důležité souhrn co se stalo v konkrétním časovém období. Takové souhrn nazývá _agregace_. V předchozím příkladu agregační metriky součet za toto období je `1` a počet hodnot metriky je `2`. Pokud použijete způsob agregace, pouze vyvolání `TrackMetric` jednou za časové období a odesílat agregované hodnoty. Toto je doporučený postup, vzhledem k tomu může významně snížit náklady a výkon režie odesláním méně datových bodů do služby Application Insights a stále shromažďovat všechny relevantní informace.

### <a name="examples"></a>Příklady:

#### <a name="single-values"></a>Jednotlivé hodnoty

Odeslání jednoho metriky hodnoty:

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

*Java*

```Java
    
    telemetry.trackMetric("queueLength", 42.0);

```

*Node.js*

 ```Javascript
     telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

#### <a name="aggregating-metrics"></a>Agregování metriky

Doporučuje se agregovaná metrika před jejich odesláním z vaší aplikace, ke snížení šířky pásma, náklady a ke zlepšení výkonu.
Tady je příklad totožný kódu:

*C#*

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

namespace MetricAggregationExample
{
    /// <summary>
    /// Aggregates metric values for a single time period.
    /// </summary>
    internal class MetricAggregator
    {
        private SpinLock _trackLock = new SpinLock();

        public DateTimeOffset StartTimestamp    { get; }
        public int Count                        { get; private set; }
        public double Sum                       { get; private set; }
        public double SumOfSquares              { get; private set; }
        public double Min                       { get; private set; }
        public double Max                       { get; private set; }
        public double Average                   { get { return (Count == 0) ? 0 : (Sum / Count); } }
        public double Variance                  { get { return (Count == 0) ? 0 : (SumOfSquares / Count)
                                                                                  - (Average * Average); } }
        public double StandardDeviation         { get { return Math.Sqrt(Variance); } }

        public MetricAggregator(DateTimeOffset startTimestamp)
        {
            this.StartTimestamp = startTimestamp;
        }

        public void TrackValue(double value)
        {
            bool lockAcquired = false;

            try
            {
                _trackLock.Enter(ref lockAcquired);

                if ((Count == 0) || (value < Min))  { Min = value; }
                if ((Count == 0) || (value > Max))  { Max = value; }
                Count++;
                Sum += value;
                SumOfSquares += value * value;
            }
            finally
            {
                if (lockAcquired)
                {
                    _trackLock.Exit();
                }
            }
        }
    }   // internal class MetricAggregator

    /// <summary>
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    /// </summary>
    public sealed class Metric : IDisposable
    {
        private static readonly TimeSpan AggregationPeriod = TimeSpan.FromSeconds(60);

        private bool _isDisposed = false;
        private MetricAggregator _aggregator = null;
        private readonly TelemetryClient _telemetryClient;

        public string Name { get; }

        public Metric(string name, TelemetryClient telemetryClient)
        {
            this.Name = name ?? "null";
            this._aggregator = new MetricAggregator(DateTimeOffset.UtcNow);
            this._telemetryClient = telemetryClient ?? throw new ArgumentNullException(nameof(telemetryClient));

            Task.Run(this.AggregatorLoopAsync);
        }

        public void TrackValue(double value)
        {
            MetricAggregator currAggregator = _aggregator;
            if (currAggregator != null)
            {
                currAggregator.TrackValue(value);
            }
        }

        private async Task AggregatorLoopAsync()
        {
            while (_isDisposed == false)
            {
                try
                {
                    // Wait for end end of the aggregation period:
                    await Task.Delay(AggregationPeriod).ConfigureAwait(continueOnCapturedContext: false);

                    // Atomically snap the current aggregation:
                    MetricAggregator nextAggregator = new MetricAggregator(DateTimeOffset.UtcNow);
                    MetricAggregator prevAggregator = Interlocked.Exchange(ref _aggregator, nextAggregator);

                    // Only send anything is at least one value was measured:
                    if (prevAggregator != null && prevAggregator.Count > 0)
                    {
                        // Compute the actual aggregation period length:
                        TimeSpan aggPeriod = nextAggregator.StartTimestamp - prevAggregator.StartTimestamp;
                        if (aggPeriod.TotalMilliseconds < 1)
                        {
                            aggPeriod = TimeSpan.FromMilliseconds(1);
                        }

                        // Construct the metric telemetry item and send:
                        var aggregatedMetricTelemetry = new MetricTelemetry(
                                Name,
                                prevAggregator.Count,
                                prevAggregator.Sum,
                                prevAggregator.Min,
                                prevAggregator.Max,
                                prevAggregator.StandardDeviation);
                        aggregatedMetricTelemetry.Properties["AggregationPeriod"] = aggPeriod.ToString("c");

                        _telemetryClient.Track(aggregatedMetricTelemetry);
                    }
                }
                catch(Exception ex)
                {
                    // log ex as appropriate for your application
                }
            }
        }

        void IDisposable.Dispose()
        {
            _isDisposed = true;
            _aggregator = null;
        }
    }   // public sealed class Metric
}
```

### <a name="custom-metrics-in-metrics-explorer"></a>Vlastní metriky v Průzkumníku metrik

Pokud chcete zobrazit výsledky, otevřete Průzkumníka metrik a přidejte nový graf. Upravte v grafu zobrazí vaše metriku.

> [!NOTE]
> Vlastní metriku může trvat několik minut, než se objeví v seznamu dostupné metriky.
>

![Přidejte nový graf nebo vyberte graf a v části vlastní, vyberte vaše metrika](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Vlastní metriky v Analytics

Je k dispozici v telemetrii `customMetrics` tabulky v [Application Insights Analytics](app-insights-analytics.md). Každý řádek představuje volání `trackMetric(..)` ve vaší aplikaci.
* `valueSum` -Toto je součet měření. Chcete-li získat střední hodnoty, vydělte `valueCount`.
* `valueCount` -Počet měření, které byly agregovat do této `trackMetric(..)` volání.

## <a name="page-views"></a>Zobrazení stránek
V aplikaci pomocí zařízení nebo webové stránky je odeslána telemetrická zobrazení stránky ve výchozím nastavení při načtení každé obrazovky nebo stránky. Ale můžete změnit, sledovat zobrazení stránky v další nebo různých časech. Například v aplikaci, která zobrazí karty nebo okna, můžete sledovat na stránce vždy, když uživatel otevře nové okno.

![Použití přehledu v okně Přehled](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Data uživatele a relace se odešle jako vlastnosti společně s zobrazení stránky, uživatele a relace grafy pocházet zachování připojení při telemetrická zobrazení stránky.

### <a name="custom-page-views"></a>Zobrazení vlastních stránek
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Java*

    telemetry.trackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


Pokud máte několik karet v rámci jiné stránky HTML, můžete zadat adresu URL příliš:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>Zobrazení stránky časování
Ve výchozím nastavení, časy hlášené jako **zobrazení času načítání stránky** se měří od, když prohlížeč odesílá požadavek, dokud se nazývá události načtení stránky v prohlížeči.

Místo toho můžete buď:

* Nastavit explicitní doba trvání [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) volání: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Použití zobrazení stránky časování volání `startTrackPage` a `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

Název, který použijete jako první parametr přidruží volání zahájení a ukončení. Výchozí název aktuální stránky.

Výsledný doby zatížení stránky zobrazí v Průzkumníku metrik jsou odvozeny od interval mezi volání zahájení a ukončení. Je to na můžete jaké interval, ve skutečnosti čas.

### <a name="page-telemetry-in-analytics"></a>Stránka telemetrie Analytics

V [Analytics](app-insights-analytics.md) dvou tabulek zobrazit data z prohlížeče operace:

* `pageViews` Tabulka obsahuje data o název adresy URL a stránky
* `browserTimings` Tabulka obsahuje data o výkonu klienta, jako je například čas potřebný ke zpracování příchozích dat.

Vyhledání, jak dlouho trvá prohlížeče pro zpracování různých stránkách:

```
browserTimings | summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name 
```

Pro zjištění popularities různých prohlížečů:

```
pageViews | summarize count() by client_Browser
```

Chcete-li přidružit zobrazení stránky k volání AJAX, spojení s závislosti:

```
pageViews | join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest
Server SDK používá TrackRequest do protokolu HTTP žádosti.

Můžete také volat ho sami Pokud chcete simulovat požadavků v kontextu, kde nemáte modulu služby web spuštěn.

Doporučeným způsobem, jak odesílat telemetrická data požadavku je ale, kde žádost funguje jako <a href="#operation-context">operační kontext</a>.

## <a name="operation-context"></a>Operace kontextu
Mohou korelovat telemetrii položky společně podle jejich přidružení k operaci kontextu. Standardní modulu Sledování žádostí o tomu pro výjimky a dalších událostí, které se odesílají během zpracování požadavku HTTP. V [vyhledávání](app-insights-diagnostic-search.md) a [Analytics](app-insights-analytics.md), budete moci snadno najít všechny události přidružené k žádosti pomocí jeho operace ID.

V tématu [korelace Telemetrii ve službě Application Insights](application-insights-correlation.md) další podrobnosti o korelace.

Při sledování telemetrie ručně, nejjednodušší způsob, jak zajistit telemetrie korelace pomocí tohoto vzoru:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...
    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Společně s nastavení kontextu operace `StartOperation` vytvoří položku telemetrie typu, který určíte. Odešle položce telemetrie při vyřazení operaci, nebo pokud explicitně volání `StopOperation`. Pokud používáte `RequestTelemetry` jako typ telemetrická data, jeho trvání nastavena na se časový interval mezi zahájení a ukončení.

Telemetrie položky hlášené v rámci oboru operace stát podřízené tyto operace. Kontexty operace může být vnořena. 

Do pole hledání kontext operace se používá k vytvoření **související položky** seznamu:

![Související položky](./media/app-insights-api-custom-events-metrics/21.png)

V tématu [sledovat vlastní operace s Application Insights .NET SDK](application-insights-custom-operations-tracking.md) Další informace o sledování vlastní operace.

### <a name="requests-in-analytics"></a>Požadavky v Analytics 

V [Application Insights Analytics](app-insights-analytics.md), požadavky zobrazit nahoru v `requests` tabulky.

Pokud [vzorkování](app-insights-sampling.md) je v operaci vlastnost itemCount zobrazí hodnotu větší než 1. Pro příklad itemCount == 10 znamená, že 10 volání trackRequest(), proces vzorkování přenášena pouze jeden z nich. Správný počet požadavků a průměrné trvání segmentované podle požadavku názvy získáte pomocí kódu, jako:

```AIQL
requests | summarize count = sum(itemCount), avgduration = avg(duration) by name
```


## <a name="trackexception"></a>TrackException
Odesílání výjimky Application Insights:

* K [jejich počet](app-insights-metrics-explorer.md), jako údaje o četnosti problému.
* K [Zkontrolujte jednotlivé výskyty](app-insights-diagnostic-search.md).

Sestavy obsahují trasování zásobníku.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*Java*

    try {
        ...
    } catch (Exception ex) {
        telemetry.trackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }
    
*Node.js*

    try
    {
       ...
    }
    catch (ex)
    {
       telemetry.trackException({exception: ex});
    }

Sady SDK catch množství výjimek automaticky, takže nemáte vždy volat TrackException explicitně.

* ASP.NET: [napsat kód pro zachycení výjimky](app-insights-asp-net-exceptions.md).
* J2EE: [výjimky jsou zachyceny automaticky](app-insights-java-get-started.md#exceptions-and-request-failures).
* JavaScript: Výjimky jsou zachyceny automaticky. Pokud chcete zakázat automatické shromažďování, přidejte řádek fragment kódu, který vložte do své webové stránky:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

### <a name="exceptions-in-analytics"></a>Výjimky v Analytics

V [Application Insights Analytics](app-insights-analytics.md), výjimky objeví v `exceptions` tabulky.

Pokud [vzorkování](app-insights-sampling.md) je v provozu, `itemCount` vlastnost zobrazuje hodnotu větší než 1. Pro příklad itemCount == 10 znamená, že 10 volání pro trackException() proces vzorkování přenášena pouze jeden z nich. Chcete-li získat správný počet výjimek segmentované podle typu výjimky, použijte kód, jako:

```
exceptions | summarize sum(itemCount) by type
```

Většinu informací důležité zásobníku je již extrahován do samostatné proměnné, ale můžete vyžádat od sebe `details` struktura získat další. Vzhledem k tomu, že tato struktura je dynamický, by měl přetypovat na typ očekávaný výsledek. Příklad:

```AIQL
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Chcete-li výjimky přidružit jejich související požadavky, použijte spojení:

```
exceptions
| join (requests) on operation_Id 
```

## <a name="tracktrace"></a>TrackTrace
Použijte TrackTrace k diagnostice potíží odesláním "záznam s popisem cesty" Application Insights. Můžete odesílat bloky diagnostických dat a je v zkontrolovat [diagnostické vyhledávání](app-insights-diagnostic-search.md).

V rozhraní .NET [protokolu adaptéry](app-insights-asp-net-trace-logs.md) používat toto rozhraní API k odesílání jiných výrobců protokoly na portál.

V jazyce Java pro [standardní protokolovacích nástrojů, jako Log4J, Logback](app-insights-java-trace-logs.md) používat k odesílání jiných výrobců protokoly na portál Application Insights Log4j nebo Logback Appenders.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

*Java*

    telemetry.trackTrace(message, SeverityLevel.Warning, properties);
    
*Node.js*

    telemetry.trackTrace({message: message, severity:applicationInsights.Contracts.SeverityLevel.Warning, properties:properties});


Můžete hledat na obsah zprávy, ale (na rozdíl od hodnoty vlastností) nelze filtrovat na něm.

Limit velikosti na `message` mnohem vyšší, než je limit na vlastnosti.
Výhodou TrackTrace je, že můžete ukládat poměrně dlouho data ve zprávě. Můžete například kódovat následných dat existuje.  

Kromě toho můžete přidat úroveň závažnosti na zprávu. A, podobně jako ostatní telemetrických dat, můžete přidat hodnoty vlastností, které vám pomohou filtru nebo vyhledávání pro různé skupiny trasování. Příklad:

*C#*

```C#
    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```Java

    Map<String, Integer> properties = new HashMap<>();
    properties.put("Database", db.ID);
    telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);

```

V [vyhledávání](app-insights-diagnostic-search.md), můžete pak snadno odfiltrovat všechny zprávy konkrétní závažnosti úrovně, které se vztahují ke konkrétní databázi.


### <a name="traces-in-analytics"></a>Trasování v Analytics

V [Application Insights Analytics](app-insights-analytics.md), volání TrackTrace zobrazí v `traces` tabulky.

Pokud [vzorkování](app-insights-sampling.md) je v provozu, vlastnost itemCount zobrazuje hodnotu větší než 1. Příklad itemCount == 10 znamená, že 10 volání `trackTrace()`, proces vzorkování přenášena pouze jeden z nich. Pokud chcete získat správný počet volání trasování, měli byste použít proto kódu, jako `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency
Volání TrackDependency použijte ke sledování doby odezvy a úspěšnost volání externí část kódu. Výsledky se zobrazí v grafech závislost na portálu.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```Java
    boolean success = false;
    long startTime = System.currentTimeMillis();
    try {
        success = dependency.call();
    }
    finally {
        long endTime = System.currentTimeMillis();
        long delta = endTime - startTime;
        RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
        telemetry.setTimeStamp(startTime);
        telemetry.trackDependency(dependencyTelemetry);
    }

```

*JavaScript*

```Javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({dependencyTypeName: "myDependency", name: "myCall", duration: elapsed, success:success});
}
```

Mějte na paměti, že zahrnují sady SDK serveru [závislostí modulu](app-insights-asp-net-dependencies.md) , zjišťuje a sleduje určitých volání závislosti automaticky – například k databázím a rozhraní REST API. Je třeba nainstalovat agenta na vašem serveru, aby se modul fungovat. 

V jazyce Java, určitá závislost volání lze automaticky sledovat pomocí [agenta Java](app-insights-java-agent.md).

Toto volání použijte, pokud chcete sledovat volání, které není catch automatizované sledování, nebo pokud nechcete, aby pro instalaci agenta.

Chcete-li vypnout standardní modul sledování závislostí v jazyce C#, upravit [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) a odstraňte odkaz na `DependencyCollector.DependencyTrackingTelemetryModule`. V jazyce Java prosím nainstalujte agenta java Pokud nechcete automaticky shromažďovat standardní závislosti.

### <a name="dependencies-in-analytics"></a>Závislosti v Analytics

V [Application Insights Analytics](app-insights-analytics.md), trackDependency volá zobrazit `dependencies` tabulky.

Pokud [vzorkování](app-insights-sampling.md) je v provozu, vlastnost itemCount zobrazuje hodnotu větší než 1. Pro příklad itemCount == 10 znamená, že 10 volání trackDependency(), proces vzorkování přenášena pouze jeden z nich. Získat správný počet závislostí segmentované podle cílové součásti, například použijte kód:

```
dependencies | summarize sum(itemCount) by target
```

Chcete-li přidružit své žádosti související závislosti, použijte spojení:

```
dependencies
| join (requests) on operation_Id 
```

## <a name="flushing-data"></a>Probíhá vyprazdňování dat
Za normálních okolností sady SDK odešle data v některých případech se rozhodli minimalizovat dopad na uživatele. Ale v některých případech můžete chtít vyprázdní vyrovnávací paměti – například pokud používáte sady SDK v aplikaci, která ukončí.

*C#*
 
 ```C#
    telemetry.Flush();
    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(5000);
```

*Java*

```Java
    telemetry.flush();
    //Allow some time for flushing before shutting down
    Thread.sleep(5000);
```

    
*Node.js*

    telemetry.flush();

Upozorňujeme, že je asynchronní pro funkce [kanálu telemetrii serveru](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

V ideálním případě by měl metody flush() použít v aktivitě vypnutí aplikace.

## <a name="authenticated-users"></a>Ověření uživatelé
Ve webové aplikaci jsou uživatelé (ve výchozím nastavení) identifikovat pomocí souborů cookie. Uživatel může počítají více než jednou, pokud budou přistupovat k vaší aplikace z v jiném počítači či prohlížeči, nebo pokud se soubory cookie odstranit.

Pokud se uživatel přihlásí k aplikaci, můžete získat přesnější počet nastavením ID ověřený uživatel v prohlížeči kódu:

*JavaScript*

```JS
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

V technologie ASP.NET MVC aplikace, například:

*syntaxe Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Není nutné používat skutečné přihlašovací jméno uživatele. Pouze musí být ID, které jsou jedinečné pro daného uživatele. Nesmí obsahovat mezery ani znaky `,;=|`.

ID uživatele je také nastavit v souboru cookie relace a odesílá na server. Pokud je nainstalován server SDK, ID ověřený uživatel odešle jako součást vlastností kontextu telemetrie klient i server. Potom můžete filtrovat a hledání v něm.

Pokud vaše aplikace skupin uživatelů do účtů, můžete předat také identifikátor k účtu (s stejné omezení znaků).

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

V [Průzkumníku metrik](app-insights-metrics-explorer.md), můžete vytvořit graf, který udává **ověřeného uživatele,**, a **uživatelské účty**.

Můžete také [vyhledávání](app-insights-diagnostic-search.md) pro datové body klienta s účty a názvy konkrétního uživatele.

## <a name="properties"></a>Filtrování, vyhledávání a segmentace svá data pomocí vlastností
Můžete přiřadit události vlastnosti a měření (a také k metrikám, stránka zobrazení, výjimky a další data telemetrie).

*Vlastnosti* jsou řetězcové hodnoty, které můžete použít k filtrování telemetrie v sestavách využití. Například pokud vaše aplikace obsahuje několik hry, můžete připojit název hry pro každou jednotlivou událost tak, aby her, které jsou populárnější, si můžete zobrazit.

Existuje omezení 8192 na délku řetězce. (Pokud chcete odeslat velké množství dat, použijte parametr zpráva [TrackTrace](#track-trace).)

*Metriky* jsou číselné hodnoty, které lze zobrazit graficky. Například můžete chtít zjistit, zda je k postupné zvýšení skóre, které vaše hráči dosáhnout. V grafech mohou být segmentovány podle vlastnosti, které se odesílají s událostí, tak, aby vám samostatný nebo skládaný grafy pro různé hry.

Metriky hodnot, který se má zobrazit správně musí být větší než nebo rovna 0.

Některé [omezení počtu vlastností, hodnoty vlastností a metriky](#limits) , kterou můžete použít.

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);

*Node.js*

    // Set up some properties and metrics:
    var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
    var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

    // Send the event:
    telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});


*Visual Basic*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> Je třeba dbát na ve vlastnostech protokolu identifikovatelné osobní údaje.
>
>

*Pokud jste použili metriky*, otevřete Průzkumníka metrik a vyberte metriku z **vlastní** skupiny:

![Otevřete Průzkumníka metrik, vyberte graf a vyberte metriku](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> Pokud vaše metrika se nezobrazí, nebo pokud **vlastní** záhlaví nejsou, zavřete okno Výběr a zkuste to znovu později. Metriky někdy může trvat hodinu mají agregovat prostřednictvím kanálu.

*Pokud jste použili vlastnosti a metriky*, segmentovat metriku vlastností:

![Nastavit seskupování a potom vyberte vlastnost pod Seskupit podle](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*Ve vyhledávání diagnostiky*, můžete zobrazit vlastnosti a metriky jednotlivé výskyty události.

![Vyberte instance a pak vyberte "..."](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Použití **vyhledávání** pole zobrazíte výskytů události, které mají konkrétní hodnotu vlastnosti.

![Zadejte termín do vyhledávání](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Další informace o vyhledávacích výrazech](app-insights-diagnostic-search.md).

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternativní způsob, jak nastavit vlastnosti a metriky
Pokud je pohodlnější, můžete shromáždit parametry události v samostatném objektu:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> Nemáte opakovaně použít stejnou instanci položky telemetrie (`event` v tomto příkladu) Track*() volat vícekrát. To může způsobit telemetrických dat k odeslání s nesprávná konfigurace.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Vlastní měření a vlastnosti v Analytics

V [Analytics](app-insights-analytics.md), vlastní metriky a vlastnosti zobrazit v `customMeasurements` a `customDimensions` atributy každý záznam telemetrie.

Například pokud jste přidali vlastnost s názvem "herní" k žádosti o telemetrie, tento dotaz počet výskytů různé hodnoty "herní" a zobrazit průměr vlastní metriky "skóre":

```
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game) 
```

Všimněte si, že:

* Při extrahování hodnotu z customDimensions nebo customMeasurements JSON, má dynamické typ, a proto musíte vysílat `tostring` nebo `todouble`.
* Vzít v úvahu možnost [vzorkování](app-insights-sampling.md), měli byste použít `sum(itemCount)`, nikoli `count()`.



## <a name="timed"></a> Časování událostí
Někdy budete chtít grafu doba potřebná k provedení akce. Například můžete chtít vědět, jak dlouho uživatelé proveďte vzít v úvahu volbám v hru. Můžete pro tento parametr měření.

*C#*

```C#
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string>
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```Java
    long startTime = System.currentTimeMillis();

    // perform timed action

    long endTime = System.currentTimeMillis();
    Map<String, Double> metrics = new HashMap<>();
    metrics.put("ProcessingTime", endTime-startTime);

    // Setup some propereties
    Map<String, String> properties = new HashMap<>();
    properties.put("signalSource", currentSignalSource.getName());

    //send the event
    telemetry.trackEvent("SignalProcessed", properties, metrics);

```


## <a name="defaults"></a>Výchozí vlastnosti pro vlastní telemetrii
Pokud chcete nastavit výchozí hodnoty vlastností pro některé vlastní události, které lze zadat, můžete je nastavit v instanci TelemetryClient. Jsou připojené každé položce telemetrie, který se odesílá z tohoto klienta.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");
    
*Node.js*

    var gameTelemetry = new applicationInsights.TelemetryClient();
    gameTelemetry.commonProperties["Game"] = currentGame.Name;

    gameTelemetry.TrackEvent({name: "WinGame"});



Jednotlivé telemetrii volání můžete přepsat výchozí hodnoty ve slovnících jejich vlastnosti.

*Pro jazyk JavaScript webových klientů*, [pomocí jazyka JavaScript telemetrie inicializátory](#js-initializer).

*Přidání vlastnosti do všech telemetrie*, včetně dat z modulů standardního shromažďování [implementovat `ITelemetryInitializer` ](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Vzorkování, filtrování a zpracování telemetrie
Můžete napsat kód pro zpracování telemetrie před odesláním ze sady SDK. Zpracování obsahuje data, která se odesílá z modulů standardní telemetrie, jako je shromažďování požadavků HTTP a kolekce závislost.

[Přidání vlastnosti](app-insights-api-filtering-sampling.md#add-properties) k telemetrie implementací `ITelemetryInitializer`. Můžete například přidat čísla verzí nebo vypočtené hodnoty od dalších vlastností.

[Filtrování](app-insights-api-filtering-sampling.md#filtering) můžete změnit nebo zrušit telemetrie před odesláním ze sady SDK implementací `ITelemetryProcesor`. Můžete řídit, co se odesílá nebo se zahodí, ale budete muset účet pro vliv na vaše metriky. V závislosti na tom, jak zrušit položek může dojít ke ztrátě možnost přecházet mezi související položky.

[Vzorkování](app-insights-api-filtering-sampling.md) je zabalené řešení ke snížení objemu dat, který se odesílá z vaší aplikace na portál. Dělá to tak, aniž by to ovlivnilo zobrazených metrik. A dělá to tak, aniž by to ovlivnilo moct lépe diagnostikovat problémy tak, že přejdete mezi související položky jako výjimky, požadavky a zobrazení stránek.

[Další informace](app-insights-api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Vypnutí telemetrie
K *dynamicky zastavit a spustit* shromažďování a předávání telemetrie:

*C#*

```csharp

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```Java
    
    telemetry.getConfiguration().setTrackingDisabled(true);

```

K *zakázat vybrané standardní Kolektory*– například čítače výkonu, požadavky HTTP nebo závislosti – odstranit nebo komentář příslušné řádky v [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). To provedete, například pokud chcete odeslat vlastní TrackRequest data.

*Node.js*

```Javascript

    telemetry.config.disableAppInsights = true;
```

K *zakázat vybrané standardní Kolektory*– například čítače výkonu, požadavky HTTP nebo závislosti – v době inicializace řetězu metody konfigurace kódu inicializace SDK:

```Javascript

    applicationInsights.setup()
        .setAutoCollectRequests(false)
        .setAutoCollectPerformance(false)
        .setAutoCollectExceptions(false)
        .setAutoCollectDependencies(false)
        .setAutoCollectConsole(false)
        .start();
```

Pokud chcete zakázat tyto Kolektory po inicializaci, použijte objekt konfigurace: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Režim vývojáře
Během ladění, je užitečné používat telemetrie prostřednictvím kanálu je rychlé, aby mohli zobrazit výsledky okamžitě. Můžete také zprávy Další get, které vám pomůžou trasování všechny problémy s telemetrií. Vypněte ho v produkčním prostředí, protože mohou být zpomaleny vaší aplikace.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> Nastavení pro vybrané vlastní telemetrii klíč instrumentace
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> Klíč dynamické instrumentace
Abyste se vyhnuli kombinování až telemetrie z vývoj, testování a provozním prostředí, můžete [vytvořit samostatné prostředky Application Insights](app-insights-create-new-resource.md) a změnit jejich klíče, v závislosti na prostředí.

Místo získání klíč instrumentace z konfiguračního souboru, můžete ho nastavit v kódu. V metodě inicializace, jako jsou například souboru global.aspx.cs v službě ASP.NET nastavte:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



Na webových stránkách můžete ho nastavit ze stavu webový server, než kódování oznámena do skriptu. Například ve webové stránky vygenerované v aplikaci ASP.NET:

*JavaScript ve Razor*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey;
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient má vlastnost kontext, který obsahuje hodnoty, které jsou odeslány společně s všechny telemetrická data. Za normálních okolností jsou nastavené moduly standardní telemetrie, ale můžete také nastavit jejich sami. Příklad:

    telemetry.Context.Operation.Name = "MyOperationName";

Pokud jste nastavili některou z těchto hodnot sami, zvažte odebrání na příslušný řádek z [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)tak, aby vaše hodnoty a standardní hodnoty získat nerozumíte nemáte.

* **Součást**: aplikace a její verzi.
* **Zařízení**: Data o zařízení, na kterém je spuštěna aplikace. (Ve službě web apps, to je serveru nebo klientském zařízení, který se odesílá telemetrii z.)
* **InstrumentationKey**: The Application Insights prostředků v Azure, kde se zobrazí telemetrii. Ho je obvykle zachyceny ze souboru ApplicationInsights.config.
* **Umístění**: geografickém umístění zařízení.
* **Operace**: ve službě web apps, aktuální žádost HTTP. V jiných typů aplikací můžete tento parametr můžete nastavit pro skupiny událostí společně.
  * **ID**: generované hodnoty, které koreluje různých událostí, takže když si prohlédnout všechny události ve vyhledávání diagnostiky, můžete najít související položky.
  * **Název**: identifikátor, obvykle adresu URL požadavku HTTP.
  * **SyntheticSource**: není-li null nebo prázdný řetězec, který označuje, že zdroj žádosti bylo zjištěno, že robot nebo webový test. Ve výchozím nastavení je vyloučen z výpočtů v Průzkumníku metrik.
* **Vlastnosti**: vlastnosti, které se odesílají s všechny telemetrická data. Může být přepsána v jednotlivých sledovat * volání.
* **Relace**: uživatelské relace. ID nastavena pro generovanou hodnotu, která je změněn, pokud uživatel nebyl aktivní po dobu.
* **Uživatel**: informace o uživateli.

## <a name="limits"></a>Omezení
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

Abyste se vyhnuli stiskne omezení četnosti data, použijte [vzorkování](app-insights-sampling.md).

Chcete-li určit, jak dlouho se data ukládají najdete v tématu [uchovávání dat a ochrana osobních údajů](app-insights-data-retention-privacy.md).

## <a name="reference-docs"></a>Referenční dokumenty
* [Rozhraní ASP.NET – reference](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referenční informace sady Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Referenční dokumentace technologie JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Sada SDK pro Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [Sada SDK pro iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Kód SDK
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Balíčky systému Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [Všechny platformy](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Otázky
* *Jaké výjimky může vyvolat volání Track_()?*

    Žádné. Nemusíte zabalení je v klauzulích try-catch. Pokud sada SDK zaznamená problémy, bude protokolování zpráv ve výstupu konzoly ladění a--zprávy získat prostřednictvím – ve vyhledávání diagnostiky.
* *Je k dispozici rozhraní REST API k získání dat z portálu?*

    Ano, [rozhraní API pro přístup k datům](https://dev.applicationinsights.io/). Zahrnout další způsoby, jak extrahovat data [exportovat z analýz do Power BI](app-insights-export-power-bi.md) a [průběžné export](app-insights-export-telemetry.md).

## <a name="next"></a>Další kroky
* [Hledání událostí a protokolů](app-insights-diagnostic-search.md)

* [Řešení potíží](app-insights-troubleshoot-faq.md)


