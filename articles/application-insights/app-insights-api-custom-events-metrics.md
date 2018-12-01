---
title: Rozhraní API pro Application Insights pro vlastní události a metriky | Dokumentace Microsoftu
description: Vložte několik řádků kódu v zařízení nebo plochy aplikace, webové stránky nebo služby, ke sledování využití a diagnostiku problémů.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2018
ms.author: mbullwin
ms.openlocfilehash: 1e17eadf3d4321e36c31d9a7581e1b02e42c2d08
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682618"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Rozhraní API pro Application Insights pro vlastní události a metriky

Ve vaší aplikaci a zjistěte, jak uživatelé pracují s ním, nebo pro usnadnění diagnostiky potíží stačí vložte několik řádků kódu. Odesílat telemetrická data z aplikací pro zařízení a desktop, webovými klienty a webové servery. Použití [Azure Application Insights](app-insights-overview.md) core API telemetrie k odesílání vlastních událostí a metrik a verze standard telemetrická data. Toto rozhraní API je stejného rozhraní API, použít standardní kolekce dat Application Insights.

> [!NOTE]
> `TrackMetric()` už není upřednostňovaný způsob odesílání vlastních metrik pro .NET na základě aplikací. V [verze 2,60 beta 3](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/CHANGELOG.md#version-260-beta3) sady .NET SDK Application Insights novou metodu [ `TelemetryClient.GetMetric()` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) byla zavedena. Od .NET Application Insights SDK [verze 2.72](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) tato funkce je teď součástí stabilní verzi.

## <a name="api-summary"></a>Souhrn rozhraní API

Na všech platformách, kromě několik variant, jako je jednotné základního rozhraní API `GetMetric`(pouze .NET).

| Metoda | Používá pro |
| --- | --- |
| [`TrackPageView`](#page-views) |Stránky, obrazovky, okna nebo formuláře. |
| [`TrackEvent`](#trackevent) |Akce uživatele a další události. Používá ke sledování chování uživatelů nebo k monitorování výkonu. |
| [`GetMetric`](#getmetric) |Nula a vícedimenzionálních metrik, centrálně nakonfigurované agregace, jenom v C#. |
| [`TrackMetric`](#trackmetric) |Měření výkonu, jako je například délky front, které nesouvisí s konkrétní události. |
| [`TrackException`](#trackexception) |Protokolování výjimek pro diagnostiku. Trasování, kde k nim dojde ve vztahu k jiné události a zkontrolovat trasování zásobníku. |
| [`TrackRequest`](#trackrequest) |Protokolování četnost a doba trvání požadavků na serveru pro analýzu výkonu. |
| [`TrackTrace`](#tracktrace) |Diagnostický protokol zpráv. Můžete také zachytit protokoly třetích stran. |
| [`TrackDependency`](#trackdependency) |Protokolování doba trvání a četnost volání externí komponenty, které vaše aplikace závisí. |

Je možné [připojení vlastnosti a metriky](#properties) pro většinu těchto volání telemetrická data.

## <a name="prep"></a>Než začnete

Pokud nemáte odkaz na sadu SDK Application Insights ještě:

* Přidejte Application Insights SDK do projektu:

  * [Projekt ASP.NET](app-insights-asp-net.md)
  * [Projekt v Javě](app-insights-java-get-started.md)
  * [Projekt Node.js](app-insights-nodejs.md)
  * [V každé webové stránky JavaScript](app-insights-javascript.md) 
* Do kódu zařízení nebo webového serveru přidejte:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Získání TelemetryClient instance

Získat instanci `TelemetryClient` (s výjimkou v jazyce JavaScript ve webových stránkách):

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

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

TelemetryClient je bezpečná pro vlákno.

Pro projekty ASP.NET a Java jsou automaticky shromažďovat příchozích požadavků HTTP. Můžete chtít vytvořit další instance TelemetryClient pro ostatní moduly vaší aplikace. Například může mít jednu instanci TelemetryClient ve své třídě middleware na sestavu obchodní logiky události. Můžete nastavit vlastnosti, jako je ID uživatele a ID zařízení k identifikaci počítače. Tyto informace se připojuje ke všem událostem, které odešle instanci. 

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

V projektech, Node.js, můžete použít `new applicationInsights.TelemetryClient(instrumentationKey?)` k vytvoření nové instance, ale to se doporučuje jenom pro scénáře, které vyžadují izolované konfiguraci z typu singleton `defaultClient`.

## <a name="trackevent"></a>TrackEvent

Ve službě Application Insights *vlastní událost* je datový bod, který si můžete zobrazit v [Průzkumníka metrik](app-insights-metrics-explorer.md) jako agregovaného počtu a v [diagnostické vyhledávání](app-insights-diagnostic-search.md) jako jednotlivé události. (Nesouvisí se MVC nebo jiné rozhraní framework "události.")

Vložit `TrackEvent` volání v kódu mají spočítat různé události. Jak často uživatelé vybrat určitou funkci, jak často se dosažení určitého cíle nebo možná jak často využívají konkrétních typů chyb.

Například herní aplikaci odeslat událost pokaždé, když se uživatel služby wins hry:

*JavaScript*

```javascript
appInsights.trackEvent("WinGame");
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

Je k dispozici v telemetrii `customEvents` tabulku v [Application Insights Analytics](app-insights-analytics.md). Každý řádek představuje volání `trackEvent(..)` ve vaší aplikaci.

Pokud [vzorkování](app-insights-sampling.md) je v provozu, vlastnost itemCount zobrazuje hodnotu větší než 1. Příklad itemCount == 10 znamená, že 10 volání funkce trackEvent(), proces vzorkování přenášena pouze jeden z nich. Chcete-li získat správný počet vlastních událostí, používejte proto použít kód například `customEvents | summarize sum(itemCount)`.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Příklady

*C#*

```csharp
#pragma warning disable CA1716  // Namespace naming

namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using TraceSeveretyLevel = Microsoft.ApplicationInsights.DataContracts.SeverityLevel;

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
            // We use a common .Net pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", TraceSeveretyLevel.Error);
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
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric je zastaralé v sadě .NET SDK. Metriky musí být vždy předem agregovat napříč časové období před odesláním. Pomocí jednoho z přetížení GetMetric(..) jak získat objekt metriky pro přístup k možnosti předběžnou agregací SDK. Při implementaci vlastní logiky předběžnou agregací, můžete odeslat výsledný agregace – metoda (ITelemetry metricTelemetry) sledování. Pokud vaše aplikace vyžaduje odeslání telemetrie samostatné položky ve všech případech mohou oprávnění bez agregace v čase, pravděpodobně máte případ použití pro telemetrii událostí; Zobrazit TelemetryClient.TrackEvent (Microsoft.Applicationlnsights.DataContracts.EventTelemetry).

Application Insights můžete graf metrik, které nejsou přiřazené k určité události. Například může monitorovat délka fronty v pravidelných intervalech. S metrikami jednotlivými měřeními jsou méně zajímavé než odchylky a trendy a proto statistické grafy.

Aby bylo možné odeslání metrik do Application Insights, můžete použít `TrackMetric(..)` rozhraní API. Existují dva způsoby, jak odeslat metriku:

* Jednu hodnotu. Pokaždé, když je ve vaší aplikaci provést měření, odesíláte do Application Insights odpovídající hodnotu. Například předpokládejme, že máte metriku popisující počet položek v kontejneru. Během určitého časového období nejprve umístit tři položky do kontejneru a pak odeberte dvě položky. Proto by volat `TrackMetric` dvakrát: nejprve předejte hodnotu `3` a potom hodnotu `-2`. Application Insights jsou obě hodnoty uloženy vaším jménem.

* Agregace. Při práci s metrikami, každé jedno měření je zřídka relevantní. Místo toho je důležité souhrn co se stalo během určitého časového období. Volá tyto Souhrn _agregace_. V předchozím příkladu je agregovaná metrika součet pro toto časové období `1` a počet hodnoty metrik je `2`. Při použití agregace přístup pouze vyvolat `TrackMetric` jednou za časové období a odeslat agregované hodnoty. Toto je doporučená, protože to může výrazně snížit náklady a výkon režie odesláním méně datových bodů do Application Insights při stále shromažďuje všechny relevantní informace.

### <a name="examples"></a>Příklady

#### <a name="single-values"></a>Jednotlivé hodnoty

Odeslání jedné hodnoty metrik:

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

Je k dispozici v telemetrii `customMetrics` tabulku v [Application Insights Analytics](app-insights-analytics.md). Každý řádek představuje volání `trackMetric(..)` ve vaší aplikaci.

* `valueSum` – Toto je součtem měření. Střední hodnoty získáte dělit `valueCount`.
* `valueCount` -Počet měření, které byly seskupeny do tohoto `trackMetric(..)` volání.

## <a name="page-views"></a>Zobrazení stránek

V aplikaci na zařízení nebo webové stránky je odeslána telemetrie zobrazení stránky ve výchozím nastavení při načtení každou obrazovku nebo stránku. Ale můžete výběr změnit ke sledování zobrazení stránek v další nebo v různých časech. Například v aplikaci, která zobrazí karty a okna, můžete chtít sledovat na stránce pokaždé, když uživatel otevře nové okno.

Data uživatele a relace se odešle jako vlastnosti spolu s zobrazení stránek, proto po telemetrie zobrazení stránky poutavější grafy uživatelů a relací.

### <a name="custom-page-views"></a>Zobrazení vlastních stránek

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

Pokud máte několik karet na různých stránkách HTML, je příliš zadat adresu URL:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Zobrazení stránek časování

Ve výchozím nastavení, časy označení **doba načtení zobrazení stránky** se měří z, když prohlížeč odesílá žádosti, dokud se nazývá události načtení stránky v prohlížeči.

Místo toho máte tyto možnosti:

* Nastavení explicitní doby trvání [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) volání: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Použití zobrazení stránky časování volání `startTrackPage` a `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Název, který použijete jako první parametr přidruží spouštění a zastavování volání. Použije se výchozí název aktuální stránky.

Výsledný trvání načtení stránky zobrazí v Průzkumníku metrik jsou odvozeny z intervalu mezi voláními spouštění a zastavování. To je na vás jaký interval, ve skutečnosti čas.

### <a name="page-telemetry-in-analytics"></a>Telemetrie stránky v Analytics

V [Analytics](app-insights-analytics.md) dvou tabulek, zobrazení dat z operace prohlížeče:

* `pageViews` Tabulka obsahuje data týkající adresy URL a nadpis stránky
* `browserTimings` Tabulka obsahuje data o výkonu klienta, jako je čas potřebný ke zpracování příchozích dat.

Vyhledání, jak dlouho trvá prohlížeče pro zpracování různých stránkách:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Ke zjištění popularities různými prohlížeči:

```kusto
pageViews
| summarize count() by client_Browser
```

Přidružit zobrazení stránek pro volání AJAX, připojte se k závislosti:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

Server SDK používá TrackRequest k protokolování žádostí HTTP.

Můžete ji volat i sami Pokud chcete simulovat žádosti v kontextu, ve kterém nemáte spuštěný modul webové služby.

Doporučený způsob odesílání telemetrických dat požadavek je však kde žádost funguje jako <a href="#operation-context">kontext operace</a>.

## <a name="operation-context"></a>Kontext operace

Můžete porovnat položky telemetrie společně přidruží je kontext operace. Standardní modulu Sledování žádostí o to dělá pro výjimky a dalších událostí, které se odesílají během zpracování požadavku HTTP. V [hledání](app-insights-diagnostic-search.md) a [Analytics](app-insights-analytics.md), můžete snadno vyhledat všechny události přidružené k požadavku pomocí jeho operace s ID.

Zobrazit [korelace Telemetrie v Application Insights](application-insights-correlation.md) podrobné informace o korelace.

Při sledování telemetrická data ručně, nejjednodušší způsob, jak zajistit korelace telemetrie pomocí tohoto vzorce:

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

Spolu s nastavením objekt context operace `StartOperation` vytvoří položky telemetrie typu, který zadáte. Při vyřazení operaci, nebo pokud explicitně volat odešle položky telemetrie `StopOperation`. Pokud používáte `RequestTelemetry` jako typ telemetrie, jeho trvání je nastavena na časovaném intervalu mezi spouštění a zastavování.

Položky telemetrie hlášená v rozsahu operace budou podřízené tyto operace. Operace kontexty může být vnořený.

Ve službě Search je kontext operace slouží k vytvoření **související položky** seznamu:

![Související položky](./media/app-insights-api-custom-events-metrics/21.png)

Zobrazit [sledování vlastních operací pomocí Application Insights .NET SDK](application-insights-custom-operations-tracking.md) Další informace o sledování vlastních operací.

### <a name="requests-in-analytics"></a>Požadavky v Analytics

V [Application Insights Analytics](app-insights-analytics.md), požadavků zobrazit až za `requests` tabulky.

Pokud [vzorkování](app-insights-sampling.md) je v provozu, vlastnost itemCount zobrazí hodnotu větší než 1. Příklad itemCount == 10 znamená, že 10 volání trackRequest(), proces vzorkování přenášena pouze jeden z nich. Chcete-li získat správný počet požadavků a průměrná doba trvání segmentované podle názvů žádostí, použijte kód jako například:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Výjimky posílat do Application Insights:

* K [spočítat](app-insights-metrics-explorer.md), jako údaje o četnosti problém.
* K [prozkoumání jednotlivých výskytů](app-insights-diagnostic-search.md).

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

Sady SDK pro mnoho výjimek catch automaticky, proto není vždy nutné explicitně volat TrackException.

* ASP.NET: [napsat kód k zachycování výjimek](app-insights-asp-net-exceptions.md).
* J2EE: [výjimky jsou zachyceny automaticky](app-insights-java-get-started.md#exceptions-and-request-failures).
* Jazyk JavaScript: Výjimky jsou zachyceny automaticky. Pokud chcete zakázat automatické shromažďování, přidá řádek do kódu, který vložíte do své webové stránky:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Výjimky v Analytics

V [Application Insights Analytics](app-insights-analytics.md), výjimky v uveden `exceptions` tabulky.

Pokud [vzorkování](app-insights-sampling.md) je v provozu, `itemCount` vlastnosti zobrazuje hodnotu větší než 1. Příklad itemCount == 10 znamená, že 10 volání metody trackexception() procesu vzorkování přenášena pouze jeden z nich. Chcete-li získat správný počet výjimek segmentované podle typu výjimky, použijte kód jako například:

```kusto
exceptions
| summarize sum(itemCount) by type
```

Většinu informací důležité zásobník již je extrahován do samostatných proměnné, ale o přijetí změn od sebe `details` struktury a získejte další možnosti. Vzhledem k tomu, že tato struktura je dynamická, by měl přetypujte výsledek typu, který jste očekávali. Příklad:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Pokud chcete přidružit jejich související požadavky, výjimky, použijte spojení:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Pomocí TrackTrace pomoc s diagnostikou problémů odesláním "záznam s popisem cesty" do Application Insights. Umožňuje odesílání diagnostických dat a zkontrolujte je v [diagnostické vyhledávání](app-insights-diagnostic-search.md).

V rozhraní .NET [protokolu adaptéry](app-insights-asp-net-trace-logs.md) pomocí tohoto rozhraní API k odeslání protokolů třetích stran na portál.

V jazyce Java pro [standardní Protokolovací nástroje, jako je Log4J, Logback](app-insights-java-trace-logs.md) můžete odeslat protokoly třetích stran na portál Application Insights Log4j nebo Logback Appenders.

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

Můžete hledat obsah zprávy, ale (na rozdíl od hodnoty vlastností) nelze pomocí filtru na něj.

Omezení velikosti pro `message` je mnohem větší než limit na vlastnosti.
Výhodou TrackTrace je umístit relativně dlouho data ve zprávě. Můžete například kódovat následných dat existuje.  

Kromě toho můžete přidat úroveň závažnosti zprávě. A stejně jako další telemetrická data, můžete přidat hodnoty vlastností, které vám pomůžou filtr nebo hledaný pro různé skupiny trasování. Příklad:

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

V [hledání](app-insights-diagnostic-search.md), potom můžete snadno filtrovat všechny zprávy konkrétní závažnosti úrovně, které se vztahují ke konkrétní databázi.

### <a name="traces-in-analytics"></a>Trasování v Analytics

V [Application Insights Analytics](app-insights-analytics.md), volání TrackTrace zobrazí `traces` tabulky.

Pokud [vzorkování](app-insights-sampling.md) je v provozu, vlastnost itemCount zobrazuje hodnotu větší než 1. Příklad itemCount == 10 znamená, že 10 volání `trackTrace()`, proces vzorkování přenášena pouze jeden z nich. Chcete-li získat správný počet trasování volání, používejte proto kód, jako `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency

Volání TrackDependency použijte ke sledování doby odezvy a míru úspěšných volání extrení části kódu. Výsledky se zobrazí v grafech závislostí na portálu.

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
     // The call above has been made obsolete in the latest SDK. The updated call follows this format:
     // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
}
```

*Java*

```java
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

Mějte na paměti, že se sadami SDK serveru zahrnují [závislosti modulu](app-insights-asp-net-dependencies.md) , který zjišťuje a sleduje volání určitých závislostí automaticky – například k databázím a rozhraní REST API. Je třeba nainstalovat agenta na serveru, aby modul fungovat. 

V jazyce Java, volání určitých závislostí lze automaticky sledovat pomocí [agenta Java](app-insights-java-agent.md).

Toto volání použít, pokud chcete sledovat volání, která není catch automatizované sledování, nebo pokud nechcete, aby pro instalaci agenta.

Chcete-li vypnout standardní modul sledování závislostí v jazyce C#, upravte [soubor ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) a odstranit odkazy na `DependencyCollector.DependencyTrackingTelemetryModule`. V jazyce Java není nainstalujte agenta java Pokud nechcete automaticky shromažďovat standardní závislosti.

### <a name="dependencies-in-analytics"></a>Závislosti v Analytics

V [Application Insights Analytics](app-insights-analytics.md), trackDependency volá zobrazit `dependencies` tabulky.

Pokud [vzorkování](app-insights-sampling.md) je v provozu, vlastnost itemCount zobrazuje hodnotu větší než 1. Příklad itemCount == 10 znamená, že 10 volání trackDependency(), proces vzorkování přenášena pouze jeden z nich. Chcete-li získat správný počet závislostí segmentované komponenta cílového, použijte kód jako například:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Chcete-li přidružit závislosti jejich související požadavky, použijte spojení:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Vyprazdňování dat

Za normálních okolností sada SDK odesílá data v některých případech se rozhodli minimalizaci vlivu na uživatele. Nicméně v některých případech můžete chtít vyprázdní vyrovnávací paměť – například pokud používáte sady SDK v aplikaci, která ukončí.

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

Všimněte si, že je asynchronní pro funkci [kanálu serveru telemetrie](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Metoda vyprázdnění() v ideálním případě byste měli použít ve vypnutí aktivity aplikace.

## <a name="authenticated-users"></a>Ověření uživatelé

Ve webové aplikaci uživatelé (ve výchozím nastavení) označené soubory cookie. Uživatel může počítat více než jednou, pokud budou přistupovat k vaší aplikace v jiném počítači či prohlížeči, nebo pokud jsou soubory cookie odstranit.

Pokud se uživatelé přihlásí do vaší aplikace, můžete získat přesnější počet nastavením ID ověřeného uživatele v kódu v prohlížeči:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Na webu technologie ASP.NET MVC aplikace, například:

*Syntaxe Razor*

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

Není nutné použít skutečné přihlašovací jméno uživatele. Pouze musí být ID, které jsou jedinečné pro daného uživatele. Nesmí obsahovat mezery nebo znaky `,;=|`.

ID uživatele je také nastavit v souboru cookie relace a odeslat na server. Pokud je nainstalovaná sada SDK pro server, ID ověřeného uživatele se odešle jako součást vlastností kontext klienta i serveru telemetrie. Potom můžete filtrovat a hledat v něm.

Pokud vaše aplikace ve skupině uživatelů do účtů, můžete také předat identifikátor účtu (pomocí stejného omezení pro znaky).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

V [Průzkumníka metrik](app-insights-metrics-explorer.md), můžete vytvořit graf, který počítá **uživatelů, ověření**, a **uživatelské účty**.

Můžete také [hledání](app-insights-diagnostic-search.md) pro datové body klienta pomocí konkrétního uživatelského jména a účty.

## <a name="properties"></a>Filtrování, hledání a segmentace vašich dat pomocí vlastností

Můžete připojit vlastnosti a hodnoty pro události (a také na metriky, stránka zobrazení, výjimky a další telemetrická data).

*Vlastnosti* jsou řetězcové hodnoty, které můžete použít k filtrování telemetrických dat v sestavách využití. Například pokud vaše aplikace obsahuje několik her, můžete připojit název hry pro každou událost, kde můžete zobrazit her, které jsou další oblíbené.

Platí omezení na délku řetězce 8192. (Pokud chcete odeslat velké množství dat, použijte parametr zprávy z [TrackTrace](#track-trace).)

*Metriky* jsou číselné hodnoty, které lze zobrazit graficky. Můžete třeba chtít zjistit, jestli postupný nárůst skóre, které se svými uživateli dosáhnout. Grafy mohou být segmentovány podle vlastnosti, které jsou odesílány události, tak, aby vám samostatné nebo Skládané grafy pro různé hry.

Pro hodnoty metriky správně zobrazen by měla být větší než nebo rovna 0.

Zde jsou některé [omezení počtu metriky, vlastnosti a hodnoty vlastností](#limits) , který vám pomůže.

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
> Aby se postaral aby nedošlo k protokolování osobních identifikovatelných informací v okně Vlastnosti.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternativní způsob, jak nastavit vlastnosti a metriky

Pokud je pohodlnější, můžete shromažďovat parametry události v samostatném objektu:

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
> Nepoužívejte soubory stejnou instanci položky telemetrie (`event` v tomto příkladu) Track*() volat více než jednou. To může způsobit telemetrických dat k odeslání s nesprávnou konfigurací.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Vlastní měření a vlastností v Analytics

V [Analytics](app-insights-analytics.md), vlastní metriky a vlastnosti zobrazit v `customMeasurements` a `customDimensions` atributy každého záznamu telemetrická data.

Například pokud jste přidali vlastnost s názvem "Konec hry" do vaší telemetrie požadavku, tento dotaz se počítá výskyty různé hodnoty "Konec hry" a zobrazit průměrnou hodnotu vlastní metriky "skóre":

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Všimněte si, že:

* Při extrahování hodnoty z customDimensions nebo customMeasurements JSON má dynamického typu, a proto musíte vysílat `tostring` nebo `todouble`.
* Vzít v úvahu možnost [vzorkování](app-insights-sampling.md), měli byste použít `sum(itemCount)`, nikoli `count()`.

## <a name="timed"></a> Události časování

Někdy budete chtít graf, jak dlouho trvá provedení akce. Například můžete chtít vědět, jak dlouho uživatelé provést zvážit možnosti ve hře. To můžete použít parametr měření.

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
metrics.put("ProcessingTime", endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>Výchozí vlastnosti pro vlastní telemetrii

Pokud chcete nastavit výchozí hodnoty vlastností pro některé vlastní události, které lze zadat, můžete je nastavit v instanci TelemetryClient. Jsou připojeny k každé položky telemetrie odesílané z daného klienta.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.Properties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.Properties("Game") = currentGame.Name
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

Jednotlivé telemetrické volání můžete přepsat výchozí hodnoty v jejich vlastnost slovníky.

*Pro jazyk JavaScript webových klientů*, [pomocí jazyka JavaScript telemetrie inicializátory](#js-initializer).

*Přidání vlastnosti do všech telemetrie*, včetně dat z modulů standardního shromažďování [implementovat `ITelemetryInitializer` ](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Vzorkování, filtrování a zpracování telemetrie

Můžete napsat kód pro zpracování telemetrických dat před odesláním ze sady SDK. Zpracování obsahuje data, která se odesílá ze standardní telemetrie moduly, například shromažďování požadavků HTTP a závislostí kolekce.

[Přidání vlastností](app-insights-api-filtering-sampling.md#add-properties) k telemetrii implementací `ITelemetryInitializer`. Můžete například přidat čísla verzí nebo hodnoty, které se počítá z dalších vlastností.

[Filtrování](app-insights-api-filtering-sampling.md#filtering) můžete upravit nebo zrušit telemetrická data před odesláním ze sady SDK implementací `ITelemetryProcesor`. Můžete řídit, co je odeslána nebo zahozeny, ale budete muset počítat vliv na vaše metriky. V závislosti na tom, jak můžete zahodit položky můžete ztratit možnost navigace mezi související položky.

[Vzorkování](app-insights-api-filtering-sampling.md) je zabalená řešení a snížit objem dat, který je odesílán z vaší aplikace na portál. Dělá to aniž by to ovlivnilo zobrazených metrik. A dělá to aniž by to ovlivnilo moct lépe diagnostikovat problémy tak, že přejdete mezi související položky jako výjimky, požadavků a zobrazení stránek.

[Další informace](app-insights-api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Vypnutí telemetrie

K *dynamicky zastavení a spuštění* shromažďování a předávání telemetrických dat:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

K *zakázat vybrané standardní Kolektory*– například čítače výkonu, požadavky HTTP nebo závislosti – odstranit nebo okomentovat odpovídající řádky v [soubor ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Můžete to provedete, například pokud chcete poslat TrackRequest data.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

K *zakázat vybrané standardní Kolektory*– například čítače výkonu, požadavky HTTP nebo závislosti--během inicializace, zřetězení metody konfigurace sady SDK inicializace kódu:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Pokud chcete zakázat tyto kolekce po inicializaci, použijte objekt konfigurace: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Vývojářský režim

Během ladění, je užitečné mít telemetrie skrz kanál tak, aby ihned sledujte výsledky. Můžete také získat další zprávy, které vám pomůžou sledovat jakékoli problémy s telemetrická data. Vypněte ho v produkčním prostředí, protože to může zpomalit vaši aplikaci.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

## <a name="ikey"></a> Nastavení klíče instrumentace pro vybranou vlastní telemetrii

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a> Dynamické Instrumentační klíč

Nekombinujte se telemetrie z vývoj, testování a produkční prostředí, můžete [vytvořit samostatný prostředek Application Insights](app-insights-create-new-resource.md) a změňte jejich klíče, v závislosti na prostředí.

Místo zobrazování Instrumentační klíč z konfiguračního souboru, můžete ho nastavit ve vašem kódu. Nastavte klíč v metodě inicializace, jako je například souboru global.aspx.cs v rámci služby technologie ASP.NET:

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

Na webových stránkách může být vhodné provést nastavení ze stavu webový server, než doslova kódování do skriptu. Například ve webové stránky vygenerované v aplikaci ASP.NET:

*JavaScript v Razor*

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

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient má vlastnost kontextu, který obsahuje hodnoty, které jsou odeslány společně s všechny telemetrická data. Jsou obvykle nastaveny pomocí moduly standardní telemetrická data, ale můžete také nastavit jejich sami. Příklad:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Pokud nastavíte některou z těchto hodnot sami, zvažte odebrání relevantní řádek z [soubor ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), takže nemusíte nepřehledné hodnoty a standardní hodnoty.

* **Komponenta**: aplikace a její verzi.
* **Zařízení**: Data o zařízení, ve kterém je aplikace spuštěna. (Ve službě web apps, to je na serveru nebo klientského zařízení, které se odesílá telemetrická data ze.)
* **InstrumentationKey**: prostředku The Application Insights v Azure, kde se zobrazují telemetrická data. To obvykle vyzvednutí ze souboru ApplicationInsights.config.
* **Umístění**: zeměpisnou polohu zařízení.
* **Operace**: ve službě web apps, aktuální žádosti HTTP. V další typy aplikací můžete nastavit toto seskupování událostí společně.
  * **ID**: vygenerovanou hodnotu, která koreluje jednotlivé události, když si prohlédnout všechny události v diagnostickém vyhledávání, abyste našli související položky.
  * **Název**: identifikátor, obvykle adresa URL požadavku HTTP.
  * **SyntheticSource**: Ověřte, zda je null nebo prázdný řetězec, který označuje, že zdroj požadavku byly identifikovány jako robot nebo webový test. Ve výchozím nastavení je vyloučena z výpočtů v Průzkumníku metrik.
* **Vlastnosti**: vlastnosti, které se odesílají s všechny telemetrická data. Je možné přepsat v jednotlivých sledování * volání.
* **Relace**: uživatelské relace. ID je nastavena na vygenerovanou hodnotu, která se změní, pokud uživatel nebyl aktivní po nějakou dobu.
* **Uživatel**: informace o uživateli.

## <a name="limits"></a>Omezení

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

Chcete-li zabránit v dosažení omezení četnosti data, použijte [vzorkování](app-insights-sampling.md).

Chcete-li zjistit, jak dlouho se data ukládají, přečtěte si téma [uchovávání dat a ochrany osobních údajů](app-insights-data-retention-privacy.md).

## <a name="reference-docs"></a>Referenční dokumenty

* [Referenční dokumentace technologie ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referenční informace k Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Referenční dokumentace jazyka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Sada SDK pro Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [Sada SDK pro iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Kód SDK

* [Sada ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Balíčky Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [Všechny platformy](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Otázky

* *Jaké výjimky může vyvolat volání Track_()?*

    Žádné. Není nutné zabalit je do klauzule try-catch. Pokud sada SDK narazí na problémy, se protokolování zpráv ve výstupu konzoly ladění a--pokud zprávy zobrazí prostřednictvím – v diagnostickém vyhledávání.
* *Je k dispozici rozhraní REST API k získání dat z portálu?*

    Ano, [rozhraní API pro přístup k datům](https://dev.applicationinsights.io/). Další možnosti, jak extrahovat data zahrnují [exportovat z analýzy do Power BI](app-insights-export-power-bi.md) a [průběžný export](app-insights-export-telemetry.md).

## <a name="next"></a>Další kroky

* [Hledat události a protokoly](app-insights-diagnostic-search.md)
* [Řešení potíží](app-insights-troubleshoot-faq.md)
