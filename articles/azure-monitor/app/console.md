---
title: Azure Application Insights pro konzolové aplikace | Microsoft Docs
description: Monitorujte webové aplikace pro účely dostupnosti, výkonu a využití.
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp
ms.reviewer: lmolkova
ms.openlocfilehash: aa39a1eca04621fc4db75f755402d3679403e814
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920590"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights pro konzolové aplikace .NET

[Application Insights](./app-insights-overview.md) umožňuje monitorovat webovou aplikaci pro účely dostupnosti, výkonu a využití.

K [Microsoft Azure](https://azure.com)potřebujete předplatné. Přihlaste se pomocí účet Microsoft, kterou můžete potřebovat pro Windows, Xbox Live nebo jiné cloudové služby Microsoftu. Váš tým může mít k Azure předplatné organizace: Požádejte vlastníka, aby vás do něho přidal pomocí účet Microsoft.

> [!NOTE]
> [Pro všechny](./worker-service.md) konzolové aplikace se *důrazně doporučuje* použít balíček [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) a související pokyny. Tento balíček cílí [`NetStandard2.0`](/dotnet/standard/net-standard) , a proto se dá použít v .NET Core 2,1 nebo vyšší a .NET Framework 4.7.2 nebo novější.

## <a name="getting-started"></a>Začínáme

> [!IMPORTANT]
> Nové oblasti Azure **vyžadují** použití připojovacích řetězců místo klíčů instrumentace. [Připojovací řetězec](./sdk-connection-string.md?tabs=net) identifikuje prostředek, ke kterému chcete přidružit data telemetrie. Umožňuje také upravit koncové body, které prostředek použije jako cíl pro vaši telemetrii. Budete muset zkopírovat připojovací řetězec a přidat ho do kódu aplikace nebo do proměnné prostředí.

* Na webu [Azure Portal](https://portal.azure.com)[vytvořte prostředek Application Insights](./create-new-resource.md). Jako typ aplikace vyberte **Obecné**.
* Zkopírujte klíč instrumentace. Vyhledejte klíč v rozevíracím seznamu **základy** nového prostředku, který jste vytvořili.
* Nainstalujte nejnovější balíček [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) .
* Před sledováním jakékoli telemetrie (nebo nastavením APPINSIGHTS_INSTRUMENTATIONKEY proměnné prostředí) nastavte v kódu klíč instrumentace. Potom byste měli být schopni ručně sledovat telemetrii a vidět ji na Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Telemetrii se neposílá okamžitě. Položky telemetrie jsou v sadě ApplicationInsights SDK a posílány pomocí dávek. V konzolových aplikacích, které se ukončí hned po volání `Track()` metod, telemetrie nemůžete odeslat, pokud `Flush()` a `Sleep` / `Delay` není provedeno před ukončením aplikace, jak je uvedeno v [úplném příkladu](#full-example) dále v tomto článku. `Sleep` není vyžadováno, pokud používáte `InMemoryChannel` . Existuje aktivní problém týkající se potřeby, `Sleep` které tady sledujeme: [ApplicationInsights-dotnet/problémy/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* Nainstalovat nejnovější verzi balíčku [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) – automaticky sleduje http, SQL nebo některá další volání vnějších závislostí.

Můžete inicializovat a konfigurovat Application Insights z kódu nebo pomocí `ApplicationInsights.config` souboru. Ujistěte se, že k inicializaci dojde co nejdříve. 

> [!NOTE]
> Pokyny týkající se **ApplicationInsights.config** jsou použitelné pouze pro aplikace, které cílí na .NET Framework a nevztahují se na aplikace .NET Core.

### <a name="using-config-file"></a>Použití konfiguračního souboru

Ve výchozím nastavení Application Insights SDK `ApplicationInsights.config` při vytváření v pracovním adresáři vyhledá soubor `TelemetryConfiguration` .

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
> Čtení konfiguračního souboru není v rozhraní .NET Core podporováno. Můžete zvážit použití [Application Insights SDK pro ASP.NET Core](./asp-net-core.md)

* Při spuštění aplikace vytvořte a nakonfigurujte `DependencyTrackingTelemetryModule` instanci – musí být typu Singleton a musí být zachována pro dobu života aplikace.

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

Pokud jste vytvořili konfiguraci pomocí jednoduchého `TelemetryConfiguration()` konstruktoru, je nutné povolit podporu korelace navíc. Není **nutné** , pokud si přečtete konfiguraci ze souboru, používá se `TelemetryConfiguration.CreateDefault()` nebo `TelemetryConfiguration.Active` .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Můžete také chtít nainstalovat a inicializovat modul sběrače čítače výkonu, jak je popsáno [zde](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/) .


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

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
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
* [Sledujte závislosti](./asp-net-dependencies.md) , abyste viděli, jestli REST, SQL nebo jiné externí prostředky zpomalují vaši práci.
* [Pomocí rozhraní API](./api-custom-events-metrics.md) můžete odesílat vlastní události a metriky pro podrobnější přehled o výkonu a využití vaší aplikace.

