---
title: Odkaz na ApplicationInsights.config – Azure | Microsoft Docs
description: Povolte nebo zakažte moduly shromažďování dat a přidejte čítače výkonu a další parametry.
ms.topic: conceptual
ms.date: 05/22/2019
ms.custom: devx-track-csharp
ms.reviewer: olegan
ms.openlocfilehash: 7c0759e78b1adc1704acb602daa12cf9cabbe153
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934799"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurace sady Application Insights SDK pomocí souboru ApplicationInsights.config nebo .xml
Sada Application Insights .NET SDK se skládá z řady balíčků NuGet. [Základní balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights) poskytuje rozhraní API pro posílání telemetrie do Application Insights. [Další balíčky](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) poskytují *moduly* telemetrie a *Inicializátory* pro automatické sledování telemetrie z vaší aplikace a jejího kontextu. Úpravou konfiguračního souboru můžete povolit nebo zakázat moduly a Inicializátory telemetrie a nastavit parametry pro některé z nich.

Konfigurační soubor má název `ApplicationInsights.config` nebo `ApplicationInsights.xml` , v závislosti na typu vaší aplikace. Automaticky se přidá do projektu při [instalaci většiny verzí sady SDK][start]. Ve výchozím nastavení, při použití automatizovaného prostředí z projektů šablon sady Visual Studio, které podporují **přidání > telemetrie Application Insights**, se soubor ApplicationInsights.config vytvoří v kořenové složce projektu a při jeho splnění se zkopíruje do složky bin. Také se přidá do webové aplikace [monitorování stavu na serveru služby IIS][redfield]. Konfigurační soubor se ignoruje, pokud se použije [rozšíření pro web Azure](azure-web-apps.md) nebo [rozšíření pro virtuální počítač Azure a sadu škálování virtuálního počítače](azure-vm-vmss-apps.md) .

Pro řízení [sady SDK na webové stránce][client]není k dispozici odpovídající soubor.

Tento dokument popisuje oddíly, které vidíte v konfiguračním souboru, jak ovládají součásti sady SDK a které balíčky NuGet tyto součásti načítají.

> [!NOTE]
> Pokyny ApplicationInsights.config a. XML se nevztahují na .NET Core SDK. Pokud chcete konfigurovat aplikace .NET Core, postupujte podle pokynů v [tomto](./asp-net-core.md) průvodci.

## <a name="telemetry-modules-aspnet"></a>Moduly telemetrie (ASP.NET)
Každý modul telemetrie shromažďuje konkrétní typ dat a používá základní rozhraní API k posílání dat. Moduly jsou nainstalovány různými balíčky NuGet, které také přidávají požadované řádky do souboru. config.

V konfiguračním souboru je uzel pro každý modul. Pokud chcete modul zakázat, odstraňte uzel nebo ho Odkomentujte.

### <a name="dependency-tracking"></a>Sledování závislostí
[Sledování závislostí](./asp-net-dependencies.md) shromažďuje telemetrii o voláních, která vaše aplikace vytváří v databázích a externích službách a databázích. Aby mohl tento modul fungovat na serveru služby IIS, je nutné [nainstalovat monitorování stavu][redfield].

