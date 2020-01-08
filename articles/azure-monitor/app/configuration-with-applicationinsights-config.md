---
title: Odkaz na ApplicationInsights. config – Azure | Microsoft Docs
description: Povolte nebo zakažte moduly shromažďování dat a přidejte čítače výkonu a další parametry.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: f7f32cc7f160a7ac9253b60e8c0c13926c110ac2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407107"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurace sady Application Insights SDK pomocí souboru ApplicationInsights.config nebo .xml
Sada Application Insights .NET SDK se skládá z řady balíčků NuGet. [Základní balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights) poskytuje rozhraní API pro posílání telemetrie do Application Insights. [Další balíčky](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) poskytují *moduly* telemetrie a *Inicializátory* pro automatické sledování telemetrie z vaší aplikace a jejího kontextu. Úpravou konfiguračního souboru můžete povolit nebo zakázat moduly a Inicializátory telemetrie a nastavit parametry pro některé z nich.

Konfigurační soubor má název `ApplicationInsights.config` nebo `ApplicationInsights.xml`v závislosti na typu vaší aplikace. Automaticky se přidá do projektu při [instalaci většiny verzí sady SDK][start]. Ve výchozím nastavení se při použití automatizovaného prostředí z projektů šablon sady Visual Studio, které podporují **přidání > telemetrie Application Insights**, vytvoří soubor ApplicationInsights. config v kořenové složce projektu a v případě, že je do složky bin zkopírována hodnota splněné. Také se přidá do webové aplikace [monitorování stavu na serveru služby IIS][redfield]. Konfigurační soubor se ignoruje, pokud se použije [rozšíření pro web Azure](azure-web-apps.md) nebo [rozšíření pro virtuální počítač Azure a sadu škálování virtuálního počítače](azure-vm-vmss-apps.md) .

Pro řízení [sady SDK na webové stránce][client]není k dispozici odpovídající soubor.

Tento dokument popisuje oddíly, které vidíte v konfiguračním souboru, jak ovládají součásti sady SDK a které balíčky NuGet tyto součásti načítají.

> [!NOTE]
> Instrukce ApplicationInsights. config a. XML se nevztahují na .NET Core SDK. Pokud chcete konfigurovat aplikace .NET Core, postupujte podle pokynů v [tomto](../../azure-monitor/app/asp-net-core.md) průvodci.

## <a name="telemetry-modules-aspnet"></a>Moduly telemetrie (ASP.NET)
Každý modul telemetrie shromažďuje konkrétní typ dat a používá základní rozhraní API k posílání dat. Moduly jsou nainstalovány různými balíčky NuGet, které také přidávají požadované řádky do souboru. config.

V konfiguračním souboru je uzel pro každý modul. Pokud chcete modul zakázat, odstraňte uzel nebo ho Odkomentujte.

### <a name="dependency-tracking"></a>Sledování závislostí
[Sledování závislostí](../../azure-monitor/app/asp-net-dependencies.md) shromažďuje telemetrii o voláních, která vaše aplikace vytváří v databázích a externích službách a databázích. Aby mohl tento modul fungovat na serveru služby IIS, je nutné [nainstalovat monitorování stavu][redfield].

Pomocí [rozhraní API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)můžete také napsat vlastní kód sledování závislosti.

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Balíček NuGet [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector)

Závislosti se dají automaticky shromáždit bez úprav kódu pomocí připojení (bez kódu) na základě agenta. Pokud ho chcete použít ve službě Azure Web Apps, povolte [rozšíření Application Insights](azure-web-apps.md). Pokud ho chcete použít ve virtuálním počítači Azure nebo Azure Virtual Machine Scale set, povolte [rozšíření monitorování aplikací pro virtuální počítače a sadu škálování virtuálního počítače](azure-vm-vmss-apps.md).

