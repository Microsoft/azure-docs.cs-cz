---
title: Rozhraní API Přehledů aplikací pro vlastní události a metriky | Dokumenty společnosti Microsoft
description: Vložte několik řádků kódu do aplikace, webové stránky nebo služby zařízení nebo stolního počítače, abyste sledovali problémy s používáním a diagnostikovali.
ms.topic: conceptual
ms.date: 03/27/2019
ms.openlocfilehash: d6cb2f5ab418e8d3b5935fef535565ccf55a3906
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536943"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Rozhraní API služby Application Insights pro vlastní události a metriky

Vložte několik řádků kódu do aplikace, abyste zjistili, co s ní uživatelé dělají, nebo pomozte diagnostikovat problémy. Telemetrie můžete odesílat z aplikací pro zařízení a stolních počítačů, webových klientů a webových serverů. Pomocí rozhraní API pro telemetrii jádra [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) můžete odesílat vlastní události a metriky a vlastní verze standardní telemetrie. Toto rozhraní API je stejné rozhraní API, které používají standardní kolekce dat Application Insights.

## <a name="api-summary"></a>Souhrn rozhraní API

Základní rozhraní API je jednotné na všech platformách, kromě několika variant, jako `GetMetric`je (pouze.NET).

| Metoda | Použití |
| --- | --- |
| [`TrackPageView`](#page-views) |Stránky, obrazovky, čepele nebo formuláře. |
| [`TrackEvent`](#trackevent) |Akce uživatele a další události. Slouží ke sledování chování uživatelů nebo ke sledování výkonu. |
| [`GetMetric`](#getmetric) |Nulové a vícerozměrné metriky, centrálně nakonfigurovaná agregace, pouze C#. |
| [`TrackMetric`](#trackmetric) |Měření výkonu, jako jsou délky fronty, které nesouvisejí s konkrétními událostmi. |
| [`TrackException`](#trackexception) |Protokolování výjimek pro diagnostiku. Trasování, kde k nim dochází ve vztahu k jiným událostem a zkontrolujte trasování zásobníku. |
| [`TrackRequest`](#trackrequest) |Protokolování četnosti a trvání požadavků serveru pro analýzu výkonu. |
| [`TrackTrace`](#tracktrace) |Zprávy protokolu diagnostiky prostředků. Můžete také zachytit protokoly třetích stran. |
| [`TrackDependency`](#trackdependency) |Protokolování doby trvání a četnosti volání externích součástí, na kterých vaše aplikace závisí. |

K většině těchto telemetrických volání můžete [připojit vlastnosti a metriky.](#properties)

## <a name="before-you-start"></a><a name="prep"></a>Než začnete

Pokud ještě nemáte odkaz na sdk application insights:

* Přidejte do projektu sazbujení aplikací:

  * [ASP.NET projekt](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Základní projekt](../../azure-monitor/app/asp-net-core.md)
  * [Projekt Java](../../azure-monitor/app/java-get-started.md)
  * [Projekt Node.js](../../azure-monitor/app/nodejs.md)
  * [JavaScript na každé webové stránce](../../azure-monitor/app/javascript.md) 
* Do kódu zařízení nebo webového serveru uveďte:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Získání instance TelemetryClient

Získejte instanci `TelemetryClient` (s výjimkou JavaScriptu na webových stránkách):

Pro [ASP.NET základní](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) aplikace a non [HTTP/Worker pro aplikace .NET/.NET Core,](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) je doporučeno získat instanci z `TelemetryClient` kontejneru vkládání závislostí, jak je vysvětleno v jejich příslušné dokumentaci.

Pokud používáte AzureFunctions v2+ nebo Azure WebJobs v3+ – postupujte podle tohoto dokumentu:https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
Pro každého, kdo vidí tuto metodu je zastaralé zprávy naleznete [microsoft/ApplicationInsights-dotnet#1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) pro další podrobnosti.

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient je bezpečný pro přístup z více vláken.

U projektů ASP.NET a Javy jsou příchozí požadavky HTTP automaticky zachyceny. Můžete chtít vytvořit další instance TelemetryClient pro jiný modul vaší aplikace. Například můžete mít jednu instanci TelemetryClient ve vaší třídě middleware pro hlášení událostí obchodní logiky. Můžete nastavit vlastnosti, jako je UserId a DeviceId k identifikaci počítače. Tyto informace jsou připojeny ke všem událostem, které instance odešle.

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

V projektech Node.js `new applicationInsights.TelemetryClient(instrumentationKey?)` můžete použít k vytvoření nové instance, ale to je doporučeno pouze `defaultClient`pro scénáře, které vyžadují izolovanou konfiguraci z ojedinělé .

## <a name="trackevent"></a>Událost TrackEvent

V Application Insights vlastní *událost* je datový bod, který můžete zobrazit v [Průzkumníku metrik](../../azure-monitor/platform/metrics-charts.md) jako agregovaný počet a diagnostické [vyhledávání](../../azure-monitor/app/diagnostic-search.md) jako jednotlivé výskyty. (Nesouvisí s MVC nebo jiné framework "události.")

Vložte `TrackEvent` volání do kódu počítat různé události. Jak často si uživatelé vybírají určitou funkci, jak často dosahují určitých cílů nebo jak často dělají určité typy chyb.

Například v herní aplikaci odešlete událost vždy, když uživatel vyhraje hru:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Vlastní události v Analytics

Telemetrie je k `customEvents` dispozici v tabulce v [Application Insights Analytics](analytics.md). Každý řádek představuje `trackEvent(..)` volání ve vaší aplikaci.

Pokud [vzorkování](../../azure-monitor/app/sampling.md) je v provozu, itemCount vlastnost zobrazuje hodnotu větší než 1. Například itemCount==10 znamená, že z 10 volání trackEvent(), proces vzorkování pouze přenášeny jeden z nich. Chcete-li získat správný počet vlastních událostí, `customEvents | summarize sum(itemCount)`měli byste proto použít kód, například .

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Příklady

*C#*

```csharp
namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric není upřednostňovanou metodou pro odesílání metrik. Metriky by měly být vždy předem agregovány v průběhu časového období před odesláním. Použijte jeden z GetMetric(..) přetížení získat objekt metriky pro přístup k sdk pre-agregace schopnosti. Pokud implementujete vlastní logiku předběžné agregace, můžete použít Metodu TrackMetric() k odeslání výsledných agregací. Pokud vaše aplikace vyžaduje odesílání samostatné položky telemetrie při každé příležitosti bez agregace v čase, pravděpodobně máte případ použití pro telemetrii událostí; viz TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry).

Application Insights můžete graf metriky, které nejsou připojeny k určité události. Můžete například sledovat délku fronty v pravidelných intervalech. S metrikami jsou jednotlivá měření méně zajímavá než odchylky a trendy, a proto jsou užitečné statistické grafy.

Chcete-li odesílat metriky do Application `TrackMetric(..)` Insights, můžete použít rozhraní API. Metriku můžete odeslat dvěma způsoby:

* Jedna hodnota. Pokaždé, když provedete měření ve vaší aplikaci, odešlete odpovídající hodnotu Application Insights. Předpokládejme například, že máte metriku popisující počet položek v kontejneru. Během určitého časového období nejprve vložíte tři položky do kontejneru a potom odeberete dvě položky. Proto byste volat `TrackMetric` dvakrát: nejprve `3` předání hodnoty `-2`a potom hodnotu . Application Insights ukládá obě hodnoty vaším jménem.

* Agregace. Při práci s metrikami je každé jednotlivé měření zřídka zajímavé. Místo toho je důležité shrnutí toho, co se stalo během určitého časového období. Takový souhrn se nazývá _agregace_. Ve výše uvedeném příkladu je `1` souhrnný součet metrik pro `2`toto časové období a je počet hodnot metriky . Při použití přístupu agregace, `TrackMetric` můžete vyvolat pouze jednou za časové období a odeslat agregační hodnoty. Toto je doporučený přístup, protože může výrazně snížit náklady a výkon režie odesláním méně datových bodů application insights, zatímco stále shromažďování všech relevantních informací.

### <a name="examples"></a>Příklady

#### <a name="single-values"></a>Jednotlivé hodnoty

Odeslání jediné hodnoty metriky:

*JavaScript*

 ```javascript
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

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Vlastní metriky v Analytics

Telemetrie je k `customMetrics` dispozici v tabulce v [Application Insights Analytics](analytics.md). Každý řádek představuje `trackMetric(..)` volání ve vaší aplikaci.

* `valueSum`- Tohle je součet měření. Chcete-li získat střední `valueCount`hodnotu, vydělte .
* `valueCount`- Počet měření, které byly `trackMetric(..)` agregovány do tohoto volání.

## <a name="page-views"></a>Zobrazení stránky

Telemetrie zobrazení stránky se v aplikaci zařízení nebo webové stránky odesílá ve výchozím nastavení při načtení každé obrazovky nebo stránky. Můžete to však změnit tak, aby sledovala zobrazení stránek v dalších nebo odlišných časech. Například v aplikaci, která zobrazuje karty nebo okna, můžete chtít sledovat stránku vždy, když uživatel otevře nové okno.

Data uživatelů a relací jsou odesílána jako vlastnosti spolu se zobrazeními stránek, takže grafy uživatelů a relací ožívají, když je telemetrie zobrazení stránky.

### <a name="custom-page-views"></a>Vlastní zobrazení stránek

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Pokud máte několik karet v rámci různých stránek HTML, můžete také zadat adresu URL:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Časování zobrazení stránky

Ve výchozím nastavení se časy hlášené jako **doba načítání zobrazení stránky** měří od okamžiku, kdy prohlížeč odešle požadavek, dokud není volána událost načítání stránky prohlížeče.

Místo toho můžete buď:

* Nastavte explicitní dobu trvání ve volání `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);` [trackPageView:](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) .
* Použijte časovací volání `startTrackPage` `stopTrackPage`zobrazení stránky a .

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Název, který používáte jako první parametr přidruží volání start a stop. Ve výchozím nastavení se používá aktuální název stránky.

Výsledné doby načítání stránky zobrazené v Průzkumníku metrik jsou odvozeny z intervalu mezi voláním zahájení a zastavení. Je jen na vás, jaký interval jste vlastně čas.

### <a name="page-telemetry-in-analytics"></a>Telemetrie stránky v Analytics

Ve [službě Analytics](analytics.md) se ve dvou tabulkách zobrazují data z operací prohlížeče:

* Tabulka `pageViews` obsahuje data o adrese URL a názvu stránky.
* Tabulka `browserTimings` obsahuje data o výkonu klienta, například čas na zpracování příchozích dat.

Jak zjistit, jak dlouho trvá zpracování různých stránek prohlížečem:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Chcete-li zjistit popularity různých prohlížečů:

```kusto
pageViews
| summarize count() by client_Browser
```

Chcete-li přidružit zobrazení stránky k volání AJAX, spojte se závislostmi:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>Požadavek na skladbu

Sada SDK serveru používá trackrequest k protokolování požadavků HTTP.

Můžete také volat sami, pokud chcete simulovat požadavky v kontextu, kde nemáte spuštěn modul webové služby.

Doporučený způsob odeslání telemetrie požadavku je však v případě, že požadavek funguje jako <a href="#operation-context">kontext operace</a>.

## <a name="operation-context"></a>Kontext operace

Můžete korelovat telemetrické položky dohromady jejich spojením s kontextem operace. Standardní modul sledování požadavků to pro výjimky a další události, které jsou odesílány při zpracování požadavku HTTP. Ve [vyhledávání](../../azure-monitor/app/diagnostic-search.md) a [analytice](analytics.md)můžete snadno najít všechny události spojené s požadavkem pomocí jeho ID operace.

Další podrobnosti o [korelaci telemetrie v přehledech aplikací](../../azure-monitor/app/correlation.md) najdete v tématu Telemetrická korelace.

Při ručním sledování telemetrie nejjednodušší způsob, jak zajistit korelaci telemetrie pomocí tohoto vzoru:

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

Spolu s nastavením `StartOperation` kontextu operace vytvoří položku telemetrie typu, který zadáte. Odešle položku telemetrie při vyřazení operace `StopOperation`nebo pokud explicitně volat . Pokud použijete `RequestTelemetry` jako typ telemetrie, jeho doba trvání je nastavena na časový interval mezi start a stop.

Telemetrické položky hlášené v rámci operace se stanou "podřízenými" takové operace. Kontexty operace mohou být vnořeny.

Při hledání se kontext operace používá k vytvoření seznamu **Související položky:**

![Související položky](./media/api-custom-events-metrics/21.png)

Další informace o sledování vlastních operací najdete v [tématu Sledování vlastních operací pomocí sady Application Insights .NET SDK.](../../azure-monitor/app/custom-operations-tracking.md)

### <a name="requests-in-analytics"></a>Požadavky v Analytics

V [application insights analytics](analytics.md)se v `requests` tabulce zobrazí požadavky.

Pokud [vzorkování](../../azure-monitor/app/sampling.md) je v provozu, itemCount vlastnost zobrazí hodnotu větší než 1. Například itemCount==10 znamená, že z 10 volání trackRequest(), proces vzorkování pouze přenášeny jeden z nich. Chcete-li získat správný počet požadavků a průměrnou dobu trvání segmentované podle názvů požadavků, použijte kód, například:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Odesílat výjimky do Application Insights:

* Chcete-li [je počítat](../../azure-monitor/platform/metrics-charts.md), jako údaj o četnosti problému.
* Zkoumat [jednotlivé výskyty](../../azure-monitor/app/diagnostic-search.md).

Sestavy obsahují trasování zásobníku.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Sady SDK zachytit mnoho výjimek automaticky, takže není vždy muset volat TrackException explicitně.

* ASP.NET: [Napište kód pro zachycení výjimek](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: [Výjimky jsou zachyceny automaticky](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: Výjimky jsou zachyceny automaticky. Pokud chcete zakázat automatickou kolekci, přidejte řádek do fragmentu kódu, který vložíte na webové stránky:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Výjimky v Analytics

V [application insights analytics](analytics.md)se v `exceptions` tabulce zobrazují výjimky.

Pokud [vzorkování](../../azure-monitor/app/sampling.md) je `itemCount` v provozu, vlastnost zobrazuje hodnotu větší než 1. Například itemCount==10 znamená, že z 10 volání trackException(), proces vzorkování pouze přenášeny jeden z nich. Chcete-li získat správný počet výjimek segmentovaných podle typu výjimky, použijte kód, například:

```kusto
exceptions
| summarize sum(itemCount) by type
```

Většina důležitých informací zásobníku je již extrahována do `details` samostatných proměnných, ale můžete rozdělit strukturu, abyste získali další. Vzhledem k tomu, že tato struktura je dynamická, měli byste přetypovat výsledek na očekávaný typ. Příklad:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Chcete-li přidružit výjimky k jejich souvisejícím požadavkům, použijte spojení:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>Sledovat trasu

Pomocí tracktrace můžete diagnostikovat problémy odesláním "drobečková navigace" do Application Insights. Můžete odeslat bloky diagnostických dat a zkontrolovat je v [diagnostickém vyhledávání](../../azure-monitor/app/diagnostic-search.md).

V [adaptérech protokolu](../../azure-monitor/app/asp-net-trace-logs.md) .NET použijte toto rozhraní API k odesílání protokolů třetích stran na portál.

V jazyce Java pro [standardní úhozy kláves, jako je Log4J, přihlášení](../../azure-monitor/app/java-trace-logs.md) pomocí Application Insights Log4j nebo Logback Appenders k odesílání protokolů třetích stran na portál.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*JavaScript na straně klienta/prohlížeče*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Protokolovat diagnostickou událost, jako je například zadání nebo opuštění metody.

 Parametr | Popis
---|---
`message` | Diagnostická data. Může být mnohem delší než jméno.
`properties` | Mapa řetězec na řetězec: Další data používaná k [filtrování výjimek](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) na portálu. Výchozí hodnota je prázdná.
`severityLevel` | Podporované hodnoty: [SeverityLevel.ts](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Obsah zprávy můžete vyhledávat, ale (na rozdíl od hodnot vlastností) na něm nemůžete filtrovat.

Limit velikosti `message` na je mnohem vyšší než limit na vlastnosti.
Výhodou TrackTrace je, že můžete umístit relativně dlouhá data ve zprávě. Například můžete kódovat data POST tam.  

Kromě toho můžete ke zprávě přidat úroveň závažnosti. A stejně jako ostatní telemetrie můžete přidat hodnoty vlastností, které vám pomohou filtrovat nebo vyhledávat různé sady trasování. Příklad:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

Ve [vyhledávání](../../azure-monitor/app/diagnostic-search.md)pak můžete snadno odfiltrovat všechny zprávy určité úrovně závažnosti, které se vztahují k určité databázi.

### <a name="traces-in-analytics"></a>Stopy v Analytics

V [Application Insights Analytics](analytics.md)se v `traces` tabulce zobrazí volání tracktrace.

Pokud [vzorkování](../../azure-monitor/app/sampling.md) je v provozu, itemCount vlastnost zobrazuje hodnotu větší než 1. Například itemCount==10 znamená, že `trackTrace()`z 10 volání , proces vzorkování pouze přenášeny jeden z nich. Chcete-li získat správný počet trasování volání, `traces | summarize sum(itemCount)`měli byste použít proto kód, jako je například .

## <a name="trackdependency"></a>Závislost track

Pomocí trackdependency volání sledovat doby odezvy a úspěšnost volání externí část kódu. Výsledky se zobrazí v grafech závislostí na portálu. Níže uvedený fragment kódu je třeba přidat všude, kde je provedeno volání závislosti.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
Instant startTime = Instant.now();
try {
    success = dependency.call();
}
finally {
    Instant endTime = Instant.now();
    Duration delta = Duration.between(startTime, endTime);
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    RemoteDependencyTelemetry.setTimeStamp(startTime);
    RemoteDependencyTelemetry.trackDependency(dependencyTelemetry);
}
```

*Node.js*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Nezapomeňte, že sady SDK serveru obsahují [modul závislostí,](../../azure-monitor/app/asp-net-dependencies.md) který automaticky zjišťuje a sleduje určitá volání závislostí – například do databází a rozhraní REST API. Musíte nainstalovat agenta na serveru, aby modul fungoval. 

V jazyce Java lze určitá volání závislostí automaticky sledovat pomocí [Java Agent](../../azure-monitor/app/java-agent.md).

Toto volání použijte, pokud chcete sledovat volání, které automatické sledování nezachytí, nebo pokud nechcete agenta nainstalovat.

Chcete-li vypnout standardní modul sledování závislostí v c#, upravte `DependencyCollector.DependencyTrackingTelemetryModule` [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) a odstraňte odkaz na aplikaci . V Javě prosím neinstalujte java agenta, pokud nechcete automaticky shromažďovat standardní závislosti.

### <a name="dependencies-in-analytics"></a>Závislosti v Analytics

V [Application Insights Analytics](analytics.md)se v `dependencies` tabulce zobrazí volání trackDependency.

Pokud [vzorkování](../../azure-monitor/app/sampling.md) je v provozu, itemCount vlastnost zobrazuje hodnotu větší než 1. Například itemCount==10 znamená, že z 10 volání trackDependency(), proces vzorkování pouze přenášeny jeden z nich. Chcete-li získat správný počet závislostí segmentovaných podle cílové součásti, použijte kód, například:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Chcete-li přidružit závislosti k jejich souvisejícím požadavkům, použijte spojení:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Vyprázdnění dat

Za normálních okolností sada SDK odesílá data v pevných intervalech (obvykle 30 sekund) nebo vždy, když je plná vyrovnávací paměť (obvykle 500 položek). V některých případech však můžete chtít vyprázdnění vyrovnávací paměti – například pokud používáte sadu SDK v aplikaci, která se vypne.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

Funkce je asynchronní pro [serverový telemetrický kanál](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

V ideálním případě flush() metoda by měla být použita v vypnutí aktivity aplikace.

## <a name="authenticated-users"></a>Skupina Authenticated Users

Ve webové aplikaci jsou uživatelé (ve výchozím nastavení) označeni soubory cookie. Uživatel může být započítán více než jednou, pokud přistupuje k vaší aplikaci z jiného počítače nebo prohlížeče nebo pokud odstraní soubory cookie.

Pokud se uživatelé přihlásí k vaší aplikaci, můžete získat přesnější počet nastavením ověřeného ID uživatele v kódu prohlížeče:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

V ASP.NET webové aplikaci MVC, například:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Není nutné používat skutečné přihlašovací jméno uživatele. Musí se jedná pouze o ID, které je pro tohoto uživatele jedinečné. Nesmí obsahovat mezery ani `,;=|`žádné znaky .

ID uživatele je také nastaveno v souboru cookie relace a odesláno na server. Pokud je nainstalována sada SDK serveru, je ověřené ID uživatele odesláno jako součást vlastností kontextu telemetrie klienta i serveru. Poté jej můžete filtrovat a vyhledávat.

Pokud vaše aplikace seskupuje uživatele na účty, můžete také předat identifikátor účtu (se stejným omezením znaků).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

V [Průzkumníku metrik](../../azure-monitor/platform/metrics-charts.md)můžete vytvořit graf, který počítá **účty Uživatelé, Ověřené**a **Uživatelské .**

Můžete také [vyhledávat](../../azure-monitor/app/diagnostic-search.md) datové body klienta s konkrétními uživatelskými jmény a účty.

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Filtrování, vyhledávání a segmentace dat pomocí vlastností

K událostem (a také k metrikám, zobrazením stránek, výjimkám a dalším telemetrickým datům) můžete připojit vlastnosti a měření.

*Vlastnosti* jsou řetězcové hodnoty, které můžete použít k filtrování telemetrie v sestavách využití. Pokud například aplikace obsahuje několik her, můžete ke každé události připojit název hry, abyste viděli, které hry jsou populárnější.

Na délku řetězce je limit 8192. (Pokud chcete odeslat velké bloky dat, použijte parametr zprávy TrackTrace.)

*Metriky* jsou číselné hodnoty, které lze graficky prezentovat. Můžete například chtít zjistit, zda dochází k postupnému zvyšování skóre, kterého vaši hráči dosahují. Grafy lze segmentovat podle vlastností, které jsou odesílány s událostí, takže můžete získat samostatné nebo skládané grafy pro různé hry.

Pro metriky hodnoty, které mají být správně zobrazeny, by měly být větší nebo rovna 0.

Existují určitá [omezení počtu vlastností, hodnot vlastností a metrik,](#limits) které můžete použít.

*JavaScript*

```javascript
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
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Dbejte na to, abyste nezaznamenávali osobně identifikovatelné informace ve vlastnostech.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternativní způsob nastavení vlastností a metrik

Pokud je to pohodlnější, můžete shromažďovat parametry události v samostatném objektu:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Nepoužívejte znovu stejnou instanci telemetrické položky (v`event` tomto příkladu) k volání Track*() vícekrát. To může způsobit telemetrie, které mají být odeslány s nesprávnou konfiguraci.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Vlastní měření a vlastnosti v Analytics

V [Analytics](analytics.md)se vlastní metriky `customMeasurements` a `customDimensions` vlastnosti zobrazují v atributech a jednotlivých záznamů telemetrie.

Pokud jste například do telemetrie požadavku přidali vlastnost s názvem "hra", tento dotaz spočítá výskyty různých hodnot "hry" a zobrazí průměr vlastní metriky "skóre":

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Všimněte si, že:

* Při extrahování hodnoty z customDimensions nebo customMeasurements JSON, má dynamický `tostring` typ, a proto je nutné přetypovat nebo `todouble`.
* Aby se zohlednila možnost [odběru](../../azure-monitor/app/sampling.md)vzorků `sum(itemCount)`, `count()`měli byste použít , ne .

## <a name="timing-events"></a><a name="timed"></a>Časování událostí

Někdy chcete zmapovat, jak dlouho trvá provedení akce. Můžete například chtít vědět, jak dlouho uživatelům trvá, než zváží volby ve hře. Pro to můžete použít parametr měření.

*C#*

```csharp
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

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Výchozí vlastnosti pro vlastní telemetrii

Pokud chcete nastavit výchozí hodnoty vlastností pro některé vlastní události, které píšete, můžete je nastavit v instanci TelemetryClient. Jsou připojeny ke každé položce telemetrie, která je odeslána z tohoto klienta.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Jednotlivá telemetrická volání mohou přepsat výchozí hodnoty ve svých slovníkech vlastností.

*Pro webové klienty JavaScriptu*použijte telemetrické inicializátory JavaScriptu.

*Chcete-li přidat vlastnosti do všech telemetrie*, včetně dat ze standardních modulů kolekce, [implementujte `ITelemetryInitializer` ](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Vzorkování, filtrování a zpracování telemetrie

Můžete napsat kód pro zpracování telemetrie před odesláním z sady SDK. Zpracování zahrnuje data odeslaná ze standardních modulů telemetrie, jako je například shromažďování požadavků HTTP a shromažďování závislostí.

[Přidejte vlastnosti](../../azure-monitor/app/api-filtering-sampling.md#add-properties) telemetrie implementací `ITelemetryInitializer`. Můžete například přidat čísla verzí nebo hodnoty, které jsou vypočteny z jiných vlastností.

[Filtrování](../../azure-monitor/app/api-filtering-sampling.md#filtering) můžete upravit nebo zahodit telemetrie před odesláním z `ITelemetryProcessor`sady SDK implementací . Můžete určit, co je odesláno nebo zahozeno, ale musíte vzít v úvahu vliv na vaše metriky. V závislosti na způsobu zahození položek může dojít ke ztrátě možnosti navigace mezi souvisejícími položkami.

[Vzorkování](../../azure-monitor/app/api-filtering-sampling.md) je zabalené řešení pro snížení objemu dat odeslaných z vaší aplikace na portál. Činí tak bez ovlivnění zobrazených metrik. A činí tak bez ovlivnění vaší schopnosti diagnostikovat problémy procházením mezi souvisejícími položkami, jako jsou výjimky, požadavky a zobrazení stránek.

[Další informace](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Zakázání telemetrie

Dynamické *zastavení a spuštění* shromažďování a přenosu telemetrie:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Chcete-li *zakázat vybrané standardní kolektory*--například čítače výkonu, požadavky HTTP nebo závislosti --odstraňte nebo zakomentujte příslušné řádky v [souboru ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Můžete to provést, například pokud chcete odeslat vlastní TrackRequest data.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Chcete-li *zakázat vybrané standardní kolektory*--například čítače výkonu, požadavky HTTP nebo závislosti -- v době inicializace, metody konfigurace řetězce do kódu inicializace sady SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Chcete-li tyto kolektory po inicializaci zakázat, použijte objekt Configuration:`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Vývojářský režim

Během ladění je užitečné mít telemetrie urychlena prostřednictvím kanálu, takže můžete okamžitě zobrazit výsledky. Získáte také další zprávy, které vám pomohou sledovat všechny problémy s telemetrií. Vypněte ji v produkčním prostředí, protože to může zpomalit vaši aplikaci.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Pro Node.js můžete povolit režim vývojáře `setInternalLogging` povolením interní protokolování přes a nastavení `maxBatchSize` 0, což způsobí, že telemetrie, které mají být odeslány, jakmile je shromážděna.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a>Nastavení klíče instrumentace pro vybranou vlastní telemetrii

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dynamický klíč instrumentace

Chcete-li se vyhnout míchání telemetrie z vývojového, testovacího a produkčního prostředí, můžete [vytvořit samostatné prostředky Application Insights](../../azure-monitor/app/create-new-resource.md ) a změnit jejich klíče v závislosti na prostředí.

Místo získání klíče instrumentace z konfiguračního souboru jej můžete nastavit v kódu. Nastavte klíč v metodě inicializace, například global.aspx.cs ve službě ASP.NET:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

Na webových stránkách jej můžete nastavit ze stavu webového serveru, nikoli dodoslova do skriptu. Například na webové stránce generované v ASP.NET aplikaci:

*JavaScript v břitvě*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetrieKontext

TelemetryClient má Vlastnost Context, která obsahuje hodnoty, které jsou odesílány spolu se všemi daty telemetrie. Obvykle jsou nastaveny standardními moduly telemetrie, ale můžete je také nastavit sami. Příklad:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Pokud nastavíte některou z těchto hodnot sami, zvažte odebrání příslušného řádku z [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), aby vaše hodnoty a standardní hodnoty nebyly zmatené.

* **Komponenta**: Aplikace a její verze.
* **Zařízení**: Data o zařízení, ve kterém je aplikace spuštěna. (Ve webových aplikacích se jedná o server nebo klientské zařízení, ze kterého je telemetrie odesílána.)
* **InstrumentationKey:** Prostředek Application Insights v Azure, kde se zobrazí telemetrie. Obvykle se vyzvednou z applicationinsights.config.
* **Umístění**: Zeměpisná poloha zařízení.
* **Operace**: Ve webových aplikacích aktuální požadavek HTTP. V jiných typech aplikací můžete nastavit tak, aby seskupiludálosti dohromady.
  * **ID**: Vygenerovaná hodnota, která koreluje různé události, takže při kontrole jakékoli události v diagnostickém vyhledávání můžete najít související položky.
  * **Název**: Identifikátor, obvykle adresa URL požadavku HTTP.
  * **SyntheticSource**: Pokud není null nebo prázdné, řetězec, který označuje, že zdroj požadavku byl identifikován jako robot nebo webový test. Ve výchozím nastavení je vyloučena z výpočtů v Průzkumníku metrik.
* **Vlastnosti**: Vlastnosti, které jsou odesílány se všemi daty telemetrie. To může být přepsána v jednotlivých track * volání.
* **Relace**: Relace uživatele. ID je nastavena na vygenerované hodnoty, která se změní, když uživatel nebyl aktivní na chvíli.
* **Uživatel**: Informace o uživateli.

## <a name="limits"></a>Omezení

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Chcete-li se vyhnout dosažení limitu rychlosti přenosové rychlosti, použijte [vzorkování](../../azure-monitor/app/sampling.md).

Informace o tom, jak dlouho jsou data uchovávána, najdete v [tématu Uchovávání dat a ochrana osobních údajů](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Referenční dokumenty

* [ASP.NET odkaz](https://docs.microsoft.com/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Referenční příručka v jazyce Java](https://docs.microsoft.com/java/api/overview/azure/appinsights?view=azure-java-stable/)
* [Referenční příručka javascriptu](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


## <a name="sdk-code"></a>Kód sady SDK

* [Sada ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Balíčky systému Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="questions"></a>Dotazy

* *Jaké výjimky může Track_() volání vyvolat?*

    Žádné. Nemusíte je zabalit do klauzulí o try-catch. Pokud sada SDK narazí na problémy, bude protokolovat zprávy ve výstupu konzoly ladění a - pokud zprávy projít - v diagnostické vyhledávání.
* *Existuje rozhraní REST API pro získání dat z portálu?*

    Ano, [rozhraní API pro přístup k datům](https://dev.applicationinsights.io/). Mezi další způsoby extrahování dat patří [export z Analytics do Power BI](../../azure-monitor/app/export-power-bi.md ) a [nepřetržitý export](../../azure-monitor/app/export-telemetry.md).

## <a name="next-steps"></a><a name="next"></a>Další kroky

* [Hledání událostí a protokolů](../../azure-monitor/app/diagnostic-search.md)
* [Odstraňování potíží](../../azure-monitor/app/troubleshoot-faq.md)
