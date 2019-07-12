---
title: Azure Application Insights pro aplikace konzoly | Dokumentace Microsoftu
description: Monitorování webových aplikací pro dostupnost, výkon a využití.
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
ms.openlocfilehash: 0c2a28462633d47ad1d3f247793e3fcf6f4d40c0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795443"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights pro .NET konzolové aplikace
[Application Insights](../../azure-monitor/app/app-insights-overview.md) umožňuje monitorovat webové aplikace z hlediska dostupnosti, výkonu a využití.

Musíte mít předplatné s [Microsoft Azure](https://azure.com). Přihlaste se pomocí účtu Microsoft, které můžete mít zřízen pro Windows, Xbox Live a dalším cloudovým službám Microsoftu. Váš tým může mít předplatné pro společnosti do Azure: Požádejte vlastníka, aby vás přidal k ní pomocí svého účtu Microsoft.

## <a name="getting-started"></a>Začínáme

* Na webu [Azure Portal](https://portal.azure.com) [vytvořte prostředek Application Insights](../../azure-monitor/app/create-new-resource.md). Typ aplikace vyberte **Obecné**.
* Zkopírujte klíč instrumentace. Vyhledejte klíč v **Essentials** rozevíracího seznamu nový prostředek vytvořený. 
* Nainstalujte nejnovější [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) balíčku.
* Nastavení klíče instrumentace v kódu před sledování žádnou telemetrii (nebo nastavte proměnnou prostředí APPINSIGHTS_INSTRUMENTATIONKEY). Potom byste měli mít ručně sledovat telemetrii a podívejte se na webu Azure portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

* Nainstalujte nejnovější verzi [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) balíček – automaticky sleduje HTTP, SQL nebo některých jiných volání externích závislostí.

Může inicializace a konfigurace Application Insights z kódu nebo použití `ApplicationInsights.config` souboru. Zajistěte, aby se co nejdříve stane inicializace. 

> [!NOTE]
> Odkaz na pokyny **soubor ApplicationInsights.config** platí pouze pro aplikace, které cílí na rozhraní .NET Framework a neplatí pro aplikace .NET Core.

### <a name="using-config-file"></a>Pomocí konfiguračního souboru
Ve výchozím nastavení, Application Insights SDK hledá `ApplicationInsights.config` souboru v pracovním adresáři při `TelemetryConfiguration` se vytváří

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Můžete také zadat cesta ke konfiguračnímu souboru.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Další informace najdete v tématu [odkaz na soubor konfigurace](configuration-with-applicationinsights-config.md).

Úplný příklad konfiguračního souboru se může zobrazit nainstalováním nejnovější verze [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) balíčku. Tady je **minimální** konfigurace závislostí kolekce, které se rovná v příkladu kódu.

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

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurace shromažďování telemetrie z kódu
> [!NOTE]
> Čtení konfiguračního souboru není podporován v rozhraní .NET Core. Můžete zvážit použití [Application Insights SDK pro ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* Při spuštění aplikace vytvořit a nakonfigurovat `DependencyTrackingTelemetryModule` instance – musí být typu singleton a pro dobu životnosti aplikace musí být zachovány.

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

* Přidat inicializátory běžné telemetrie

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Pokud jste vytvořili konfiguraci s prostým `TelemetryConfiguration()` konstruktor, musíte kromě povolení podpory korelace. **Není potřeba** Pokud čtení konfigurace ze souboru používá `TelemetryConfiguration.CreateDefault()` nebo `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Můžete také chtít nainstalovat a inicializovat modul čítače výkonu kolekcí, jak je popsáno [zde](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


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

            var telemetryClient = new TelemetryClient();
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

## <a name="next-steps"></a>Další postup
* [Monitorovat závislosti](../../azure-monitor/app/asp-net-dependencies.md) zobrazíte, pokud REST, SQL nebo jiných externích prostředků vás zpomalují.
* [Použití rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md) k odesílání vlastních událostí a metrik pro podrobnější přehled výkonu a využití vaší aplikace.
