---
title: Odkaz na soubor ApplicationInsights.config – Azure | Dokumentace Microsoftu
description: Povolte nebo zakažte modulů kolekce dat a přidejte čítače výkonu a další parametry.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: d17b1b754afc5067a885025dba83cd0fba2370d5
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214568"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurace sady Application Insights SDK pomocí souboru ApplicationInsights.config nebo .xml
Application Insights .NET SDK se skládá z počtu balíčků NuGet. [Balíčku core](https://www.nuget.org/packages/Microsoft.ApplicationInsights) poskytuje rozhraní API pro odesílání telemetrických dat ze služby Application Insights. [Další balíčky](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) poskytují telemetrie *moduly* a *inicializátory* pro automatické sledování telemetrie z vaší aplikace a jeho kontextu. Úpravou konfiguračního souboru, můžete povolit nebo zakázat inicializátory a moduly telemetrie a nastavit parametry pro některé z nich.

Konfigurační soubor má název `ApplicationInsights.config` nebo `ApplicationInsights.xml`, v závislosti na typu aplikace. Je automaticky přidán do projektu při vám [nainstalovat většině verzí sady SDK][start]. Je taky přidaný do webové aplikace pomocí [monitorování stavu na serveru služby IIS][redfield], nebo když vyberete Application Insights [rozšíření webu Azure nebo na virtuálním počítači](azure-web-apps.md).

Není k dispozici odpovídající soubor do ovládacího prvku [SDK na webové stránce][client].

Tento dokument popisuje oddílů, které se zobrazí v konfiguraci souboru, jak jsou součástí sady SDK, řídit a které balíčky NuGet načítání těchto komponent.

> [!NOTE]
> Pokyny k souboru ApplicationInsights.config nebo .xml se nevztahují na .NET Core SDK. Změny v aplikaci .NET Core obvykle používáme soubor appsettings.json. Příklad najdete v [dokumentaci Snapshot Debugger.](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)

## <a name="telemetry-modules-aspnet"></a>Moduly telemetrická data (ASP.NET)
Každý modul telemetrie určitém typu dat shromažďuje a používá základního rozhraní API odesílat data. Moduly jsou nainstalovány jiné balíčky NuGet, které také přidat požadované řádky do souboru .config.

V konfiguračním souboru pro každý modul je uzel. Zakázat modul, odstranit uzel nebo ji komentář.

### <a name="dependency-tracking"></a>Sledování závislostí
[Sledování závislostí](../../azure-monitor/app/asp-net-dependencies.md) shromažďuje telemetrii o voláních vaší aplikace díky k databázím a externí služby a databáze. Chcete-li povolit tento modul pro práci na serveru služby IIS, je potřeba [nainstalujte monitorování stavu][redfield]. Jeho použití v Azure web apps nebo virtuální počítače, [vyberte rozšíření Application Insights](azure-web-apps.md).

Můžete také napsat vlastní sledování kódu pomocí závislostí [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) balíček NuGet.

### <a name="performance-collector"></a>Výkon kolektoru
[Shromažďuje čítače výkonu systému](../../azure-monitor/app/performance-counters.md) , jako například procesoru, paměti a sítě z instalace služby IIS. Můžete zadat které čítače, které chcete shromažďovat, včetně čítačů výkonu, které jste vytvořili sami.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) balíček NuGet.

### <a name="application-insights-diagnostics-telemetry"></a>Diagnostika Telemetrie Application Insights
`DiagnosticsTelemetryModule` Hlásí chyby v samotný kód instrumentace Application Insights. Například kód nemůže získat přístup k čítače výkonu, nebo pokud `ITelemetryInitializer` vyvolá výjimku. Telemetrie trasování sledován pomocí funkce tento modul se zobrazí ve [diagnostické vyhledávání][diagnostic]. Odešle dc.services.vsallin.net diagnostická data.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) balíček NuGet. Pokud nainstalujete jenom tento balíček, soubor ApplicationInsights.config se nevytvoří automaticky.

