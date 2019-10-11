---
title: Azure Application Insights pro konzolové aplikace | Microsoft Docs
description: Monitorujte webové aplikace pro účely dostupnosti, výkonu a využití.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 53a765cd2e71b5b1eb1ac2c70506fd55aec6736e
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274132"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights pro konzolové aplikace .NET

[Application Insights](../../azure-monitor/app/app-insights-overview.md) umožňuje monitorovat webovou aplikaci pro účely dostupnosti, výkonu a využití.

K [Microsoft Azure](https://azure.com)potřebujete předplatné. Přihlaste se pomocí účet Microsoft, kterou můžete potřebovat pro Windows, Xbox Live nebo jiné cloudové služby Microsoftu. Váš tým může mít k Azure předplatné organizace: Požádejte vlastníka, aby vás do něho přidal pomocí účet Microsoft.

> [!NOTE]
> K dispozici je nová verze beta Application Insights SDK označovaná jako [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , která se dá použít k povolení Application Insights pro jakékoli konzolové aplikace. Doporučuje se použít tento balíček a související pokyny [odsud.](../../azure-monitor/app/worker-service.md) Tento balíček cílí na [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard), a proto je možné ho použít v .net Core 2,0 nebo vyšší a .NET Framework 4.7.2 nebo vyšší.
Po vydání stabilní verze tohoto nového balíčku bude tento dokument zastaralý.

## <a name="getting-started"></a>Začínáme

* V [Azure Portal](https://portal.azure.com) [vytvořte prostředek Application Insights](../../azure-monitor/app/create-new-resource.md). Jako typ aplikace vyberte **Obecné**.
* Poznamenejte si kopii klíče instrumentace. Vyhledejte klíč v rozevíracím seznamu **základy** nového prostředku, který jste vytvořili. 
* Nainstalujte nejnovější balíček [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) .
* Před sledováním jakékoli telemetrie (nebo nastavením proměnné prostředí APPINSIGHTS_INSTRUMENTATIONKEY) nastavte klíč instrumentace ve vašem kódu. Potom byste měli být schopni ručně sledovat telemetrii a vidět ji na Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

* Nainstalovat nejnovější verzi balíčku [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) – automaticky sleduje http, SQL nebo některá další volání vnějších závislostí.

Můžete inicializovat a konfigurovat Application Insights z kódu nebo pomocí souboru `ApplicationInsights.config`. Ujistěte se, že k inicializaci dojde co nejdříve. 

> [!NOTE]
> Pokyny odkazující na **ApplicationInsights. config** platí jenom pro aplikace, které cílí na .NET Framework a nevztahují se na aplikace .NET Core.

### <a name="using-config-file"></a>Použití konfiguračního souboru
Ve výchozím nastavení vyhledává sada Application Insights SDK při vytváření `TelemetryConfiguration` soubor `ApplicationInsights.config` v pracovním adresáři.

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Můžete také zadat cestu ke konfiguračnímu souboru.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Další informace najdete v tématu [Referenční dokumentace ke konfiguračnímu souboru](configuration-with-applicationinsights-config.md).

Úplný příklad konfiguračního souboru můžete získat instalací nejnovější verze balíčku [Microsoft. ApplicationInsights. windowsserver](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) . Zde je **minimální** konfigurace pro kolekci závislostí, která je ekvivalentní k příkladu kódu.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurace kolekce telemetrie z kódu
> [!NOTE]
> Čtení konfiguračního souboru není v rozhraní .NET Core podporováno. Můžete zvážit použití [Application Insights SDK pro ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* Při spuštění aplikace se vytvoří a nakonfigurují instance `DependencyTrackingTelemetryModule` – musí být typu Singleton a musí se zachovat pro dobu života aplikace.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Přidat běžné Inicializátory telemetrie

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Pokud jste vytvořili konfiguraci pomocí jednoduchého konstruktoru `TelemetryConfiguration()`, je nutné povolit podporu korelace navíc. Není **potřeba** , pokud načtete konfiguraci ze souboru, který se používá `TelemetryConfiguration.CreateDefault()` nebo `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Můžete také chtít nainstalovat a inicializovat modul sběrače čítače výkonu, jak je popsáno [zde](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/) .


#### <a name="full-example"></a>Úplný příklad

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Další kroky
* [Sledujte závislosti](../../azure-monitor/app/asp-net-dependencies.md) , abyste viděli, jestli REST, SQL nebo jiné externí prostředky zpomalují vaši práci.
* [Pomocí rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md) můžete odesílat vlastní události a metriky pro podrobnější přehled o výkonu a využití vaší aplikace.