Pomocí [rozhraní API TrackDependency](./api-custom-events-metrics.md#trackdependency)můžete také napsat vlastní kód sledování závislosti.

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Balíček NuGet [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector)

Závislosti se dají automaticky shromáždit bez úprav kódu pomocí připojení (bez kódu) na základě agenta. Pokud ho chcete použít ve službě Azure Web Apps, povolte [rozšíření Application Insights](azure-web-apps.md). Pokud ho chcete použít ve virtuálním počítači Azure nebo Azure Virtual Machine Scale set, povolte [rozšíření monitorování aplikací pro virtuální počítače a sadu škálování virtuálního počítače](azure-vm-vmss-apps.md).

### <a name="performance-collector"></a>Kolektor výkonu
[Shromažďuje čítače výkonu systému](./performance-counters.md) , jako je procesor, paměť a zatížení sítě, z instalace služby IIS. Můžete určit, které čítače se mají shromažďovat, včetně čítačů výkonu, které jste nastavili sami.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Balíček NuGet [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights telemetrie diagnostiky
`DiagnosticsTelemetryModule`Hlásí chyby v samotném kódu instrumentace Application Insights. Například pokud kód nemůže získat přístup k čítačům výkonu nebo `ITelemetryInitializer` vyvolá výjimku. Sledovací telemetrie sledovanou tímto modulem se zobrazí v [diagnostickém vyhledávání][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Vývojářský režim
`DeveloperModeWithDebuggerAttachedTelemetryModule` vynutí, `TelemetryChannel` aby Application Insights odesílala data okamžitě, jednu položku telemetrie v čase, když je ladicí program připojen k procesu aplikace. Tím se zkracuje doba mezi okamžikem, kdy vaše aplikace sleduje telemetrii a když se zobrazuje na portálu Application Insights. Způsobuje značnou režii v CPU a šířce pásma sítě.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Balíček NuGet

### <a name="web-request-tracking"></a>Sledování webových žádostí
Oznamuje [dobu odezvy a kód výsledku](../../azure-monitor/app/asp-net.md) požadavků HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet

### <a name="exception-tracking"></a>Sledování výjimek
`ExceptionTrackingTelemetryModule` sleduje neošetřené výjimky ve vaší webové aplikaci. Viz [selhání a výjimky][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -sleduje výjimky nepozorovaných úloh.
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` -sleduje neošetřené výjimky pro role pracovního procesu, služby systému Windows a konzolové aplikace.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Balíček NuGet.

### <a name="eventsource-tracking"></a>Sledování EventSource
`EventSourceTelemetryModule` umožňuje konfigurovat události EventSource k odeslání do Application Insights jako trasování. Další informace o sledování událostí EventSource najdete v tématu [použití událostí EventSource](./asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Sledování událostí ETW
`EtwCollectorTelemetryModule` umožňuje konfigurovat události od zprostředkovatelů ETW, které se mají odeslat Application Insights jako trasování. Informace o sledování událostí ETW najdete v tématu [použití událostí ETW](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft. ApplicationInsights
Balíček Microsoft. ApplicationInsights poskytuje [základní rozhraní API](/dotnet/api/microsoft.applicationinsights?view=azure-dotnet) sady SDK. Ostatní moduly telemetrie tuto možnost používají a můžete [je také použít k definování vlastní telemetrie](./api-custom-events-metrics.md).

* V ApplicationInsights.config není žádný záznam.
* Balíček NuGet [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) Pokud nainstalujete jenom tento NuGet, nevytvoří se žádný soubor. config.

## <a name="telemetry-channel"></a>Kanál telemetrie
[Kanál telemetrie](telemetry-channels.md) spravuje ukládání do vyrovnávací paměti a přenos telemetrie do služby Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` je výchozím kanálem pro webové aplikace. Ukládá data do vyrovnávací paměti a využívá mechanismy opakování a úložiště na místním disku pro spolehlivější doručování telemetrie.
* `Microsoft.ApplicationInsights.InMemoryChannel` je odlehčený kanál telemetrie, který se používá, pokud není nakonfigurovaný žádný jiný kanál. 

## <a name="telemetry-initializers-aspnet"></a>Inicializátory telemetrie (ASP.NET)
Inicializátory telemetrie nastavují vlastnosti kontextu, které se odesílají společně s každou položkou telemetrie.

Můžete [napsat vlastní Inicializátory](./api-filtering-sampling.md#add-properties) pro nastavení vlastností kontextu.

Standardní Inicializátory jsou nastaveny buď pomocí webu, nebo WindowsServer balíčky NuGet:

* `AccountIdTelemetryInitializer` nastaví vlastnost AccountId.
* `AuthenticatedUserIdTelemetryInitializer` nastaví vlastnost AuthenticatedUserId dle sady JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer` aktualizuje `RoleName` vlastnosti a `RoleInstance` `Device` kontextu pro všechny položky telemetrie s informacemi získanými z běhového prostředí Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aktualizuje `Version` vlastnost `Component` Context pro všechny položky telemetrie s hodnotou extrahovanou ze `BuildInfo.config` souboru vytvořeného pomocí sestavení MS Build.
* `ClientIpHeaderTelemetryInitializer` aktualizuje `Ip` vlastnost `Location` kontextu všech položek telemetrie na základě `X-Forwarded-For` hlavičky HTTP žádosti.
* `DeviceTelemetryInitializer` aktualizuje následující vlastnosti `Device` kontextu pro všechny položky telemetrie.
  * `Type` je nastavená na "počítač"
  * `Id` je nastaven na název domény počítače, na kterém je webová aplikace spuštěna.
  * `OemName` hodnota je nastavená na extrakci hodnoty z `Win32_ComputerSystem.Manufacturer` pole pomocí rozhraní WMI.
  * `Model` hodnota je nastavená na extrakci hodnoty z `Win32_ComputerSystem.Model` pole pomocí rozhraní WMI.
  * `NetworkType` je nastaven na hodnotu extrahovanou z `NetworkInterface` .
  * `Language` je nastaven na název `CurrentCulture` .
* `DomainNameRoleInstanceTelemetryInitializer` aktualizuje `RoleInstance` vlastnost `Device` Context pro všechny položky telemetrie s názvem domény počítače, na kterém je webová aplikace spuštěná.
* `OperationNameTelemetryInitializer` aktualizuje `Name` vlastnost `RequestTelemetry` a `Name` vlastnosti `Operation` kontextu všech položek TELEMETRIE na základě metody HTTP a také názvů kontroléru ASP.NET MVC a akce vyvolané ke zpracování žádosti.
* `OperationIdTelemetryInitializer` nebo `OperationCorrelationTelemetryInitializer` aktualizuje `Operation.Id` vlastnost Context všech položek telemetrie, které jsou sledovány při zpracování žádosti s automaticky vygenerovaným objektem `RequestTelemetry.Id` .
* `SessionTelemetryInitializer` aktualizuje `Id` vlastnost `Session` Context pro všechny položky telemetrie s hodnotou extrahovanou ze `ai_session` souboru cookie generovaného kódem instrumentace ApplicationInsights JavaScript spuštěným v prohlížeči uživatele.
* `SyntheticTelemetryInitializer` nebo `SyntheticUserAgentTelemetryInitializer` aktualizuje `User` vlastnosti, `Session` a `Operation` kontexty všech položek telemetrie, které jsou sledovány při zpracování žádosti z syntetického zdroje, jako je například test dostupnosti nebo robot vyhledávače. Ve výchozím nastavení [Průzkumník metrik](../platform/metrics-charts.md) nezobrazuje syntetickou telemetrii.

    `<Filters>`Sada identifikující vlastnosti požadavků.
* `UserTelemetryInitializer` aktualizuje `Id` vlastnosti a `AcquisitionDate` `User` pro kontext pro všechny položky telemetrie s hodnotami extrahované ze `ai_user` souboru cookie generovaného kódem Application Insights JavaScript instrumentace spuštěným v prohlížeči uživatele.
* `WebTestTelemetryInitializer` nastaví ID uživatele, ID relace a syntetické vlastnosti zdroje pro požadavky HTTP, které pocházejí z [testů dostupnosti](./monitor-web-app-availability.md).
  `<Filters>`Sada identifikující vlastnosti požadavků.

Pro aplikace .NET běžící v Service Fabric můžete zahrnout `Microsoft.ApplicationInsights.ServiceFabric` balíček NuGet. Tento balíček zahrnuje `FabricTelemetryInitializer` , který přidává vlastnosti Service Fabric do položek telemetrie. Další informace najdete na [stránce GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) o vlastnostech přidaných tímto balíčkem NuGet.

## <a name="telemetry-processors-aspnet"></a>Procesory telemetrie (ASP.NET)
Procesor telemetrie může filtrovat a upravovat každou položku telemetrie těsně předtím, než se pošle z SDK na portál.

Můžete [napsat vlastní procesory telemetrie](./api-filtering-sampling.md#filtering).

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

[Přečtěte si další informace o vzorkování](./sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Procesor telemetrie pro vzorkování s pevnou sazbou (od 2.0.0-Beta1)
K dispozici je také standardní [procesor telemetrie vzorkování](./api-filtering-sampling.md) (od 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

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

Tato možnost je k dispozici `TelemetryConfiguration.ApplicationIdProvider` v kódu nebo v konfiguraci.

### <a name="interface-iapplicationidprovider"></a>Rozhraní: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Poskytujeme dvě implementace v sadě [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) SDK: `ApplicationInsightsApplicationIdProvider` a `DictionaryApplicationIdProvider` .

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Toto je Obálka kolem našeho rozhraní API profilu. Omezí požadavky a výsledky mezipaměti.

Tento poskytovatel se přidá do konfiguračního souboru při instalaci [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) nebo [Microsoft. ApplicationInsights. Web.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Tato třída má volitelnou vlastnost `ProfileQueryEndpoint` .
Ve výchozím nastavení je tato hodnota nastavena na `https://dc.services.visualstudio.com/api/profiles/{0}/appId` .
Pokud pro tuto konfiguraci potřebujete nakonfigurovat proxy server, doporučujeme, abyste použili proxy základní adresu a včetně "/API/Profiles/ {0} /appId". Všimněte si, že znak {0} "" je nahrazen za běhu na žádost s klíčem instrumentace.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Příklad konfigurace prostřednictvím ApplicationInsights.config:
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

Tato třída má vlastnost `Defined` , která je slovníkem<řetězec, řetězec> klíče instrumentace na páry ID aplikace.

Tato třída má volitelnou vlastnost, `Next` která se dá použít ke konfiguraci jiného poskytovatele, který se použije, když se požaduje klíč instrumentace, který ve vaší konfiguraci neexistuje.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Příklad konfigurace prostřednictvím ApplicationInsights.config:
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

[api]: ./api-custom-events-metrics.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: ./asp-net-exceptions.md
[netlogs]: ./asp-net-trace-logs.md
[new]: ./create-new-resource.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