### <a name="developer-mode"></a>Vývojářský režim
`DeveloperModeWithDebuggerAttachedTelemetryModule` Vynutí Application Insights `TelemetryChannel` k odesílání dat okamžitě, jeden telemetrie položky v době, když je připojen ladicí program k procesu aplikace. To snižuje množství času mezi okamžikem, když vaše aplikace sleduje telemetrická data, a když se objeví na portálu služby Application Insights. To způsobí, že významné režijní náklady v procesoru a šířku pásma sítě.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) balíček NuGet

### <a name="web-request-tracking"></a>Sledování webové žádosti
Sestavy [čas a výsledek kód odpovědi](../../azure-monitor/app/asp-net.md) požadavků protokolu HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) balíček NuGet

### <a name="exception-tracking"></a>Sledování výjimek
`ExceptionTrackingTelemetryModule` sleduje neošetřených výjimek ve vaší webové aplikace. Zobrazit [chyby a výjimky][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) balíček NuGet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -sleduje [nepozorované výjimky úkolu](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` -sleduje neošetřené výjimky pro pracovní role, služby systému windows a konzolových aplikací.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) balíček NuGet.

### <a name="eventsource-tracking"></a>Sledování EventSource
`EventSourceTelemetryModule` Umožňuje nakonfigurovat událostí EventSource k odeslání do Application Insights jako trasování. Informace o sledování událostí EventSource, naleznete v tématu [pomocí událostí EventSource](../../azure-monitor/app/asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Sledování událostí trasování událostí pro Windows
`EtwCollectorTelemetryModule` Umožňuje nakonfigurovat událostí ze zprostředkovatele trasování událostí pro Windows k odeslání do Application Insights jako trasování. Informace o sledování událostí trasování událostí pro Windows najdete v tématu [události trasování událostí pro Windows pomocí](../../azure-monitor/app/asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Balíček Microsoft.ApplicationInsights poskytuje [základní rozhraní API](https://msdn.microsoft.com/library/mt420197.aspx) sady SDK. Další telemetrická data moduly se řídí tím, a můžete je také [použijte k definování vlastní telemetrii](../../azure-monitor/app/api-custom-events-metrics.md).

* Žádné položky v souboru ApplicationInsights.config.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) balíček NuGet. Pokud právě instalaci tohoto balíčku NuGet, vygeneruje se žádný soubor .config.

## <a name="telemetry-channel"></a>Kanál telemetrie
Kanál telemetrie slouží ke správě ukládání do vyrovnávací paměti a přenos telemetrických dat do služby Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` je výchozím kanálu pro služby. Ukládány data v paměti.
* `Microsoft.ApplicationInsights.PersistenceChannel` představuje alternativu pro konzolové aplikace. Když vaše aplikace se zavře a odešle ho při spuštění aplikace znovu ho můžete uložit všechna unflushed data do trvalého úložiště.

## <a name="telemetry-initializers-aspnet"></a>Inicializátory telemetrická data (ASP.NET)
Inicializátory telemetrie nastavit vlastnosti kontextu, které jsou odeslány společně s každou položkou telemetrie.

Je možné [napsat vlastní inicializátory](../../azure-monitor/app/api-filtering-sampling.md#add-properties) můžete nastavit vlastnosti kontextu.

Standardní inicializátory vše je nastaveno buď pomocí webové nebo WindowsServer NuGet balíčků:

* `AccountIdTelemetryInitializer` Nastaví vlastnost ID účtu.
* `AuthenticatedUserIdTelemetryInitializer` Nastaví vlastnost AuthenticatedUserId jako sada JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer` aktualizace `RoleName` a `RoleInstance` vlastnosti `Device` kontext pro všechny položky telemetrie s informacemi o extrahují z prostředí modul runtime služby Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aktualizace `Version` vlastnost `Component` kontext pro všechny položky telemetrie s hodnotou extrahují z `BuildInfo.config` vytvořen soubor pomocí MS Build.
* `ClientIpHeaderTelemetryInitializer` aktualizace `Ip` vlastnost `Location` na základě kontextu všech položkách telemetrie `X-Forwarded-For` hlavičky protokolu HTTP žádosti.
* `DeviceTelemetryInitializer` aktualizuje následující vlastnosti `Device` kontext pro všechny položky telemetrie.
  * `Type` je nastavena na "PC"
  * `Id` je nastavena na název domény počítače, ve kterém je spuštěna webová aplikace.
  * `OemName` je nastavena na hodnotu extrahují z `Win32_ComputerSystem.Manufacturer` pole pomocí rozhraní WMI.
  * `Model` je nastavena na hodnotu extrahují z `Win32_ComputerSystem.Model` pole pomocí rozhraní WMI.
  * `NetworkType` je nastavena na hodnotu extrahují z `NetworkInterface`.
  * `Language` je nastavena na název `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` aktualizace `RoleInstance` vlastnost `Device` kontext pro všechny položky telemetrie s názvem domény počítače, ve kterém je spuštěna webová aplikace.
* `OperationNameTelemetryInitializer` aktualizace `Name` vlastnost `RequestTelemetry` a `Name` vlastnost `Operation` kontextu všech položkách telemetrie podle metody HTTP, jakož i názvy kontroler ASP.NET MVC a akce vyvolat a zpracovat požadavek.
* `OperationIdTelemetryInitializer` nebo `OperationCorrelationTelemetryInitializer` aktualizace `Operation.Id` kontextu vlastnosti všech položkách telemetrie sledovat při zpracovávání žádosti pomocí automaticky generovaného `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` aktualizace `Id` vlastnost `Session` kontext pro všechny položky telemetrie s hodnotou extrahují z `ai_session` vygenerovaný kód instrumentace ApplicationInsights JavaScript spuštěný v prohlížeči uživatele soubor cookie.
* `SyntheticTelemetryInitializer` nebo `SyntheticUserAgentTelemetryInitializer` aktualizace `User`, `Session`, a `Operation` kontexty vlastnosti všech položkách telemetrie sledovat při zpracovávání konkrétní žádosti ze syntetického zdroje, jako například testování dostupnosti nebo hledání modulu bot. Ve výchozím nastavení [Průzkumníka metrik](../../azure-monitor/app/metrics-explorer.md) nezobrazuje syntetické telemetrie.

    `<Filters>` Nastavte výchozí určující vlastnosti žádosti.
* `UserTelemetryInitializer` aktualizace `Id` a `AcquisitionDate` vlastnosti `User` kontext pro všechny položky telemetrie se extrahují z hodnoty `ai_user` vygenerovaný kód instrumentace Application Insights JavaScript spuštěný v uživatele soubor cookie prohlížeč.
* `WebTestTelemetryInitializer` Nastaví id uživatele, id relace a vlastnosti syntetické zdroje pro požadavky HTTP, která pocházejí z [testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md).
  `<Filters>` Nastavte výchozí určující vlastnosti žádosti.

Pro aplikace .NET spuštěné v Service Fabric, můžete zahrnout `Microsoft.ApplicationInsights.ServiceFabric` balíček NuGet. Tento balíček obsahuje `FabricTelemetryInitializer`, který přidá vlastnosti Service Fabric do položky telemetrie. Další informace najdete v tématu [stránku Githubu](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) o vlastnostech přidal tento balíček NuGet.

## <a name="telemetry-processors-aspnet"></a>Telemetrie procesory (ASP.NET)
Procesory telemetrická data můžete filtrovat a upravit každou položku telemetrie těsně před odesláním ze sady SDK k portálu.

Je možné [psát vlastní telemetrii procesory](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Adaptivní vzorkování procesoru telemetrická data (z 2.0.0-beta3)
Tato možnost je ve výchozím nastavení zapnutá. Pokud vaše aplikace odešle velké množství telemetrie, odstraní tento procesor některé.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametr obsahuje cíl, který algoritmus se snaží dosáhnout. Každou instanci sady SDK funguje nezávisle na sobě, takže pokud je server clusteru několik počítačů, skutečný objem telemetrických dat bude vynásobené odpovídajícím způsobem.

[Další informace o vzorkování](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Procesor-frekvence vzorkování telemetrie (od 2.0.0-beta1)
K dispozici je také standardní [vzorkování procesoru telemetrie](../../azure-monitor/app/api-filtering-sampling.md) (z 2.0.1):

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
Tyto parametry mají vliv, jak ukládat a vyprázdnění, který shromažďuje telemetrická data sady Java SDK.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Počet položek telemetrie, které mohou být uloženy v úložišti sady SDK v paměti. Při dosažení tohoto počtu vyprázdní vyrovnávací paměť telemetrická data – to znamená, položky telemetrie se odesílají do Application Insights serveru.

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
Určuje, jak často by měla být data, která jsou uložena v úložišti v paměti vyprázdní (odeslané do služby Application Insights).

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
Určuje maximální velikost v Megabajtech, která je vymezena do trvalého úložiště na místním disku. Toto úložiště se používá pro uchování položky telemetrie, které se předávají do koncového bodu Application Insights. Pokud byly splněny velikost úložiště, nové položky telemetrie se zahodí.

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

#### <a name="local-forwarder"></a>Místní předávání

[Místní server pro předávání](opencensus-local-forwarder.md) je agenta, který shromažďuje Application Insights nebo [OpenCensus](https://opencensus.io/) telemetrická data z různých sad SDK a architektur a směruje je do služby Application Insights. Je schopný běžet pod Windows a Linux. Když pomocí Application Insights Java SDK s velkou provázaností místní předávání poskytuje plnou podporu pro [Live Metrics](../../azure-monitor/app/live-stream.md) a adaptivní vzorkování.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

Pokud používáte SpringBoot starter, přidejte následující konfigurační soubor (souboru application.properties):

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Výchozí hodnoty jsou stejné pro konfiguraci SpringBoot souboru application.properties a soubor applicationinsights.xml.

## <a name="instrumentationkey"></a>InstrumentationKey
Určuje, ve kterém se zobrazí data prostředku Application Insights. Obvykle vytvoříte samostatný prostředek s samostatný klíč pro každý z vašich aplikací.

Pokud chcete nastavit klíč dynamicky – například pokud chcete odeslat výsledky z vaší aplikace pro různé prostředky – můžete vynechat klíč z konfiguračního souboru a nastavit v kódu.

Chcete-li nastavit klíč pro všemi instancemi TelemetryClient, včetně modulů standardní telemetrická data, nastavte klíč v TelemetryConfiguration.Active. V metodě inicializace, jako je například souboru global.aspx.cs v rámci služby technologie ASP.NET, postupujte takto:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
```

Pokud chcete odeslat sadu událostí, které k jinému prostředku, můžete nastavit klíč pro konkrétní TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Chcete-li získat nový klíč, [vytvořit nový prostředek na portálu Application Insights][new].



## <a name="applicationid-provider"></a>ApplicationId poskytovatele

_K dispozici od v2.6.0_

Účelem tohoto zprostředkovatele je k vyhledání ID aplikací založené na Instrumentační klíč. ID aplikace je součástí RequestTelemetry a DependencyTelemetry a použít k určení korelace na portálu.

To je k dispozici nastavením `TelemetryConfiguration.ApplicationIdProvider` v kódu nebo v konfiguraci.

### <a name="interface-iapplicationidprovider"></a>Rozhraní: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Nabízíme dvě implementace v [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk: `ApplicationInsightsApplicationIdProvider` a `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Toto je obálka kolem rozhraní API profilu. To se omezení požadavků a výsledků do mezipaměti.

Tento zprostředkovatel je přidána do konfiguračního souboru při instalaci buď [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) nebo [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Tato třída obsahuje volitelné vlastnosti `ProfileQueryEndpoint`.
Ve výchozím nastavení je nastavené na `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Pokud je potřeba nakonfigurovat proxy server pro tuto konfiguraci, doporučujeme provést činnost základní adresu a včetně "/api/profily/{0}/appId". Všimněte si, že "{0}" bude nahrazena klíč instrumentace za běhu na požadavek.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Příklad konfigurace pomocí souboru ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Příklad konfigurace prostřednictvím kódu:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Toto je statické poskytovatele, který bude záviset na nakonfigurovaných Instrumentační klíč / ID aplikace dvojice.

Tato třída obsahuje vlastnosti `Defined`, což je Dictionary < string, string > Instrumentačního klíče dvojice ID aplikace.

Tato třída obsahuje volitelné vlastnosti `Next` které lze použít ke konfiguraci jiného zprostředkovatele pro použití při vyžádání Instrumentační klíč, který neexistuje v konfiguraci.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Příklad konfigurace pomocí souboru ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Příklad konfigurace prostřednictvím kódu:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Další postup
[Další informace o rozhraní API][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
