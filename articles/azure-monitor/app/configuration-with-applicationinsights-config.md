---
title: ApplicationInsights.config odkaz - Azure | Dokumenty společnosti Microsoft
description: Povolte nebo zakažte moduly shromažďování dat a přidejte čítače výkonu a další parametry.
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: 460bd79e3a37c492301f7438112fef4487618fed
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982085"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurace sady Application Insights SDK pomocí souboru ApplicationInsights.config nebo .xml
Sada Application Insights .NET SDK se skládá z několika balíčků NuGet. [Základní balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights) poskytuje rozhraní API pro odesílání telemetrie do Application Insights. [Další balíčky](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) poskytují telemetrické *moduly* a *inicializátory* pro automatické sledování telemetrie z vaší aplikace a jejího kontextu. Úpravou konfiguračního souboru můžete povolit nebo zakázat moduly telemetrie a inicializátory a nastavit parametry pro některé z nich.

Konfigurační `ApplicationInsights.config` soubor `ApplicationInsights.xml`je pojmenován nebo v závislosti na typu aplikace. Automaticky se přidá do projektu při [instalaci většiny verzí sady SDK][start]. Ve výchozím nastavení je při použití automatizovaného prostředí z projektů šablon sady Visual Studio, které podporují **telemetrii Přidat > application insights**, vytvoří se soubor ApplicationInsights.config v kořenové složce projektu a po vyhovění se zkopíruje do složky přihrádky. Je také přidán do webové aplikace [pomocí sledování stavu na serveru služby IIS][redfield]. Konfigurační soubor se ignoruje, pokud se používá [rozšíření pro web Azure](azure-web-apps.md) nebo rozšíření pro virtuální počítač Azure a [škálovací sadu virtuálních počítačů.](azure-vm-vmss-apps.md)

Neexistuje ekvivalentní soubor pro řízení [sady SDK na webové stránce][client].

Tento dokument popisuje oddíly, které se zobrazují v konfiguračním souboru, jak řídí součásti sady SDK a které balíčky NuGet načítají tyto součásti.

> [!NOTE]
> ApplicationInsights.config a .xml pokyny se nevztahují na sadu .NET Core SDK. Při konfiguraci aplikací .NET Core postupujte podle [této](../../azure-monitor/app/asp-net-core.md) příručky.

## <a name="telemetry-modules-aspnet"></a>Telemetrické moduly (ASP.NET)
Každý modul telemetrie shromažďuje určitý typ dat a používá základní rozhraní API k odeslání dat. Moduly jsou nainstalovány různými balíčky NuGet, které také přidávají požadované řádky do souboru .config.

V konfiguračním souboru pro každý modul je uzel. Chcete-li modul zakázat, odstraňte uzel nebo jej zakomentujte.

### <a name="dependency-tracking"></a>Sledování závislostí
[Sledování závislostí](../../azure-monitor/app/asp-net-dependencies.md) shromažďuje telemetrická data o volání, která vaše aplikace provádí do databází a externích služeb a databází. Chcete-li, aby tento modul fungoval na serveru služby IIS, je třeba [nainstalovat nástroj Sledování stavu][redfield].

