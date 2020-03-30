---
title: Přehledy aplikací Azure pro konzolové aplikace | Dokumenty společnosti Microsoft
description: Sledujte dostupnost, výkon a využití webových aplikací.
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: baaea0f8055eeff0314fcf5fde00729ea8091d12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655425"
---
# <a name="application-insights-for-net-console-applications"></a>Přehledy aplikací pro aplikace konzoly .NET

[Application Insights](../../azure-monitor/app/app-insights-overview.md) umožňuje sledovat dostupnost, výkon a využití webové aplikace.

Potřebujete předplatné s [Microsoft Azure](https://azure.com). Přihlaste se pomocí účtu Microsoft, který můžete mít pro Windows, Xbox Live nebo jiné cloudové služby Microsoftu. Váš tým může mít předplatné organizace pro Azure: požádejte vlastníka, aby vás do něj přidal pomocí vašeho účtu Microsoft.

> [!NOTE]
> K dispozici je nová sada Application Insights SDK s názvem [Microsoft.ApplicationInsights.WorkerService,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) která slouží k povolení přehledů aplikací pro všechny konzolové aplikace. Doporučuje se použít tento balíček a související pokyny [zde](../../azure-monitor/app/worker-service.md). Tento balíček se zaměřuje [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)na program , a proto jej lze použít v rozhraní .NET Core 2.0 nebo vyšší a rozhraní .NET Framework 4.7.2 nebo vyšší.

## <a name="getting-started"></a>Začínáme

* Na webu [Azure Portal](https://portal.azure.com)[vytvořte prostředek Application Insights](../../azure-monitor/app/create-new-resource.md). Pro typ aplikace zvolte **Obecné**.
* Zkopírujte klíč instrumentace. Klíč najdete v rozevíracím seznamu **Essentials** s novým zdrojem, který jste vytvořili.
* Nainstalujte nejnovější balíček [Microsoft.ApplicationInsights.](https://www.nuget.org/packages/Microsoft.ApplicationInsights)
* Před sledováním jakékoli telemetrie (nebo nastavení APPINSIGHTS_INSTRUMENTATIONKEY proměnné prostředí) nastavte v kódu klíč instrumentace. Poté byste měli být schopni ručně sledovat telemetrii a vidět ji na webu Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Telemetrie není odeslána okamžitě. Telemetrické položky jsou dávkově dávkovány a odesílány sadou ApplicationInsights SDK. V konzolových aplikacích, které `Track()` se ukončí hned po `Flush()` volání `Sleep` metod, nemusí být telemetrie odeslána, pokud a není provedena před ukončením aplikace, jak je znázorněno v [úplném příkladu](#full-example) dále v tomto článku.


* Nainstalujte nejnovější verzi balíčku [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) – automaticky sleduje volání HTTP, SQL nebo jiných externích závislostí.

Můžete inicializovat a nakonfigurovat Application `ApplicationInsights.config` Insights z kódu nebo pomocí souboru. Ujistěte se, že k inicializaci dojde co nejdříve. 

> [!NOTE]
> Pokyny odkazující na **ApplicationInsights.config** platí pouze pro aplikace, které cílí na rozhraní .NET Framework a nevztahují se na aplikace .NET Core.

### <a name="using-config-file"></a>Použití konfiguračního souboru

Ve výchozím nastavení sada Application `ApplicationInsights.config` Insights SDK `TelemetryConfiguration` hledá soubor v pracovním adresáři při vytváření

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Můžete také určit cestu k konfiguračnímu souboru.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Další informace naleznete v [tématu odkaz na konfigurační soubor](configuration-with-applicationinsights-config.md).

Úplný příklad konfiguračního souboru můžete získat instalací nejnovější verze balíčku [Microsoft.ApplicationInsights.WindowsServer.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) Zde je **minimální** konfigurace pro kolekci závislostí, která je ekvivalentní příkladkódu.

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
> Čtení konfiguračního souboru není v jádru .NET podporováno. Můžete zvážit použití [sady Application Insights SDK pro ASP.NET](../../azure-monitor/app/asp-net-core.md)

* Během spuštění aplikace vytvořit `DependencyTrackingTelemetryModule` a nakonfigurovat instanci - musí být singleton a musí být zachována po dobu životnosti aplikace.

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

* Přidání běžných inicializátorů telemetrie

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Pokud jste vytvořili `TelemetryConfiguration()` konfiguraci s prostý konstruktor, je třeba povolit podporu korelace navíc. **Není potřeba,** pokud čtete konfiguraci `TelemetryConfiguration.CreateDefault()` `TelemetryConfiguration.Active`ze souboru, použité nebo .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Můžete také nainstalovat a inicializovat modul kolektoru čítače výkonu, jak je popsáno [zde](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


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
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

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
* [Monitorování závislostí,](../../azure-monitor/app/asp-net-dependencies.md) abyste zjistili, jestli vás rest, SQL nebo jiné externí prostředky zpomalují.
* [Pomocí rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md) můžete odesílat vlastní události a metriky pro podrobnější zobrazení výkonu a využití aplikace.