### <a name="performance-collector"></a>Kolektor výkonu
[Shromažďuje čítače výkonu systému](../../azure-monitor/app/performance-counters.md) , jako je procesor, paměť a zatížení sítě, z instalace služby IIS. Můžete určit, které čítače se mají shromažďovat, včetně čítačů výkonu, které jste nastavili sami.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet package.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights telemetrie diagnostiky
`DiagnosticsTelemetryModule` hlásí chyby v samotném kódu Application Insights instrumentace. Například pokud kód nemůže získat přístup k čítačům výkonu nebo pokud `ITelemetryInitializer` vyvolá výjimku. Sledovací telemetrie sledovanou tímto modulem se zobrazí v [diagnostickém vyhledávání][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Vývojářský režim
`DeveloperModeWithDebuggerAttachedTelemetryModule` vynutí, aby Application Insights `TelemetryChannel` odesílat data okamžitě, jednu položku telemetrie v čase, když je ladicí program připojen k procesu aplikace. Tím se zkracuje doba mezi okamžikem, kdy vaše aplikace sleduje telemetrii a když se zobrazuje na portálu Application Insights. Způsobuje značnou režii v CPU a šířce pásma sítě.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Balíček NuGet

### <a name="web-request-tracking"></a>Sledování webových žádostí
Oznamuje [dobu odezvy a kód výsledku](../../azure-monitor/app/asp-net.md) požadavků HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet package

### <a name="exception-tracking"></a>Sledování výjimek
`ExceptionTrackingTelemetryModule` sleduje neošetřené výjimky ve vaší webové aplikaci. Viz [selhání a výjimky][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet package
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-sleduje [výjimky nepozorovaných úloh](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` – sleduje neošetřené výjimky pro role pracovního procesu, služby systému Windows a konzolové aplikace.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Balíček NuGet.

### <a name="eventsource-tracking"></a>Sledování EventSource
`EventSourceTelemetryModule` umožňuje konfigurovat události EventSource, které se mají odeslat do Application Insights jako trasování. Další informace o sledování událostí EventSource najdete v tématu [použití událostí EventSource](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Sledování událostí ETW
`EtwCollectorTelemetryModule` umožňuje konfigurovat události od zprostředkovatelů ETW, které se budou odesílat do Application Insights jako trasování. Informace o sledování událostí ETW najdete v tématu [použití událostí ETW](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Balíček Microsoft. ApplicationInsights poskytuje [základní rozhraní API](https://msdn.microsoft.com/library/mt420197.aspx) sady SDK. Ostatní moduly telemetrie tuto možnost používají a můžete [je také použít k definování vlastní telemetrie](../../azure-monitor/app/api-custom-events-metrics.md).

* V souboru ApplicationInsights. config není žádná položka.
* Balíček NuGet [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) Pokud nainstalujete jenom tento NuGet, nevytvoří se žádný soubor. config.

## <a name="telemetry-channel"></a>Kanál telemetrie
[Kanál telemetrie](telemetry-channels.md) spravuje ukládání do vyrovnávací paměti a přenos telemetrie do služby Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` je výchozím kanálem pro webové aplikace. Ukládá data do vyrovnávací paměti a využívá mechanismy opakování a úložiště na místním disku pro spolehlivější doručování telemetrie.
* `Microsoft.ApplicationInsights.InMemoryChannel` je odlehčený kanál telemetrie, který se používá, pokud není nakonfigurovaný žádný jiný kanál. 

## <a name="telemetry-initializers-aspnet"></a>Inicializátory telemetrie (ASP.NET)
Inicializátory telemetrie nastavují vlastnosti kontextu, které se odesílají společně s každou položkou telemetrie.

Můžete [napsat vlastní Inicializátory](../../azure-monitor/app/api-filtering-sampling.md#add-properties) pro nastavení vlastností kontextu.

Standardní Inicializátory jsou nastaveny buď pomocí webu, nebo WindowsServer balíčky NuGet:

* `AccountIdTelemetryInitializer` nastaví vlastnost AccountId.
* `AuthenticatedUserIdTelemetryInitializer` nastaví vlastnost AuthenticatedUserId dle sady JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer` aktualizuje `RoleName` a `RoleInstance` vlastnosti kontextu `Device` pro všechny položky telemetrie s informacemi extrahovanými z běhového prostředí Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aktualizuje vlastnost `Version` kontextu `Component` pro všechny položky telemetrie s hodnotou extrahovanou ze souboru `BuildInfo.config` vytvořeného pomocí MS buildu.
* `ClientIpHeaderTelemetryInitializer` Updates `Ip` vlastnosti `Location`ho kontextu všech položek telemetrie na základě hlavičky HTTP `X-Forwarded-For` žádosti.
* `DeviceTelemetryInitializer` aktualizuje následující vlastnosti kontextu `Device` pro všechny položky telemetrie.
  * `Type` je nastavená na "počítač".
  * `Id` se nastaví na název domény počítače, na kterém je webová aplikace spuštěná.
  * `OemName` se nastaví na hodnotu extrahovanou z pole `Win32_ComputerSystem.Manufacturer` pomocí rozhraní WMI.
  * `Model` se nastaví na hodnotu extrahovanou z pole `Win32_ComputerSystem.Model` pomocí rozhraní WMI.
  * `NetworkType` je nastavená na hodnotu extrahovanou z `NetworkInterface`.
  * `Language` se nastaví na název `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` aktualizuje vlastnost `RoleInstance` kontextu `Device` pro všechny položky telemetrie s názvem domény počítače, na kterém je webová aplikace spuštěná.
* `OperationNameTelemetryInitializer` aktualizuje vlastnost `Name` `RequestTelemetry` a vlastnost `Name` kontextu `Operation` pro všechny položky telemetrie založené na metodě HTTP a také názvy kontroléru ASP.NET MVC a akce vyvolané ke zpracování žádosti.
* `OperationIdTelemetryInitializer` nebo `OperationCorrelationTelemetryInitializer` aktualizuje vlastnost Context `Operation.Id` všech položek telemetrie, které byly sledovány při zpracování požadavku s automaticky vygenerovaným `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` aktualizuje vlastnost `Id` `Session`ového kontextu pro všechny položky telemetrie s hodnotou extrahovanou ze souboru cookie `ai_session` vygenerovaného kódem instrumentace JavaScriptu ApplicationInsights, který běží v prohlížeči uživatele.
* `SyntheticTelemetryInitializer` nebo `SyntheticUserAgentTelemetryInitializer` aktualizuje vlastnosti `User`, `Session`a `Operation` kontextů všech položek telemetrie, které jsou sledovány při zpracování žádosti z syntetického zdroje, jako je například test dostupnosti nebo robot vyhledávače. Ve výchozím nastavení [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md) nezobrazuje syntetickou telemetrii.

    `<Filters>` nastavit identifikaci vlastností požadavků.
* `UserTelemetryInitializer` aktualizuje vlastnosti `Id` a `AcquisitionDate` `User` kontextu pro všechny položky telemetrie s hodnotami extrahovaými ze souboru cookie `ai_user` vygenerovaného kódem instrumentace Application Insights JavaScriptu spuštěným v prohlížeči uživatele.
* `WebTestTelemetryInitializer` nastaví ID uživatele, ID relace a syntetické vlastnosti zdroje pro požadavky HTTP, které pocházejí z [testů dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md).
  `<Filters>` nastavit identifikaci vlastností požadavků.

Pro aplikace .NET běžící v Service Fabric můžete zahrnout balíček NuGet `Microsoft.ApplicationInsights.ServiceFabric`. Tento balíček obsahuje `FabricTelemetryInitializer`, která přidává Service Fabric vlastností do položek telemetrie. Další informace najdete na [stránce GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) o vlastnostech přidaných tímto balíčkem NuGet.

## <a name="telemetry-processors-aspnet"></a>Procesory telemetrie (ASP.NET)
Procesor telemetrie může filtrovat a upravovat každou položku telemetrie těsně předtím, než se pošle z SDK na portál.

Můžete [napsat vlastní procesory telemetrie](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Procesor telemetrie adaptivního vzorkování (od 2.0.0-beta3)
Tato možnost je ve výchozím nastavení zapnutá. Pokud vaše aplikace pošle spoustu telemetrie, tento procesor odebere některé z nich.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametr poskytuje cíl, který se algoritmus snaží dosáhnout. Každá instance sady SDK funguje nezávisle, takže pokud je váš server clusterem několika počítačů, skutečný objem telemetrie se odpovídajícím způsobem vynásobí.

[Přečtěte si další informace o vzorkování](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Procesor telemetrie pro vzorkování s pevnou sazbou (od 2.0.0-Beta1)
K dispozici je také standardní [procesor telemetrie vzorkování](../../azure-monitor/app/api-filtering-sampling.md) (od 2.0.1):

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
Tyto parametry ovlivňují, jak by měla sada Java SDK ukládat a vyprázdnit data telemetrie, která shromažďuje.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Počet položek telemetrie, které mohou být uloženy v úložišti v paměti sady SDK. Po dosažení tohoto počtu se vyrovnávací paměť telemetrie vyprázdní – to znamená, že se položky telemetrie odesílají na Application Insights Server.

* Minimum: 1
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
Určuje, jak často mají být data uložená v úložišti v paměti vyprázdněna (odeslána do Application Insights).

* Minimum: 1
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
Určuje maximální velikost v MB, která je vyhrazená pro trvalé úložiště na místním disku. Toto úložiště se používá k uchování položek telemetrie, které se nepodařilo přenést do Application Insightsho koncového bodu. Když je velikost úložiště splněná, nové položky telemetrie se zahodí.

* Minimum: 1
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
Tím se určuje prostředek Application Insights, ve kterém se budou zobrazovat data. Obvykle vytvoříte samostatný prostředek, který má samostatný klíč, pro každou z vašich aplikací.

Pokud chcete klíč dynamicky nastavit, například pokud chcete odeslat výsledky z aplikace do různých prostředků – můžete z konfiguračního souboru klíč vynechat a místo toho ho nastavit v kódu.

Pro nastavení klíče pro všechny instance TelemetryClient, včetně standardních modulů telemetrie. Udělejte to v inicializační metodě, jako je například global.aspx.cs ve službě ASP.NET:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Pokud chcete pouze odeslat konkrétní sadu událostí na jiný prostředek, můžete nastavit klíč pro konkrétní TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Nový klíč získáte [vytvořením nového prostředku na portálu Application Insights][new].



## <a name="applicationid-provider"></a>Zprostředkovatel ApplicationId

_K dispozici od verze v 2.6.0_

Účelem tohoto poskytovatele je vyhledat ID aplikace na základě klíče instrumentace. ID aplikace je součástí RequestTelemetry a DependencyTelemetry a používá se k určení korelace na portálu.

Tato možnost je k dispozici nastavením `TelemetryConfiguration.ApplicationIdProvider` v kódu nebo v konfiguraci.

### <a name="interface-iapplicationidprovider"></a>Rozhraní: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Poskytujeme dvě implementace v sadě [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk: `ApplicationInsightsApplicationIdProvider` a `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Toto je Obálka kolem našeho rozhraní API profilu. Omezí požadavky a výsledky mezipaměti.

Tento poskytovatel se přidá do konfiguračního souboru při instalaci [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) nebo [Microsoft. ApplicationInsights. Web.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Tato třída má volitelnou vlastnost `ProfileQueryEndpoint`.
Ve výchozím nastavení je tato hodnota nastavená na `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Pokud pro tuto konfiguraci potřebujete nakonfigurovat proxy server, doporučujeme, abyste použili proxy základní adresu a včetně "/API/Profiles/{0}/appId". Všimněte si, že '{0}' je nahrazen za běhu na žádost pomocí klíče instrumentace.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Příklad konfigurace prostřednictvím ApplicationInsights. config:
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

Toto je statický zprostředkovatel, který bude spoléhat na vaše nakonfigurované páry klíč instrumentace nebo ID aplikace.

Tato třída má `Defined`vlastností, což je slovník < řetězec, > řetězce instrumentace klíče instrumentace na páry ID aplikace.

Tato třída má volitelnou vlastnost `Next`, která se dá použít ke konfiguraci jiného poskytovatele, který se použije, když se požaduje klíč instrumentace, který ve vaší konfiguraci neexistuje.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Příklad konfigurace prostřednictvím ApplicationInsights. config:
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




## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o rozhraní API][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
