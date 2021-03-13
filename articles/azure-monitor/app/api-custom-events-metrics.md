---
title: Application Insights rozhraní API pro vlastní události a metriky | Microsoft Docs
description: Pokud chcete sledovat využití a diagnostikovat problémy, vložte do svého zařízení nebo do aplikace, webové stránky nebo služby pár řádků kódu.
ms.topic: conceptual
ms.date: 05/11/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: a3fa14dcd406f6372a7fb409b92d9db3e8404ca7
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419027"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Rozhraní API služby Application Insights pro vlastní události a metriky

Do své aplikace vložte pár řádků kódu, abyste zjistili, co uživatelé s ním pracují, nebo aby vám pomohla diagnostikovat problémy. Telemetrii můžete odesílat ze zařízení a desktopových aplikací, webových klientů a webových serverů. Použijte rozhraní API telemetrie [Azure Application Insights](./app-insights-overview.md) Core k posílání vlastních událostí a metrik a vašich vlastních verzí standardní telemetrie. Toto rozhraní API je stejné rozhraní API, které používá standardní Application Insights sběrače dat.

## <a name="api-summary"></a>Souhrn rozhraní API

Základní rozhraní API je na všech platformách stejné, kromě několika variant, jako je `GetMetric` (jenom .NET).

| Metoda | Použití |
| --- | --- |
| [`TrackPageView`](#page-views) |Stránky, obrazovky, listy nebo formuláře. |
| [`TrackEvent`](#trackevent) |Akce uživatele a další události. Používá se ke sledování chování uživatelů nebo ke sledování výkonu. |
| [`GetMetric`](#getmetric) |Nulové a multidimenzionální metriky, centrálně konfigurovaná agregace, pouze C#. |
| [`TrackMetric`](#trackmetric) |Měření výkonu, jako jsou například délky front, které nesouvisí s konkrétními událostmi. |
| [`TrackException`](#trackexception) |Protokolování výjimek pro diagnostiku. Sledovat, kde se vyskytují ve vztahu k ostatním událostem a prozkoumávat trasování zásobníku. |
| [`TrackRequest`](#trackrequest) |Protokolování četnosti a doby trvání požadavků serveru pro analýzu výkonu. |
| [`TrackTrace`](#tracktrace) |Zprávy protokolu diagnostiky prostředků. Můžete také zachytit protokoly třetích stran. |
| [`TrackDependency`](#trackdependency) |Protokolování doby trvání a frekvence volání externích komponent, na kterých závisí vaše aplikace. |

K většině těchto volání telemetrie můžete [připojit vlastnosti a metriky](#properties) .

## <a name="before-you-start"></a><a name="prep"></a>Než začnete

Pokud ještě nemáte odkaz na Application Insights SDK, postupujte takto:

* Přidejte sadu Application Insights SDK do projektu:

  * [Projekt ASP.NET](./asp-net.md)
  * [ASP.NET Core projekt](./asp-net-core.md)
  * [Projekt Java](./java-get-started.md)
  * [Node.js projekt](./nodejs.md)
  * [JavaScript na každé webové stránce](./javascript.md) 
* Do kódu zařízení nebo webového serveru zadejte:

    *Jazyk C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Získat instanci TelemetryClient

Získat instanci `TelemetryClient` (kromě v jazyce JavaScript na webových stránkách):

Pro aplikace [ASP.NET Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) a [non http/Worker pro .NET/.NET Core](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) se doporučuje získat instanci `TelemetryClient` z kontejneru injektáže vkládání závislostí, jak je vysvětleno v příslušné dokumentaci.

Pokud používáte AzureFunctions v2 + nebo Azure WebJobs v3 +, postupujte podle tohoto dokumentu: https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
Pro každého, kdo vidí tuto metodu, jsou zastaralé zprávy, další podrobnosti najdete v [Microsoft/ApplicationInsights-dotnet # 1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) .

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

Pro projekty ASP.NET a Java jsou příchozí požadavky HTTP automaticky zachyceny. Možná budete chtít vytvořit další instance TelemetryClient pro jiný modul aplikace. Například můžete mít jednu instanci TelemetryClient ve vaší třídě middleware pro hlášení událostí obchodní logiky. Můžete nastavit vlastnosti, jako je například UserId a DeviceId, a identifikovat počítač. Tyto informace jsou připojeny ke všem událostem, které instance odesílá.

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

V Node.js projekty můžete použít `new applicationInsights.TelemetryClient(instrumentationKey?)` k vytvoření nové instance, ale toto nastavení se doporučuje jenom pro scénáře, které vyžadují izolovanou konfiguraci z typu Singleton `defaultClient` .

## <a name="trackevent"></a>TrackEvent

V Application Insights *vlastní událost* je datový bod, který lze zobrazit v [Průzkumník metrik](../essentials/metrics-charts.md) jako agregovaný počet a v [diagnostickém vyhledávání](./diagnostic-search.md) jako jednotlivé výskyty. (Nesouvisí s událostmi MVC nebo jinými rozhraními.)

Vložením `TrackEvent` volání do kódu můžete spočítat různé události. Jak často uživatelé vyberou konkrétní funkci, jak často dosahují konkrétního cíle, nebo jak často provádějí konkrétní typy chyb.

Například v herní aplikaci můžete odeslat událost pokaždé, když uživatel hry vyhrává:

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

### <a name="custom-events-in-analytics"></a>Vlastní události v analýzách

Telemetrii je k dispozici v `customEvents` tabulce na [kartě protokoly Application Insights](../logs/log-query-overview.md) nebo v [prostředí používání](usage-overview.md). Události můžou pocházet z `trackEvent(..)` nebo [kliknout na analýza modul plug-in automatické kolekce](javascript-click-analytics-plugin.md).

 

Pokud je [vzorkování](./sampling.md) v provozu, vlastnost vlastnost ItemCount zobrazí hodnotu větší než 1. Například vlastnost ItemCount = = 10 znamená, že u 10 volání trackEvent () proces vzorkování přenáší pouze jeden z nich. Chcete-li získat správný počet vlastních událostí, měli byste proto použít kód jako `customEvents | summarize sum(itemCount)` .

## <a name="getmetric"></a>GetMetric

Chcete-li se dozvědět, jak efektivně použít volání getmetric () k zachycení místně předem agregovaných metrik pro aplikace .NET a .NET Core, přejděte do dokumentace ke službě [getmetric](./get-metric.md) .

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft. ApplicationInsights. TelemetryClient. TrackMetric není upřednostňovanou metodou pro odesílání metrik. Metriky by měly být vždy před odesláním shrnuty v časovém období. K získání objektu metriky pro přístup k předagregačním funkcím sady SDK použijte jedno z přetížení getmetric (..). Pokud implementujete vlastní logiku před agregací, můžete k odeslání výsledných agregací použít metodu TrackMetric (). Pokud vaše aplikace vyžaduje odeslání samostatné položky telemetrie při každé příležitosti bez agregace v čase, budete pravděpodobně mít případ použití pro telemetrii událostí. viz TelemetryClient. TrackEvent (Microsoft. ApplicationInsights. DataContracts. EventTelemetry).

Application Insights mohou metriky grafů, které nejsou připojeny k určitým událostem. Můžete například monitorovat délku fronty v pravidelných intervalech. Díky metrikám jsou jednotlivá měření méně zajímavá než kolísání a trendy, takže statistické grafy jsou užitečné.

Aby bylo možné odesílat metriky do Application Insights, můžete použít `TrackMetric(..)` rozhraní API. Existují dva způsoby, jak odeslat metriku:

* Jedna hodnota. Pokaždé, když ve své aplikaci provedete měření, odešlete odpovídající hodnotu Application Insights. Předpokládejme například, že máte metriku popisující počet položek v kontejneru. Během konkrétního časového období nejprve do kontejneru vložíte tři položky a pak odeberete dvě položky. Proto byste volali `TrackMetric` dvakrát: nejprve předání hodnoty `3` a hodnoty `-2` . Application Insights ukládá vaše jménem obě hodnoty.

* Agregovat. Při práci s metrikami je každé jedno měření málo důležité. Místo toho je důležité mít přehled o tom, co se stalo během konkrétního časového období. Tento souhrn se nazývá _agregace_. Ve výše uvedeném příkladu je agregovaná celková metrika pro toto časové období `1` a počet hodnot metrik `2` . Při použití agregačního přístupu, vyvolejte pouze `TrackMetric` jednou za časové období a odešlete agregované hodnoty. To je doporučený postup, protože může významně snížit náklady a režii na výkon tím, že posílá méně datových bodů do Application Insights a přitom stále shromažďuje všechny relevantní informace.

### <a name="examples"></a>Příklady

#### <a name="single-values"></a>Jednotlivé hodnoty

Odeslání jedné hodnoty metriky:

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

### <a name="custom-metrics-in-analytics"></a>Vlastní metriky v analýzách

Telemetrii je k dispozici v `customMetrics` tabulce v [Application Insights Analytics](../logs/log-query-overview.md). Každý řádek představuje volání `trackMetric(..)` ve vaší aplikaci.

* `valueSum` – Toto je součet měření. Chcete-li získat střední hodnotu, rozdělte `valueCount` .
* `valueCount` – Počet měření, které byly agregovány do tohoto `trackMetric(..)` volání.

## <a name="page-views"></a>Zobrazení stránek

V aplikaci pro zařízení nebo webovou stránku se telemetrie zobrazení stránky ve výchozím nastavení posílá při načtení každé obrazovky nebo stránky. Ale můžete změnit, aby se zobrazení stránek sledovalo v dalších nebo různých časech. Například v aplikaci, která zobrazuje karty nebo okna, může být vhodné sledovat stránku vždy, když uživatel otevře nové okno.

Data uživatelů a relací se odesílají jako vlastnosti spolu se zobrazeními stránek, takže grafy uživatelů a relací budou aktivní, když se zobrazí telemetrie zobrazení stránky.

### <a name="custom-page-views"></a>Vlastní zobrazení stránky

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

Pokud máte v různých stránkách HTML několik karet, můžete zadat i adresu URL:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Zobrazení časování stránky

Ve výchozím nastavení jsou časy hlášené jako **Doba načítání zobrazení stránky** měřeny od okamžiku, kdy prohlížeč požadavek odešle, dokud není volána událost načtení stránky prohlížeče.

Místo toho můžete použít tyto kroky:

* Nastavte explicitní dobu trvání ve volání [trackPageView](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) : `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);` .
* Použijte zobrazení na stránce s časováním volání `startTrackPage` a `stopTrackPage` .

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Název, který použijete jako první parametr, přidruží volání Start a stop. Výchozí hodnota je název aktuální stránky.

Výsledná trvání načtení stránky zobrazená v Průzkumník metrik jsou odvozena z intervalu mezi voláními spuštění a zastavení. To je až na vás, jaký interval ve skutečnosti vydáte.

### <a name="page-telemetry-in-analytics"></a>Telemetrie stránky v analýzách

V [analytických](../logs/log-query-overview.md) dvou tabulkách se zobrazují data z operací prohlížeče:

* `pageViews`Tabulka obsahuje data o adrese URL a názvu stránky.
* `browserTimings`Tabulka obsahuje data o výkonu klienta, například čas potřebný ke zpracování příchozích dat.

Pokud chcete zjistit, jak dlouho bude prohlížeč zpracovávat různé stránky, postupujte takto:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Pro zjištění oblíbených různých prohlížečů:

```kusto
pageViews
| summarize count() by client_Browser
```

Chcete-li přidružit zobrazení stránky voláním AJAX, připojte se k závislostem:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

Sada SDK serveru používá TrackRequest k protokolování požadavků HTTP.

Můžete ji také zavolat sami, pokud chcete simulovat žádosti v kontextu, ve kterém nemáte spuštěný modul webové služby.

Doporučený způsob odeslání telemetrie požadavků je však, že požadavek funguje jako <a href="#operation-context">kontext operace</a>.

## <a name="operation-context"></a>Kontext operace

Můžete sladit položky telemetrie dohromady jejich přidružením k kontextu operace. Standardní modul pro sledování požadavků provádí tyto výjimky a další události, které jsou odeslány během zpracování požadavku HTTP. V části [vyhledávání](./diagnostic-search.md) a [Analýza](../logs/log-query-overview.md)můžete snadno najít jakékoli události přidružené k žádosti pomocí jejího ID operace.

Další informace o korelaci najdete [v tématu korelace telemetrie v Application Insights](./correlation.md) .

Při ručním sledování telemetrie je nejjednodušší způsob, jak zajistit korelace telemetrie pomocí tohoto modelu:

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

Spolu s nastavením kontextu operace `StartOperation` vytvoří položku telemetrie typu, který zadáte. Odesílá položku telemetrie při uvolnění operace, nebo pokud explicitně voláte `StopOperation` . Pokud použijete `RequestTelemetry` jako typ telemetrie, jeho doba trvání je nastavená na časový interval mezi začátkem a zastavením.

Položky telemetrie hlášené v rámci oboru operace se stanou "podřízenými" takové operace. Kontexty operace můžou být vnořené.

V části Hledat se k vytvoření seznamu **souvisejících položek** používá kontext operace:

![Související položky](./media/api-custom-events-metrics/21.png)

Další informace o sledování vlastních operací najdete v tématu [sledování vlastních operací pomocí Application Insights .NET SDK](./custom-operations-tracking.md) .

### <a name="requests-in-analytics"></a>Požadavky v analýzách

V [Application Insights Analytics](../logs/log-query-overview.md)se žádosti zobrazují v `requests` tabulce.

Pokud je [vzorkování](./sampling.md) v provozu, vlastnost vlastnost ItemCount zobrazí hodnotu větší než 1. Například vlastnost ItemCount = = 10 znamená, že u 10 volání trackRequest () proces vzorkování přenáší pouze jeden z nich. Chcete-li získat správný počet požadavků a průměrnou dobu, segmentované podle názvů požadavků, použijte následující kód:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Odeslat výjimky do Application Insights:

* Pokud [je chcete spočítat](../essentials/metrics-charts.md), jako indikaci četnosti problému.
* K [prohlédnutí jednotlivých výskytů](./diagnostic-search.md).

Sestavy zahrnují trasování zásobníku.

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

Sady SDK zachycují mnoho výjimek automaticky, takže nemusíte vždy volat TrackException explicitně.

* ASP.NET: [napište kód pro zachycení výjimek](./asp-net-exceptions.md).
* Java EE: [výjimky se zachycují automaticky](./java-get-started.md#exceptions-and-request-failures).
* JavaScript: výjimky jsou zachyceny automaticky. Pokud chcete zakázat automatické shromažďování, přidejte řádek do fragmentu kódu, který vložíte do webových stránek:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Výjimky v analýzách

V [Application Insights Analytics](../logs/log-query-overview.md)se výjimky zobrazují v `exceptions` tabulce.

Pokud je [vzorkování](./sampling.md) v provozu, `itemCount` vlastnost zobrazuje hodnotu větší než 1. Například vlastnost ItemCount = = 10 znamená, že u 10 volání trackException () proces vzorkování přenáší pouze jeden z nich. Chcete-li získat správný počet výjimek segmenticky podle typu výjimky, použijte kód jako:

```kusto
exceptions
| summarize sum(itemCount) by type
```

Většina důležitých informací o zásobníku se už extrahuje do samostatných proměnných, ale pokud chcete získat další informace, můžete si ji stáhnout ze své `details` struktury. Vzhledem k tomu, že je tato struktura dynamická, je vhodné přetypovat výsledek na očekávaný typ. Například:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

K přidružení výjimek ke svým souvisejícím žádostem použijte spojení:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Pomocí TrackTrace můžete diagnostikovat problémy odesláním "popisu cesty" do Application Insights. Můžete odeslat bloky diagnostických dat a zkontrolovat je v [diagnostickém vyhledávání](./diagnostic-search.md).

V [adaptérech protokolů](./asp-net-trace-logs.md) .NET použijte toto rozhraní API k posílání protokolů třetích stran na portál.

V jazyce Java pro [standardní protokolovací nástroje, jako je Log4J, Logback](./java-trace-logs.md) k odesílání protokolů třetích stran na portál použít Application Insights Log4J nebo Logback.

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

*JavaScript na straně klienta nebo prohlížeče*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Protokoluje diagnostickou událost, jako je například zadání nebo ukončení metody.

 Parametr | Popis
---|---
`message` | Diagnostická data. Může být mnohem delší než název.
`properties` | Mapa řetězce na řetězec: další data sloužící k [filtrování výjimek](#properties) na portálu. Výchozí hodnota je prázdná.
`severityLevel` | Podporované hodnoty: [SeverityLevel. TS](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Můžete hledat obsah zprávy, ale (na rozdíl od hodnot vlastností) nemůžete na něm filtrovat.

Omezení velikosti `message` je mnohem vyšší než omezení vlastností.
Výhodou TrackTrace je, že do zprávy můžete ukládat poměrně dlouhá data. Můžete například zakódovat data POST.  

Kromě toho můžete do zprávy přidat úroveň závažnosti. A podobně jako u jiné telemetrie můžete přidat hodnoty vlastností, které vám pomohou filtrovat nebo vyhledat různé sady trasování. Například:

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

V [hledání](./diagnostic-search.md)můžete snadno odfiltrovat všechny zprávy určité úrovně závažnosti, které se vztahují k určité databázi.

### <a name="traces-in-analytics"></a>Trasování v analýzách

V [Application Insights Analytics](../logs/log-query-overview.md)se v tabulce zobrazí volání TrackTrace `traces` .

Pokud je [vzorkování](./sampling.md) v provozu, vlastnost vlastnost ItemCount zobrazí hodnotu větší než 1. Například vlastnost ItemCount = = 10 znamená, že 10 volání do `trackTrace()` , proces vzorkování přenáší pouze jeden z nich. Chcete-li získat správný počet volání trasování, měli byste použít kód, například `traces | summarize sum(itemCount)` .

## <a name="trackdependency"></a>TrackDependency

Použijte volání TrackDependency ke sledování doby odezvy a míry úspěšnosti volání do externí části kódu. Výsledky se zobrazí v grafech závislostí na portálu. Následující fragment kódu je nutné přidat všude, kde je provedeno volání závislosti.

> [!NOTE]
> Pro .NET a .NET Core můžete alternativně použít `TelemetryClient.StartOperation` metodu (rozšíření), která vyplní `DependencyTelemetry` vlastnosti, které jsou potřeba pro korelaci, a některé další vlastnosti, jako je čas spuštění a doba trvání, takže nemusíte vytvářet vlastní časovač, jako v níže uvedených příkladech. Další informace najdete [v části věnované odchozímu sledování závislostí v](./custom-operations-tracking.md#outgoing-dependencies-tracking)tomto článku.

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
    dependencyTelemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
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

Pamatujte, že serverové sady SDK obsahují [modul závislosti](./asp-net-dependencies.md) , který zjišťuje a sleduje určitá volání závislosti automaticky – například pro databáze a rozhraní REST API. Abyste mohli modul pracovat, musíte na svém serveru nainstalovat agenta. 

V jazyce Java lze určitá volání závislostí automaticky sledovat pomocí [agenta Java](./java-agent.md).

Toto volání použijete, pokud chcete sledovat volání, která automatizované sledování nezachytává, nebo pokud nechcete agenta nainstalovat.

Chcete-li vypnout standardní modul Sledování závislosti v jazyce C#, upravte [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) a odstraňte odkaz na `DependencyCollector.DependencyTrackingTelemetryModule` . V jazyce Java neinstalujte agenta Java, pokud nechcete automaticky shromažďovat standardní závislosti.

### <a name="dependencies-in-analytics"></a>Závislosti v analýzách

V [Application Insights Analytics](../logs/log-query-overview.md)se v tabulce zobrazí volání trackDependency `dependencies` .

Pokud je [vzorkování](./sampling.md) v provozu, vlastnost vlastnost ItemCount zobrazí hodnotu větší než 1. Například vlastnost ItemCount = = 10 znamená, že u 10 volání trackDependency () proces vzorkování přenáší pouze jeden z nich. Chcete-li získat správný počet závislostí segmentované cílovou komponentou, použijte kód jako:

```kusto
dependencies
| summarize sum(itemCount) by target
```

K přidružení závislostí ke svým souvisejícím žádostem použijte spojení:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Vyprazdňování dat

V normálním případě SDK odesílá data v pevných intervalech (obvykle 30 sekund) nebo vždy, když je vyrovnávací paměť plná (obvykle 500 položek). V některých případech ale možná budete chtít vyprázdnit vyrovnávací paměť – například pokud používáte sadu SDK v aplikaci, která se vypne.

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

Funkce je asynchronní pro [kanál telemetrie serveru](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

V ideálním případě by měla být metoda flush () použita v aktivity vypnutí aplikace.

## <a name="authenticated-users"></a>Skupina Authenticated Users

Ve webové aplikaci jsou uživatelé (ve výchozím nastavení) [identifikováni pomocí souborů cookie](./usage-segmentation.md#the-users-sessions-and-events-segmentation-tool). Uživatel se může při přístupu k vaší aplikaci z jiného počítače nebo prohlížeče počítat více než jednou, nebo pokud odstraní soubory cookie.

Pokud se uživatelé přihlásí do vaší aplikace, můžete získat přesnější počet nastavením ID ověřeného uživatele v kódu prohlížeče:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

V aplikaci ASP.NET Web MVC například:

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

Není nutné používat vlastní přihlašovací jméno uživatele. Musí se jednat o ID, které je pro daného uživatele jedinečné. Nesmí obsahovat mezery ani žádné znaky `,;=|` .

ID uživatele je také nastaveno v souboru cookie relace a odesláno na server. Pokud je nainstalovaná sada SDK serveru, ověřený identifikátor uživatele se odešle jako součást vlastností kontextu telemetrie klienta i serveru. Pak ho můžete filtrovat a prohledávat.

Pokud vaše aplikace seskupí uživatele na účty, můžete také předat identifikátor účtu (se stejnými omezeními znaků).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

V [Průzkumník metrik](../essentials/metrics-charts.md)můžete vytvořit graf, který počítá **uživatele, ověřené** a **uživatelské účty**.

Můžete také [Vyhledat](./diagnostic-search.md) body dat klienta s konkrétními uživatelskými jmény a účty.

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Filtrování, vyhledávání a segmentace dat pomocí vlastností

Můžete připojit vlastnosti a měření k událostem (a také k metrikám, zobrazením stránky, výjimkám a dalším datům telemetrie).

*Vlastnosti* jsou řetězcové hodnoty, které můžete použít k filtrování telemetrie v sestavách využití. Pokud například vaše aplikace nabízí několik her, můžete k jednotlivým událostem připojit název hry, abyste viděli, které hry jsou populární.

Velikost řetězce je 8192. (Pokud chcete odesílat velké bloky dat, použijte parametr zprávy TrackTrace.)

*Metriky* jsou číselné hodnoty, které je možné znázornit graficky. Můžete například chtít zjistit, jestli se v hodnoceních, které hráčů dosahuje, postupný nárůst. Grafy je možné rozdělit pomocí vlastností, které se odesílají s událostí, takže můžete získat samostatné nebo skládané grafy pro různé hry.

Aby se hodnoty metriky správně zobrazily, měly by být větší nebo rovny 0.

Existují určitá [omezení počtu vlastností, hodnot vlastností a metrik](#limits) , které můžete použít.

*JavaScript*

```javascript
appInsights.trackEvent({
  name: 'some event',
  properties: { // accepts any type
    prop1: 'string',
    prop2: 123.45,
    prop3: { nested: 'objects are okay too' }
  }
});

appInsights.trackPageView({
  name: 'some page',
  properties: { // accepts any type
    prop1: 'string',
    prop2: 123.45,
    prop3: { nested: 'objects are okay too' }
  }
});
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
> Je třeba dbát na to, abyste do vlastností nehlásili osobně identifikovatelné osobní údaje.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternativní způsob, jak nastavit vlastnosti a metriky

Pokud je to pohodlnější, můžete shromáždit parametry události v samostatném objektu:

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
> Nepoužívejte znovu stejnou instanci položky telemetrie ( `event` v tomto příkladu), aby bylo volání metody Track * () několikrát voláno. To může způsobit odeslání telemetrie s nesprávnou konfigurací.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Vlastní měření a vlastnosti v analýzách

V rámci [analýzy](../logs/log-query-overview.md)se vlastní metriky a vlastnosti zobrazují v `customMeasurements` `customDimensions` atributech a každého záznamu telemetrie.

Pokud jste například přidali vlastnost s názvem "Game" do telemetrie žádosti, tento dotaz počítá výskyty různých hodnot "Game" a zobrazí průměr vlastní metriky "skóre":

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Všimněte si, že:

* Když extrahujete hodnotu z formátu JSON customDimensions nebo customMeasurements, má dynamický typ, takže je nutné ji přetypovat `tostring` nebo `todouble` .
* K zohlednění možnosti [vzorkování](./sampling.md)byste měli použít `sum(itemCount)` , ne `count()` .

## <a name="timing-events"></a><a name="timed"></a> Události časování

V některých případech je třeba, aby bylo možné graf, jak dlouho trvá provedení akce. Můžete například chtít zjistit, jak dlouho uživatelé berou v úvahu volby ve hře. Pro tuto možnost lze použít parametr měření.

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

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Výchozí vlastnosti vlastní telemetrie

Pokud chcete nastavit výchozí hodnoty vlastností pro některé vlastní události, které zapíšete, můžete je nastavit v instanci TelemetryClient. Jsou připojeny ke každé položce telemetrie, která je odeslána z tohoto klienta.

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

Jednotlivá volání telemetrie můžou v jejich slovníkech vlastností přepsat výchozí hodnoty.

*Pro webové klienty v jazyce JavaScript* použijte Inicializátory telemetrie JavaScript.

*Chcete-li přidat vlastnosti do všechny telemetrie*, včetně dat ze standardních modulů kolekcí [, `ITelemetryInitializer` implementujte](./api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Vzorkování, filtrování a zpracování telemetrie

Můžete napsat kód pro zpracování telemetrie před jejich odesláním ze sady SDK. Zpracování zahrnuje data odesílaná ze standardních modulů telemetrie, jako je kolekce žádostí HTTP a kolekce závislostí.

[Přidání vlastností](./api-filtering-sampling.md#add-properties) do telemetrie implementací `ITelemetryInitializer` . Můžete například přidat čísla verzí nebo hodnoty, které se počítají z jiných vlastností.

[Filtrování](./api-filtering-sampling.md#filtering) může před odesláním ze sady SDK změnit nebo zahodit telemetrii implementací `ITelemetryProcessor` . Můžete řídit, co se odesílá nebo zahodí, ale budete mít k dispozici vliv na vaše metriky. V závislosti na tom, jak položky zahodíte, může být ztracena možnost navigace mezi souvisejícími položkami.

[Vzorkování](./api-filtering-sampling.md) je zabalené řešení, které snižuje objem dat odesílaných z vaší aplikace na portál. V takovém případě nemá vliv na zobrazené metriky. A to i bez ovlivnění vaší schopnosti diagnostikovat problémy pomocí navigace mezi souvisejícími položkami, jako jsou výjimky, požadavky a zobrazení stránek.

[Další informace](./api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Zakázání telemetrie

Chcete-li *dynamicky zastavit a spustit* shromažďování a přenos telemetrie:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Chcete-li *zakázat vybrané standardní kolekce*, například čítače výkonu, požadavky HTTP nebo závislosti, odstraňte nebo Odkomentujte příslušné řádky v [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). To můžete udělat například v případě, že chcete odesílat vlastní TrackRequest data.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Chcete-li *zakázat vybrané standardní kolekce*, například čítače výkonu, požadavky HTTP nebo závislosti – v čase inicializace, řetězení metod konfigurace do inicializačního kódu sady SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Chcete-li zakázat tyto sběrače po inicializaci, použijte objekt konfigurace: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Vývojářský režim

Během ladění je vhodné, aby vaše telemetrie byly prostřednictvím kanálu urychleny, takže výsledky uvidíte okamžitě. Získáte také další zprávy, které vám pomohou trasovat případné problémy s telemetrie. Přepněte ho v produkčním prostředí, protože může zpomalit vaši aplikaci.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

V případě Node.js můžete povolit režim pro vývojáře povolením interního protokolování prostřednictvím `setInternalLogging` a nastavením `maxBatchSize` na 0, což způsobí, že se vaše telemetrie pošle hned po shromáždění.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a> Nastavení klíče instrumentace pro vybranou vlastní telemetrii

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Dynamický klíč instrumentace

Abyste se vyhnuli smíchání telemetrie od vývojových, testovacích a produkčních prostředí, můžete [vytvořit samostatné prostředky Application Insights](./create-new-resource.md) a změnit jejich klíče v závislosti na prostředí.

Místo získání klíče instrumentace z konfiguračního souboru ho můžete nastavit ve svém kódu. Nastavte klíč v inicializační metodě, jako je například global.aspx.cs ve službě ASP.NET:

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

Na webových stránkách můžete chtít nastavit ho ze stavu webového serveru, ale nemusíte ho zakódovat do skriptu. Například na webové stránce vygenerované v aplikaci ASP.NET:

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

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient má kontextovou vlastnost, která obsahuje hodnoty, které jsou odesílány společně se všemi daty telemetrie. Obvykle jsou nastavené standardními moduly telemetrie, ale můžete je také nastavit sami. Například:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Pokud jste některou z těchto hodnot nastavili sami, zvažte odebrání relevantního řádku z [ApplicationInsights.config](./configuration-with-applicationinsights-config.md), aby se vaše hodnoty a standardní hodnoty nemusely zaměňovat.

* **Součást**: aplikace a její verze.
* **Zařízení**: data o zařízení, ve kterém je aplikace spuštěná. (Ve webových aplikacích se jedná o server nebo klientské zařízení, ze kterého se telemetrie odesílá.)
* **InstrumentationKey**: prostředek Application Insights v Azure, kde se objeví telemetrie. Obvykle je převzata z ApplicationInsights.config.
* **Umístění**: geografické umístění zařízení.
* **Operace**: ve službě Web Apps aktuální požadavek HTTP. V ostatních typech aplikací můžete nastavit, aby se události seskupují společně.
  * **ID**: generovaná hodnota, která koreluje různé události, takže při kontrole libovolné události v diagnostickém vyhledávání můžete najít související položky.
  * **Název**: identifikátor, obvykle adresa URL požadavku HTTP.
  * **SyntheticSource**: Pokud není null nebo prázdné, řetězec, který označuje, že zdroj žádosti byl identifikován jako robot nebo webový test. Ve výchozím nastavení je vyloučen z výpočtů v Průzkumník metrik.
* **Vlastnosti**: vlastnosti, které jsou odesílány se všemi daty telemetrie. Dá se přepsat v individuálním záznamu * volání.
* **Relace**: relace uživatele. ID je nastavené na generovanou hodnotu, která se změní, když uživatel nějaký čas ještě není aktivní.
* **Uživatel**: informace o uživateli.

## <a name="limits"></a>Omezení

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Aby nedošlo k překročení limitu přenosové rychlosti, použijte [vzorkování](./sampling.md).

Informace o tom, jak dlouho se data uchovávají, najdete v tématu [uchovávání a ochrana osobních údajů](./data-retention-privacy.md).

## <a name="reference-docs"></a>Referenční dokumenty

* [Odkaz na ASP.NET](/dotnet/api/overview/azure/insights)
* [Referenční dokumentace jazyka Java](/java/api/overview/azure/appinsights)
* [Reference jazyka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## <a name="sdk-code"></a>Kód sady SDK

* [Sada ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Balíčky Windows serveru](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="questions"></a>Dotazy

* *Jaké výjimky mohou Track_ () volání throw?*

    Žádné Nemusíte je zabalit do klauzulí try-catch. Pokud sada SDK zjistí problémy, protokoluje zprávy ve výstupu konzoly ladění a--pokud se zprávy objeví v diagnostickém vyhledávání.
* *Existuje REST API získat data z portálu?*

    Ano, [rozhraní API pro přístup k datům](https://dev.applicationinsights.io/). Mezi další způsoby, jak extrahovat data, patří [Export z analýzy do Power BI](./export-power-bi.md) a [průběžný export](./export-telemetry.md).

## <a name="next-steps"></a><a name="next"></a>Další kroky

* [Hledat události a protokoly](./diagnostic-search.md)
* [Řešení potíží](../faq.md)