Můžete také napsat vlastní kód sledování závislostí pomocí [rozhraní TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Balíček [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet.

Závislosti lze automaticky shromažďovat bez úpravy kódu pomocí připojení na základě agenta (bez kódu). Pokud ji chcete používat ve webových aplikacích Azure, povolte [rozšíření Application Insights](azure-web-apps.md). Chcete-li ji použít v azure vm nebo azure virtuální počítač škálovací sady povolit [rozšíření monitorování aplikací pro virtuální počítače a škálovací sady virtuálních strojů](azure-vm-vmss-apps.md).

### <a name="performance-collector"></a>Sběratel výkonu
[Shromažďuje čítače výkonu systému,](../../azure-monitor/app/performance-counters.md) jako je procesor, paměť a zatížení sítě z instalací služby IIS. Můžete určit, které čítače mají být shromažďovány, včetně čítačů výkonu, které jste sami nastavili.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Balíček [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet.

### <a name="application-insights-diagnostics-telemetry"></a>Telemetrie diagnostiky statistik aplikací
Sestavy `DiagnosticsTelemetryModule` chyby v kódu instrumentace Application Insights instrumentace sám. Například pokud kód nemůže získat přístup k `ITelemetryInitializer` čítačům výkonu nebo pokud vyvolá výjimku. Trasování telemetrie sledované tímto modulem se zobrazí v [diagnostickém hledání][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Vývojářský režim
`DeveloperModeWithDebuggerAttachedTelemetryModule`vynutí `TelemetryChannel` Application Insights odeslat data okamžitě, jednu položku telemetrie najednou, když ladicí program je připojen k procesu aplikace. To snižuje dobu mezi okamžikem, kdy vaše aplikace sleduje telemetrii a když se zobrazí na portálu Application Insights. To způsobuje významné režie v cpu a šířku pásma sítě.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Přehledy aplikací Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Balíček NuGet

### <a name="web-request-tracking"></a>Sledování webových požadavků
Hlásí [dobu odezvy a kód výsledků](../../azure-monitor/app/asp-net.md) požadavků HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Balíček [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet

### <a name="exception-tracking"></a>Sledování výjimek
`ExceptionTrackingTelemetryModule`sleduje neošetřené výjimky ve webové aplikaci. Viz [Selhání a výjimky][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Balíček [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`- sleduje [nepozorované výjimky z úloh](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`- sleduje neošetřené výjimky pro role pracovního procesu, služby systému Windows a konzolové aplikace.
* [Přehledy aplikací Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet balíček.

### <a name="eventsource-tracking"></a>Sledování zdroje událostí
`EventSourceTelemetryModule`umožňuje nakonfigurovat události EventSource, které mají být odeslány do Application Insights jako trasování. Informace o sledování událostí EventSource naleznete [v tématu Použití událostí Zdroje událostí](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Sledování událostí ETW
`EtwCollectorTelemetryModule`umožňuje konfigurovat události od poskytovatelů ETW, které mají být odeslány do Application Insights jako trasování. Informace o sledování událostí ETW naleznete [v tématu Použití událostí ETW](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Balíček Microsoft.ApplicationInsights poskytuje [základní rozhraní API](https://msdn.microsoft.com/library/mt420197.aspx) sady SDK. Ostatní moduly telemetrie použít a můžete [také použít k definování vlastní telemetrie](../../azure-monitor/app/api-custom-events-metrics.md).

* V souboru ApplicationInsights.config není položka.
* Balíček [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet. Pokud jste právě nainstalovat tento NuGet, žádný .config soubor je generován.

## <a name="telemetry-channel"></a>Kanál telemetrie
[Kanál telemetrie](telemetry-channels.md) spravuje ukládání do vyrovnávací paměti a přenos telemetrie do služby Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`je výchozí kanál pro webové aplikace. Uvězní data v paměti a využívá mechanismy opakování a místní diskové úložiště pro spolehlivější doručování telemetrie.
* `Microsoft.ApplicationInsights.InMemoryChannel`je zjednodušený telemetrický kanál, který se používá, pokud není nakonfigurován žádný jiný kanál. 

## <a name="telemetry-initializers-aspnet"></a>Inicializátory telemetrie (ASP.NET)
Telemetrie Initializers nastavit vlastnosti kontextu, které jsou odesílány spolu s každou položku telemetrie.

Můžete [napsat vlastní inicializátory](../../azure-monitor/app/api-filtering-sampling.md#add-properties) pro nastavení vlastností kontextu.

Standardní inicializátory jsou všechny nastaveny balíčky Web nebo WindowsServer NuGet:

* `AccountIdTelemetryInitializer`nastaví AccountId vlastnost.
* `AuthenticatedUserIdTelemetryInitializer`nastaví vlastnost AuthenticatedUserId nastavenou sadou JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer`aktualizuje `RoleName` a `RoleInstance` vlastnosti `Device` kontextu pro všechny položky telemetrie s informacemi extrahovanými z prostředí runtime Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer`aktualizuje `Version` vlastnost `Component` kontextu pro všechny položky telemetrie s `BuildInfo.config` hodnotou extrahohovou ze souboru vytvořeného MS Build.
* `ClientIpHeaderTelemetryInitializer`aktualizuje `Ip` vlastnost `Location` kontextu všech položek telemetrie `X-Forwarded-For` na základě hlavičky HTTP požadavku.
* `DeviceTelemetryInitializer`aktualizuje následující vlastnosti `Device` kontextu pro všechny položky telemetrie.
  * `Type`je nastavena na "PC"
  * `Id`je nastaven na název domény počítače, ve kterém je spuštěna webová aplikace.
  * `OemName`je nastavena na hodnotu `Win32_ComputerSystem.Manufacturer` extrahovanou z pole pomocí služby WMI.
  * `Model`je nastavena na hodnotu `Win32_ComputerSystem.Model` extrahovanou z pole pomocí služby WMI.
  * `NetworkType`je nastavena na hodnotu `NetworkInterface`extrahovanou z .
  * `Language`je nastavena na `CurrentCulture`název .
* `DomainNameRoleInstanceTelemetryInitializer`aktualizuje `RoleInstance` vlastnost `Device` kontextu pro všechny položky telemetrie s názvem domény počítače, ve kterém je spuštěna webová aplikace.
* `OperationNameTelemetryInitializer`aktualizuje `Name` vlastnost `RequestTelemetry` a `Name` vlastnost `Operation` kontextu všech položek telemetrie na základě metody HTTP, stejně jako názvy ASP.NET mvc řadiča a akce vyvolané ke zpracování požadavku.
* `OperationIdTelemetryInitializer`nebo `OperationCorrelationTelemetryInitializer` aktualizuje `Operation.Id` vlastnost kontextu všech položek telemetrie sledované při zpracování `RequestTelemetry.Id`požadavku s automaticky generované .
* `SessionTelemetryInitializer`aktualizuje `Id` vlastnost `Session` kontextu pro všechny položky telemetrie `ai_session` s hodnotou extrahovanou ze souboru cookie generovaného kódem instrumentace ApplicationInsights JavaScript spuštěným v prohlížeči uživatele.
* `SyntheticTelemetryInitializer`nebo `SyntheticUserAgentTelemetryInitializer` aktualizuje `User` `Session`vlastnosti `Operation` , a kontexty všech položek telemetrie sledovaných při zpracování požadavku ze syntetického zdroje, jako je například test dostupnosti nebo robot vyhledávače. Ve výchozím nastavení [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md) nezobrazuje syntetickou telemetrii.

    Sada `<Filters>` identifikující vlastnosti požadavků.
* `UserTelemetryInitializer`aktualizuje `Id` vlastnosti `AcquisitionDate` `User` kontextu pro všechny položky telemetrie `ai_user` s hodnotami extrahovanými ze souboru cookie generovaným kódem instrumentace Application Insights JavaScript spuštěným v prohlížeči uživatele.
* `WebTestTelemetryInitializer`Nastaví ID uživatele, ID relace a vlastnosti syntetického zdroje pro požadavky HTTP, které pocházejí z [testů dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md).
  Sada `<Filters>` identifikující vlastnosti požadavků.

Pro aplikace .NET spuštěné v Service `Microsoft.ApplicationInsights.ServiceFabric` Fabric můžete zahrnout balíček NuGet. Tento balíček `FabricTelemetryInitializer`obsahuje , který přidává service fabric vlastnosti telemetrie položky. Další informace naleznete na [stránce GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) o vlastnostech přidaných tímto balíčkem NuGet.

## <a name="telemetry-processors-aspnet"></a>Telemetrické procesory (ASP.NET)
Telemetrické procesory můžete filtrovat a upravovat každou položku telemetrie těsně před odesláním z sady SDK na portál.

Můžete [napsat vlastní telemetrické procesory](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Adaptivní vzorkovací telemetrický procesor (od 2.0.0-beta3)
Tato možnost je ve výchozím nastavení zapnutá. Pokud vaše aplikace odešle velké množství telemetrie, tento procesor některé z ní odebere.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametr poskytuje cíl, který se algoritmus pokusí dosáhnout. Každá instance sady SDK funguje nezávisle, takže pokud je váš server clusterem několika počítačů, bude odpovídajícím způsobem vynásoben skutečný objem telemetrie.

[Další informace o vzorkování](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Procesor telemetrie s pevnou rychlostí vzorkování (od 2.0.0-beta1)
K dispozici je také standardní [vzorkovací telemetrický procesor](../../azure-monitor/app/api-filtering-sampling.md) (od 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>InstrumentaceKlíč
To určuje prostředek Application Insights, ve kterém se zobrazí data. Obvykle vytvoříte samostatný prostředek se samostatným klíčem pro každou aplikaci.

Pokud chcete nastavit klíč dynamicky – například pokud chcete odeslat výsledky z aplikace do různých prostředků - můžete klíč vynechat z konfiguračního souboru a místo toho jej nastavit v kódu.

Chcete-li nastavit klíč pro všechny instance TelemetryClient, včetně standardních modulů telemetrie. Proveďte to inicializační metodou, například global.aspx.cs ve službě ASP.NET:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Pokud chcete odeslat konkrétní sadu událostí do jiného prostředku, můžete nastavit klíč pro konkrétní TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Chcete-li získat nový klíč, [vytvořte nový prostředek na portálu Application Insights][new].



## <a name="applicationid-provider"></a>Zprostředkovatel ApplicationId

_K dispozici od 2.6.0_

Účelem tohoto zprostředkovatele je vyhledat ID aplikace na základě klíče instrumentace. ID aplikace je součástí RequestTelemetry a DependencyTelemetry a slouží k určení korelace na portálu.

To je k `TelemetryConfiguration.ApplicationIdProvider` dispozici nastavením buď v kódu nebo v konfigurační.

### <a name="interface-iapplicationidprovider"></a>Rozhraní: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Poskytujeme dvě implementace v [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk: `ApplicationInsightsApplicationIdProvider` a `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Toto je obálka kolem našeho rozhraní API profilu. Bude omezení požadavků a výsledky mezipaměti.

Tento zprostředkovatel je přidán do konfiguračního souboru při instalaci [souboru Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) nebo [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Tato třída má `ProfileQueryEndpoint`volitelnou vlastnost .
Ve výchozím nastavení `https://dc.services.visualstudio.com/api/profiles/{0}/appId`je tato hodnota nastavena na hodnotu .
Pokud potřebujete nakonfigurovat proxy server pro tuto konfiguraci, doporučujeme proxying základní{0}adresu a včetně "/api/profiles/ /appId". Všimněte{0}si, že ' ' je nahrazen za běhu na požadavek s instrumentace klíč.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Příklad konfigurace pomocí applicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Příklad konfigurace pomocí kódu:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Jedná se o statického zprostředkovatele, který bude spoléhat na nakonfigurované dvojice instrumentačního klíče / ID aplikace.

Tato třída má `Defined`vlastnost , což je řetězec Dictionary<,řetězec> dvojice ID instrumentace na ID aplikace.

Tato třída má `Next` volitelnou vlastnost, kterou lze použít ke konfiguraci jiného zprostředkovatele pro použití při požadavku na klíč instrumentace, který ve vaší konfiguraci neexistuje.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Příklad konfigurace pomocí applicationInsights.config:
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

#### <a name="example-configuration-via-code"></a>Příklad konfigurace pomocí kódu:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Další kroky
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
