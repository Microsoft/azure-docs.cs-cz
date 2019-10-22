---
title: Azure Monitor – Azure Application Insights přepsat výchozí koncové body sady SDK | Microsoft Docs
description: Upravte výchozí koncové body sady Azure Application Insights SDK pro oblasti, jako je Azure Government.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/26/2019
ms.openlocfilehash: e1db9782fe923f7a5759f4e001cd0db970606fed
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677483"
---
# <a name="application-insights-overriding-default-endpoints"></a>Application Insights přepsání výchozích koncových bodů

Pokud chcete odesílat data z Application Insights do určitých oblastí, budete muset přepsat výchozí adresy koncových bodů. Každá sada SDK vyžaduje mírně odlišnou změnu, která je popsána v tomto článku. Tyto změny vyžadují úpravu ukázkového kódu a nahrazení hodnot zástupných symbolů pro `QuickPulse_Endpoint_Address`, `TelemetryChannel_Endpoint_Address` a `Profile_Query_Endpoint_address` skutečnými adresami koncových bodů pro konkrétní oblast. Na konci tohoto článku najdete odkazy na adresy koncových bodů v oblastech, kde je tato konfigurace nutná.

## <a name="sdk-code-changes"></a>Změny kódu SDK

### <a name="net-with-applicationinsightsconfig"></a>.NET s ApplicationInsights. config

> [!NOTE]
> Soubor ApplicationInsights. config je automaticky přepsán, kdykoli je proveden upgrade sady SDK. Po provedení upgradu sady SDK nezapomeňte znovu zadat hodnoty koncového bodu specifické pro oblast.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

Upravte soubor appSettings. JSON v projektu následujícím způsobem a upravte hlavní koncový bod:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Hodnoty pro živé metriky a koncový bod dotazu profilu lze nastavit pouze prostřednictvím kódu. Chcete-li přepsat výchozí hodnoty pro všechny hodnoty koncových bodů prostřednictvím kódu, proveďte následující změny v metodě `ConfigureServices` `Startup.cs` souboru:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

### <a name="azure-functions-v2x"></a>Azure Functions v2. x

Do projektu funkce nainstalujte následující balíčky:

- Microsoft. ApplicationInsights verze 2.10.0
- Microsoft. ApplicationInsights. PerfCounterCollector verze 2.10.0
- Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel verze 2.10.0

Pak přidejte (nebo upravte) spouštěcí kód pro vaši aplikaci Function:

```csharp
[assembly: WebJobsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IWebJobsBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "QuickPulse_Endpoint_Address"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });
      }
  }
}
```

### <a name="java"></a>Java

Upravte soubor ApplicationInsights. XML a změňte výchozí adresu koncového bodu.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Upravte soubor `application.properties` a přidejte:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Koncové body lze také konfigurovat pomocí proměnných prostředí:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

## <a name="regions-that-require-endpoint-modification"></a>Oblasti, které vyžadují úpravu koncového bodu

V současné době jsou k [disAzure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) a [Azure Čína](https://docs.microsoft.com/azure/china/resources-developer-guide)jedinými oblastmi, které vyžadují úpravy koncových bodů.

|Oblast |  Název koncového bodu | Hodnota |
|-----------------|:------------|:-------------|
| Azure (Čína) | Kanál telemetrie | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure (Čína) | QuickPulse (živé metriky) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure (Čína) | Dotaz na profil |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Kanál telemetrie |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (živé metriky) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Dotaz na profil |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Pokud aktuálně používáte [REST API Application Insights](https://dev.applicationinsights.io/
) , ke kterému se běžně používá API.ApplicationInsights.IO prostřednictvím "", budete muset použít koncový bod, který je místní pro vaši oblast:

|Oblast |  Název koncového bodu | Hodnota |
|-----------------|:------------|:-------------|
| Azure (Čína) | Rozhraní REST API | `api.applicationinsights.azure.cn` |
| Azure Government | Rozhraní REST API | `api.applicationinsights.us`|

> [!NOTE]
> Monitorování agenta bez kódu nebo rozšíření na základě rozšíření pro Azure App Services v tuto **chvíli není** v těchto oblastech podporováno. Jakmile bude tato funkce k dispozici, bude tento článek aktualizován.

## <a name="next-steps"></a>Další kroky

- Další informace o vlastních úpravách Azure Government najdete v podrobných pokynech k [monitorování a správě Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
- Další informace o Azure Číně najdete v [Azure Čína PlayBook](https://docs.microsoft.com/azure/china/).
