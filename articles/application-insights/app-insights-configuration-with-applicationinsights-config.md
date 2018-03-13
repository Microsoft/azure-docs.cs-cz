---
title: Odkaz na soubor ApplicationInsights.config - Azure | Microsoft Docs
description: "Povolte nebo zakažte moduly shromažďování dat a přidejte čítače výkonu a dalších parametrů."
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
editor: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: a35da5c84e4e79d7bc6f2167ec7e172970992612
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurace sady Application Insights SDK pomocí souboru ApplicationInsights.config nebo .xml
Application Insights .NET SDK se skládá z počet balíčků NuGet. [Základní balíček](http://www.nuget.org/packages/Microsoft.ApplicationInsights) poskytuje rozhraní API pro odesílání telemetrie Application insights. [Další balíčky](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) poskytují telemetrie *moduly* a *inicializátory* pro automaticky sledování telemetrie z vaší aplikace a jeho kontextu. Úpravou konfiguračního souboru, můžete povolit nebo zakázat telemetrii moduly a inicializátory a nastavit parametry pro některé z nich.

Konfigurační soubor je s názvem `ApplicationInsights.config` nebo `ApplicationInsights.xml`, v závislosti na typu aplikace. Je automaticky přidán do projektu když jste [nainstalujte většina verze sady SDK][start]. Je také přidán do webové aplikace pomocí [monitorování stavu na serveru se službou IIS][redfield], nebo když vyberete Application Insights [rozšíření pro webové stránky Azure, nebo virtuální počítač](app-insights-azure-web-apps.md).

Není k dispozici soubor ekvivalentní k řízení [SDK na webové stránce][client].

Tento dokument popisuje oddílů, které se zobrazí v konfiguraci souboru, jak budou řídit komponenty sady SDK, a které balíčky NuGet načíst těchto součástí.

> [!NOTE]
> Pokyny k souboru ApplicationInsights.config a .xml se nevztahují na .NET Core SDK. Změny k aplikaci .NET Core používáme obvykle soubor appSettings.JSON určený. Příklady najdete v [dokumentace snímku ladicí program.](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)

## <a name="telemetry-modules-aspnet"></a>Telemetrie moduly (ASP.NET)
Každý modul telemetrie shromažďuje konkrétní typ dat a používá základní rozhraní API odeslat data. Moduly jsou nainstalovány jiné balíčky NuGet, které také přidat do souboru .config požadovaných řádků.

V souboru konfigurace pro každý modul není uzlu. Zakázat modul, odstraňte uzlu nebo ho nastavte jako komentář.

### <a name="dependency-tracking"></a>Sledování závislostí
[Závislost sledování](app-insights-asp-net-dependencies.md) shromažďuje telemetrická data o volání aplikace provede databáze a externí služby a databáze. Chcete-li povolit tento modul pro práci v serveru se službou IIS, je potřeba [nainstalujte monitorování stavu][redfield]. Pro použití ve službě Azure web apps nebo virtuálních počítačů, [vyberte rozšíření Application Insights](app-insights-azure-web-apps.md).

Můžete taky napsat vlastní závislost sledování kódu pomocí [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) balíček NuGet.

### <a name="performance-collector"></a>Kolekce výkonu
[Shromažďuje čítače výkonu systému](app-insights-performance-counters.md) například CPU, paměť a síť načíst z instalace služby IIS. Můžete zadat čítače, které mají shromažďovat, včetně čítačů výkonu, které jste nastavili sami.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) balíček NuGet.

### <a name="application-insights-diagnostics-telemetry"></a>Telemetrii Diagnostics Application Insights
`DiagnosticsTelemetryModule` Sestavy chyb v samotný kód instrumentace Application Insights. Například pokud kód nelze získat přístup k čítače výkonu nebo `ITelemetryInitializer` vyvolá výjimku. Trasování telemetrie sleduje tento modul se zobrazí v [diagnostické vyhledávání][diagnostic]. Sends diagnostic data to dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) balíček NuGet. Pokud máte pouze instalaci tohoto balíčku, není soubor ApplicationInsights.config vytvoří automaticky.

### <a name="developer-mode"></a>Režim vývojáře
`DeveloperModeWithDebuggerAttachedTelemetryModule` Vynutí Application Insights `TelemetryChannel` k odesílání dat okamžitě, jeden telemetrie položky v době, kdy je připojen ladicí program na proces aplikace. Tím se zkracuje dobu mezi odhalením Pokud vaše aplikace sleduje telemetrie a pokud se zobrazí na portálu služby Application Insights. Způsobuje významné režijní náklady v procesoru a šířku pásma sítě.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights systému Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) balíček NuGet

### <a name="web-request-tracking"></a>Sledování webové žádosti
Sestavy [čas a výsledek kód odpovědi](app-insights-asp-net.md) požadavků HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) balíček NuGet

### <a name="exception-tracking"></a>Sledování výjimek
`ExceptionTrackingTelemetryModule` sleduje neošetřených výjimek ve vaší webové aplikaci. V tématu [chyby a výjimky][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) balíček NuGet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -sleduje [nepozorovaná úloh výjimky](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` -sleduje neošetřené výjimky pro role pracovního procesu, služby systému windows a konzolové aplikace.
* [Application Insights systému Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) balíček NuGet.

### <a name="eventsource-tracking"></a>EventSource sledování
`EventSourceTelemetryModule` Umožňuje nakonfigurovat událostí EventSource k odeslání do Application Insights jako trasování. Informace o sledování událostí EventSource najdete v tématu [pomocí událostí EventSource](app-insights-asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Sledování událostí trasování událostí pro Windows
`EtwCollectorTelemetryModule` Umožňuje nakonfigurovat události od zprostředkovatelů trasování událostí pro Windows k odeslání do Application Insights jako trasování. Informace o sledování události trasování událostí pro Windows najdete v tématu [události trasování událostí pro Windows pomocí](app-insights-asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Balíček Microsoft.ApplicationInsights poskytuje [základní rozhraní API](https://msdn.microsoft.com/library/mt420197.aspx) sady SDK. Další moduly telemetrie použít, a můžete také [použijte jej k definování vlastní telemetrii](app-insights-api-custom-events-metrics.md).

* Žádný záznam v souboru ApplicationInsights.config.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) balíček NuGet. Pokud instalujete právě tento NuGet, vygeneruje se žádný soubor .config.

## <a name="telemetry-channel"></a>Kanál telemetrie
Kanál telemetrie spravuje ukládání do vyrovnávací paměti a přenos telemetrie do služby Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` je výchozím kanálu pro služby. Data ukládány v paměti.
* `Microsoft.ApplicationInsights.PersistenceChannel` představuje alternativu pro konzolové aplikace. Žádná unflushed data ho můžete uložit do trvalého úložiště, když vaše aplikace zavře a odešle ho při spuštění aplikace znovu.

## <a name="telemetry-initializers-aspnet"></a>Inicializátory telemetrie (ASP.NET)
Inicializátory telemetrie nastavit kontext vlastnosti, které se odesílají společně s každou položkou telemetrie.

Můžete [zápisu vlastní inicializátory](app-insights-api-filtering-sampling.md#add-properties) pro nastavení vlastností kontextu.

Standardní inicializátory jsou nastavené buď webové nebo Windows Server NuGet balíčků:

* `AccountIdTelemetryInitializer` Nastaví vlastnost ID účtu.
* `AuthenticatedUserIdTelemetryInitializer` Nastaví vlastnost AuthenticatedUserId jako sada SDK jazyka JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` aktualizace `RoleName` a `RoleInstance` vlastnosti `Device` kontext pro všechny položky telemetrie s informacemi, které jsou extrahovány z Azure běhové prostředí.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aktualizace `Version` vlastnost `Component` kontext pro všechny položky telemetrie s hodnotou z extrahovat `BuildInfo.config` souboru vytvořeného pomocí MS Build.
* `ClientIpHeaderTelemetryInitializer` aktualizace `Ip` vlastnost `Location` na základě kontextu všechny položky telemetrii `X-Forwarded-For` hlavičky protokolu HTTP žádosti.
* `DeviceTelemetryInitializer` aktualizuje následující vlastnosti `Device` kontext pro všechny položky telemetrie.
  * `Type` je nastavena na "Počítač"
  * `Id` je nastavena na název domény počítače, kde je spuštěna webová aplikace.
  * `OemName` je nastaven na hodnotu z extrahovat `Win32_ComputerSystem.Manufacturer` pole pomocí služby WMI.
  * `Model` je nastaven na hodnotu z extrahovat `Win32_ComputerSystem.Model` pole pomocí služby WMI.
  * `NetworkType` je nastaven na hodnotu z extrahovat `NetworkInterface`.
  * `Language` je nastavena na název `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` aktualizace `RoleInstance` vlastnost `Device` kontext pro všechny položky telemetrie s názvem domény počítače, kde je spuštěna webová aplikace.
* `OperationNameTelemetryInitializer` aktualizace `Name` vlastnost `RequestTelemetry` a `Name` vlastnost `Operation` kontextu všechny položky telemetrie podle metoda HTTP, jakož i názvy ASP.NET MVC jsou řadič MVC a akce vyvolaná při zpracování požadavku.
* `OperationIdTelemetryInitializer` nebo `OperationCorrelationTelemetryInitializer` aktualizace `Operation.Id` vlastností kontextu všechny položky telemetrie sledovat při zpracování žádosti o se automaticky generované `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` aktualizace `Id` vlastnost `Session` kontext pro všechny položky telemetrie s hodnotou z extrahovat `ai_session` generované kód ApplicationInsights JavaScript instrumentace spouštění v prohlížeči uživatele soubor cookie.
* `SyntheticTelemetryInitializer` nebo `SyntheticUserAgentTelemetryInitializer` aktualizace `User`, `Session` a `Operation` kontexty vlastnosti všech položek telemetrie sledovat při zpracování požadavku z syntetické zdroje, například dostupnosti testů nebo vyhledávání modul robota. Ve výchozím nastavení [Průzkumníku metrik](app-insights-metrics-explorer.md) nezobrazí syntetické telemetrie.

    `<Filters>` Nastavte výchozí určující vlastnosti žádosti.
* `UserTelemetryInitializer` aktualizace `Id` a `AcquisitionDate` vlastnosti `User` kontext pro všechny položky telemetrie s hodnotami z extrahovat `ai_user` souboru cookie generované kód instrumentace Application Insights JavaScript, který je spuštěný v uživatele prohlížeč.
* `WebTestTelemetryInitializer` Nastaví id uživatele, id relace a vlastnosti syntetické zdroje pro požadavky HTTP, která pocházejí z [testy dostupnosti](app-insights-monitor-web-app-availability.md).
  `<Filters>` Nastavte výchozí určující vlastnosti žádosti.

Pro aplikace .NET běžící v Service Fabric, můžete zahrnout `Microsoft.ApplicationInsights.ServiceFabric` balíček NuGet. Tento balíček obsahuje `FabricTelemetryInitializer`, přidává vlastnosti Service Fabric položkám telemetrie. Další informace najdete v tématu [GitHub stránce](https://go.microsoft.com/fwlink/?linkid=848457) o vlastnostech přidal tento balíček NuGet.

## <a name="telemetry-processors-aspnet"></a>Telemetrie procesorů (ASP.NET)
Telemetrie procesory můžete filtrovat a upravit každou položku telemetrie těsně před odesláním ze sady SDK k portálu.

Můžete [psát vlastní telemetrii procesory](app-insights-api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Procesor telemetrie adaptivního vzorkování (od 2.0.0-beta3)
Tato možnost je ve výchozím nastavení zapnutá. Pokud vaše aplikace odešle velké množství telemetrická data, některé jeho tohoto procesoru odebere.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametr poskytuje cíl, který algoritmus se pokusí dosáhnout. Každá instance sady SDK funguje nezávisle, takže pokud je server clusteru s podporou několik počítačů, skutečný objem telemetrie se násobí odpovídajícím způsobem.

[Další informace o vzorkování](app-insights-sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Procesor-míry vzorkování telemetrie (od 2.0.0-beta1)
Je také standardní [vzorkování procesoru telemetrie](app-insights-api-filtering-sampling.md) (z 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parametry kanálu (Java)
Tyto parametry vliv na způsob sady Java SDK měli uložit a vyprázdnit telemetrická data, která shromažďuje.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Počet položek telemetrie, které mohou být uloženy v úložišti sady SDK v paměti. Při dosažení tohoto počtu vyprázdní vyrovnávací paměť telemetrie – to znamená, položky telemetrie posílají se na serveru Application Insights.

* Min: 1
* Max: 1000
* Výchozí: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
Určuje, jak často mají být data, která je uložená v úložišti v paměti vyprázdněny (odeslané do služby Application Insights).

* Min: 1
* Max: 300
* Výchozí: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
Určuje maximální velikost v MB, která je vymezena pro trvalé úložiště na místním disku. Toto úložiště se používá pro zachování telemetrie položky, které se nepodařilo přenést do koncového bodu služby Application Insights. Pokud velikost úložiště byly splněny, nové položky telemetrie se zahodí.

* Min: 1
* Max: 100
* Výchozí: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey
Určuje prostředek Application Insights, ve kterém se zobrazí data. Obvykle vytvoříte prostředek samostatné samostatné klíčem pro každý z vašich aplikací.

Pokud chcete nastavit klíč dynamicky – například pokud chcete odeslat výsledky z vaší aplikace do různých prostředků – můžete vynechat klíč z konfiguračního souboru a nastavení v kódu.

Klíč pro všechny instance TelemetryClient, včetně standardní telemetrie moduly nastavit klíč v TelemetryConfiguration.Active. V metodě inicializace, jako jsou například souboru global.aspx.cs v službě ASP.NET, postupujte takto:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Pokud chcete odeslat sadu událostí na jiný prostředek, můžete nastavit klíč pro konkrétní TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Chcete-li získat nový klíč, [vytvoření nového prostředku na portálu služby Application Insights][new].

## <a name="next-steps"></a>Další postup
[Další informace o rozhraní API][api].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
