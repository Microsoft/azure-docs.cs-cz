---
title: Přehledy aplikací pro aplikace pracovních služeb (aplikace bez HTTP)
description: Monitorování aplikací .NET Core/.NET Framework bez HTTP pomocí Přehledů aplikací Azure Monitor.
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f043140e5a342d114f777ad16bba588790b7f8cc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536722"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Přehledy aplikací pro aplikace pracovních služeb (aplikace bez http)

Application Insights vydává novou sdk, nazvanou `Microsoft.ApplicationInsights.WorkerService`, která je nejvhodnější pro úlohy bez protokolu HTTP, jako je zasílání zpráv, úlohy na pozadí, konzolové aplikace atd. Tyto typy aplikací nemají pojem příchozí požadavek HTTP jako tradiční ASP.NET/ASP.NET základní webové aplikace, a proto použití balíčků Application Insights pro [ASP.NET](asp-net.md) nebo [ASP.NET základní](asp-net-core.md) aplikace není podporována.

Nová sada SDK neprovádí žádnou kolekci telemetrie sama o sobě. Místo toho přináší další známé automatické kolektory Application Insights, jako [je DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) atd. Tato sada SDK zveřejňuje `IServiceCollection` metody rozšíření na povolit a konfigurovat kolekci telemetrie.

## <a name="supported-scenarios"></a>Podporované scénáře

Sada [Application Insights SDK pro pracovní službu](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) je nejvhodnější pro aplikace bez protokolu HTTP bez ohledu na to, kde a jak jsou spuštěny. Pokud je vaše aplikace spuštěná a má připojení k síti k Azure, telemetrie se dá shromažďovat. Monitorování Přehledů aplikací je podporováno všude, kde je podporováno jádro .NET Core. Tento balíček lze použít v nově zavedené [pracovní službě .NET Core 3.0](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [úlohách na pozadí v Asp.Net Core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), konzolových aplikacích (.NET Core/ .NET Framework) atd.

## <a name="prerequisites"></a>Požadavky

Platný klíč instrumentace Application Insights. Tento klíč je nutné odeslat všechny telemetrie application insights. Pokud potřebujete vytvořit nový prostředek Application Insights, abyste získali klíč instrumentace, přečtěte si informace [o vytvoření prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="using-application-insights-sdk-for-worker-services"></a>Použití sady SDK s přehledy aplikací pro pracovní služby

1. Nainstalujte do aplikace balíček [Microsoft.ApplicationInsights.WorkerService.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)
   Následující úryvek zobrazuje změny, které je třeba přidat `.csproj` do souboru projektu.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Metoda `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` rozšíření `IServiceCollection`volání na , poskytuje klíč instrumentace. Tato metoda by měla být volána na začátku aplikace. Přesné umístění závisí na typu aplikace.

1. Načíst `ILogger` instanci nebo `TelemetryClient` instanci z kontejneru `serviceProvider.GetRequiredService<TelemetryClient>();` vkládání závislostí (DI) voláním nebo pomocí vkládání konstruktoru. Tento krok spustí nastavení `TelemetryConfiguration` a automatické kolekce modulů.

Konkrétní pokyny pro každý typ aplikace jsou popsány v následujících oddílech.

## <a name="net-core-30-worker-service-application"></a>Aplikace pracovních služeb .NET Core 3.0

Zde je sdílen úplný [příklad.](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Stažení a instalace [rozhraní .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Vytvoření nového projektu pracovní služby pomocí nové šablony projektu sady Visual Studio nebo příkazového řádku`dotnet new worker`
3. Nainstalujte do aplikace balíček [Microsoft.ApplicationInsights.WorkerService.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)

4. Přidejte `services.AddApplicationInsightsTelemetryWorkerService();` `CreateHostBuilder()` do metody `Program.cs` ve třídě, jako v tomto příkladu:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Upravte `Worker.cs` si podle níže uvedeného příkladu.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Nastavte klíč instrumentace.

    I když můžete zadat instrumentace `AddApplicationInsightsTelemetryWorkerService`klíč jako argument , doporučujeme zadat klíč instrumentace v konfiguraci. Následující ukázka kódu ukazuje, jak zadat `appsettings.json`klíč instrumentace v aplikaci . Ujistěte `appsettings.json` se, že je zkopírován do kořenové složky aplikace během publikování.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Případně zadejte klíč instrumentace v jedné z následujících proměnných prostředí.
`APPINSIGHTS_INSTRUMENTATIONKEY` nebo `ApplicationInsights:InstrumentationKey`

Příklad: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
Nebo`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Obvykle `APPINSIGHTS_INSTRUMENTATIONKEY` určuje klíč instrumentace pro aplikace nasazené do webových aplikací jako webové úlohy.

> [!NOTE]
> Instrumentační klíč zadaný v kódu `APPINSIGHTS_INSTRUMENTATIONKEY`vítězí nad proměnnou prostředí , která vyhrává nad jinými možnostmi.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET základní úlohy na pozadí s hostovanými službami

[Tento](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) dokument popisuje, jak vytvořit úlohy na pozadí v aplikaci ASP.NET Core 2.1/2.2.

Zde je sdílen úplný [příklad.](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Nainstalujte balíček Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) do aplikace.
2. Přidejte `services.AddApplicationInsightsTelemetryWorkerService();` `ConfigureServices()` k metodě, jako v tomto příkladu:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

Následuje kód, `TimedHostedService` kde se nachází logika úlohy na pozadí.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Nastavte klíč instrumentace.
   Použijte totéž `appsettings.json` z výše uvedeného příkladu služby pracovního procesu .NET Core 3.0.

## <a name="net-corenet-framework-console-application"></a>Aplikace .NET Core/.NET Framework Console

Jak je uvedeno na začátku tohoto článku, nový balíček lze použít k povolení Application Insights Telemetrie z i běžné konzolové aplikace. Tento balíček se zaměřuje [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)na aplikace , a proto jej lze použít pro konzolové aplikace v rozhraní .NET Core 2.0 nebo vyšší a rozhraní .NET Framework 4.7.2 nebo vyšší.

Zde je sdílen úplný [příklad.](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Nainstalujte balíček Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) do aplikace.

2. Upravte Program.cs jako následující příklad.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Tato konzolová aplikace také `TelemetryConfiguration`používá stejné výchozí a lze ji přizpůsobit stejným způsobem jako příklady v předchozí části.

## <a name="run-your-application"></a>Spusťte aplikaci

Spusťte aplikaci. Příklad pracovníků ze všech výše uvedených dělá http volání každou sekundu bing.com, a také vydává několik protokolů pomocí ILogger. Tyto řádky jsou `StartOperation` zabaleny `TelemetryClient`uvnitř volání , který se používá `RequestTelemetry` k vytvoření operace (v tomto příkladu s názvem "operace"). Application Insights bude shromažďovat tyto protokoly ILogger (upozornění nebo výše ve výchozím nastavení) a závislosti a budou korelovány `RequestTelemetry` s vztah em-parent-child. Korelace také funguje meziproces/hranice sítě. Například pokud bylo volání provedeno do jiné monitorované součásti, bude korelováno s tímto nadřazeným objektem.

Tuto vlastní `RequestTelemetry` operaci aplikace si lze dozvat jako ekvivalent příchozí ho webového požadavku v typické webové aplikaci. I když není nutné použít Operaci, nejlépe se hodí s [datovým modelem korelace Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/correlation) – s `RequestTelemetry` tím, že funguje jako nadřazená operace a každá telemetrická data generovaná uvnitř iterace pracovního procesu jsou považována za logicky patřící do stejné operace. Tento přístup také zajišťuje, že všechny generované telemetrie (automatické a ruční) budou mít stejné `operation_id`. Jako vzorkování `operation_id`je založena na , vzorkování algoritmus buď udržuje nebo zahodí všechny telemetrie z jedné iterace.

V následujícím seznamu je uvedena úplná telemetrická data automaticky shromážděná application insights.

### <a name="live-metrics"></a>Live Metrics

[Živé metriky](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) lze rychle ověřit, zda je monitorování přehledů aplikací správně nakonfigurováno. Zatímco to může trvat několik minut, než se telemetrie začne zobrazovat na portálu a v analýzách, živé metriky by zobrazovaly využití procesoru spuštěného procesu téměř v reálném čase. Může také zobrazit další telemetrie, jako jsou požadavky, závislosti, trasování atd.

### <a name="ilogger-logs"></a>Protokoly ILogger

Protokoly vyzařované `ILogger` prostřednictvím `Warning` závažnosti nebo vyšší jsou automaticky zachyceny. Postupujte [podle ILogger docs](ilogger.md#control-logging-level) přizpůsobit, které úrovně protokolu jsou zachyceny Application Insights.

### <a name="dependencies"></a>Závislosti

Kolekce závislostí je ve výchozím nastavení povolena. [Tento](asp-net-dependencies.md#automatically-tracked-dependencies) článek vysvětluje závislosti, které jsou automaticky shromažďovány a také obsahují kroky k ručnímu sledování.

### <a name="eventcounter"></a>Čítač událostí

`EventCounterCollectionModule`je ve výchozím nastavení povolena a bude shromažďovat výchozí sadu čítačů z aplikací .NET Core 3.0. Kurz [EventCounter](eventcounters.md) uvádí výchozí sadu čítačů shromážděných. Má také pokyny pro přizpůsobení seznamu.

### <a name="manually-tracking-additional-telemetry"></a>Ruční sledování další telemetrie

Zatímco sada SDK automaticky shromažďuje telemetrická data, jak je vysvětleno výše, ve většině případů bude uživatel muset odeslat další telemetrii službě Application Insights. Doporučený způsob, jak sledovat další telemetrická `TelemetryClient` data je získání instance z vkládání `TrackXXX()` závislostí a volání jedné z podporovaných metod [rozhraní API](api-custom-events-metrics.md) na něm. Dalším typickým [případem](custom-operations-tracking.md)použití je vlastní sledování operací . Tento přístup je demonstrován v příkladech pracovníka výše.

## <a name="configure-the-application-insights-sdk"></a>Konfigurace sady SDK SDK přehledů aplikací

Výchozí `TelemetryConfiguration` hodnota používaná sadou SDK pracovní služby je podobná automatické konfiguraci používané v aplikaci ASP.NET nebo `HttpContext`ASP.NET Core, mínus telemetrické initializátory používané k obohacení telemetrie z .

Chcete-li změnit výchozí konfiguraci, můžete přizpůsobit sadu Application Insights SDK pro pracovní službu. Uživatelé sady Application Insights ASP.NET core sdk mohou být obeznámeni se změnou konfigurace pomocí integrovaného [vkládání závislostí](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)ASP.NET Jádra . WorkerService SDK je také založena na podobné principy. Proveďte téměř všechny `ConfigureServices()` změny konfigurace v `IServiceCollection`sekci voláním vhodných metod na , jak je podrobně popsáno níže.

> [!NOTE]
> Při použití této sady SDK `TelemetryConfiguration.Active` není změna konfigurace úpravou podporována a změny se neprojeví.

### <a name="using-applicationinsightsserviceoptions"></a>Použití applicationinsightsmožnosti

Můžete upravit několik běžných `ApplicationInsightsServiceOptions` nastavení `AddApplicationInsightsTelemetryWorkerService`předáním do , jako v tomto příkladu:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Všimněte `ApplicationInsightsServiceOptions` si, že v této `Microsoft.ApplicationInsights.WorkerService` sadě SDK je v oboru názvů na `Microsoft.ApplicationInsights.AspNetCore.Extensions` rozdíl od v ASP.NET základní sady SDK.

Běžně používaná nastavení`ApplicationInsightsServiceOptions`

|Nastavení | Popis | Výchozí
|---------------|-------|-------
|PovolitQuickPulseMetricStream | Povolit nebo zakázat funkci LiveMetrics | true
|Povolit adaptivní vzorkování | Povolit nebo zakázat adaptivní vzorkování | true
|EnableHeartbeat | Funkce Povolit nebo zakázat prezenční signály, která pravidelně (15min default) odesílá vlastní metriku s názvem "HeartBeatState" s informacemi o běhu, jako je verze .NET, informace o prostředí Azure, pokud je to možné, atd. | true
|AddAutoCollectedMetricExtractor | Povolit nebo zakázat AutoCollectedMetrics extraktor, což je TelemetryProcessor, který odesílá předem agregované metriky o požadavky/závislosti před odběr vzorků probíhá. | true

Podívejte se na [konfigurovatelné nastavení v `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) nejaktuálnějším seznamu.

### <a name="sampling"></a>Vzorkování

Sada Application Insights SDK pro pracovní službu podporuje protokol ování s pevnou rychlostí i adaptivní vzorkování. Adaptivní vzorkování je ve výchozím nastavení povoleno. Konfigurace vzorkování pro službu pracovního procesu se provádí stejným způsobem jako pro [ASP.NET základní aplikace](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Přidání telemetrických inicializátorů

[Telemetrické inicializátory](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) použijte, pokud chcete definovat vlastnosti, které jsou odesílány se všemi telemetry.

Přidejte `TelemetryInitializer` do `DependencyInjection` kontejneru všechny nové a sada `TelemetryConfiguration`SDK je automaticky přidá do aplikace .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Odebrání telemetrických inicializátorů

Telemetrické inicializátory jsou k dispozici ve výchozím nastavení. Chcete-li odebrat všechny nebo konkrétní telemetrie inicializátory, použijte následující ukázkový kód *po* volání `AddApplicationInsightsTelemetryWorkerService()`.

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Přidání telemetrických procesorů

Vlastní telemetrické procesory můžete `TelemetryConfiguration` přidat pomocí `AddApplicationInsightsTelemetryProcessor` metody `IServiceCollection`rozšíření na . Telemetrické procesory v [pokročilých scénářích filtrování](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) umožňují přímou kontrolu nad tím, co je zahrnuto nebo vyloučeno z telemetrie, kterou odešlete do služby Application Insights. Použijte následující příklad.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurace nebo odebrání výchozích modulů telemetrie

Application Insights používá telemetrické moduly k automatickému shromažďování telemetrie o konkrétních úlohách bez nutnosti ručního sledování.

Následující moduly automatického sběru jsou ve výchozím nastavení povoleny. Tyto moduly jsou zodpovědné za automatické shromažďování telemetrie. Můžete je zakázat nebo nakonfigurovat tak, aby měnily své výchozí chování.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Chcete-li `TelemetryModule`nakonfigurovat výchozí `ConfigureTelemetryModule<T>` nastavení `IServiceCollection`, použijte metodu rozšíření na , jak je znázorněno v následujícím příkladu.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Konfigurace telemetrického kanálu

Výchozí kanál `ServerTelemetryChannel`je . Můžete přepsat, jak ukazuje následující příklad.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Dynamicky zakázat telemetrii

Pokud chcete zakázat telemetrie podmíněně a `TelemetryConfiguration` dynamicky, můžete vyřešit instanci s `DisableTelemetry` ASP.NET core vkládání závislostí kontejnerkdekoli v kódu a nastavit příznak na něm.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Jak lze sledovat telemetrii, která se neshromažďuje automaticky?

Získejte instanci `TelemetryClient` pomocí vkládání konstruktoru `TrackXXX()` a volání požadované metody na něm. Nedoporučujeme vytvářet nové `TelemetryClient` instance. Singleton instance `TelemetryClient` je již registrována v `DependencyInjection` `TelemetryConfiguration` kontejneru, který sdílí se zbytkem telemetrie. Vytvoření nové `TelemetryClient` instance se doporučuje pouze v případě, že potřebuje konfiguraci, která je oddělená od zbytku telemetrie.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Je možné použít ide sady Visual Studio k zasuzuní přehledů aplikací do projektu služby pracovního procesu?

Připojení IDE sady Visual Studio je aktuálně podporováno pouze pro ASP.NET/ASP.NET základní aplikace. Tento dokument bude aktualizován, když Visual Studio dodává podporu pro připojení pracovní aplikace služby.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Můžu povolit monitorování přehledů aplikací pomocí nástrojů, jako je Sledování stavu?

Ne. [Sledování stavu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) a [Sledování stavu v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) aktuálně podporují pouze ASP.NET 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Pokud spustím svou aplikaci v Linuxu, jsou podporovány všechny funkce?

Ano. Podpora funkcí pro tuto sdk je stejná ve všech platformách, s následujícími výjimkami:

* Čítače výkonu jsou podporovány pouze v systému Windows s výjimkou procesního procesoru/paměti zobrazené v živých metrikách.
* I `ServerTelemetryChannel` když je ve výchozím nastavení povolena, pokud je aplikace spuštěna v Linuxu nebo MacOS, kanál automaticky nevytvoří složku místního úložiště, aby dočasně uchovával telemetrii, pokud dojde k problémům se sítí. Z důvodu tohoto omezení telemetrie je ztracena, pokud existují dočasné problémy se sítí nebo serverem. Chcete-li tento problém vyřešit, nakonfigurujte místní složku kanálu:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Ukázkové aplikace

[Aplikace základní konzoly .NET](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Tuto ukázku použijte, pokud používáte konzolovou aplikaci napsanou v rozhraní .NET Core (2.0 nebo vyšší) nebo .NET Framework (4.7.2 nebo vyšší)

[Úlohy základního pozadí technologie ASP .NET s hostedservices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Tuto ukázku použijte, pokud jste v Asp.Net Core 2.1/2.2 a vytváříte úlohy na pozadí podle oficiálních pokynů [zde](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[Pracovní služba .NET Core 3.0](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Tuto ukázku použijte, pokud máte aplikaci .NET Core 3.0 Worker Service podle oficiálních pokynů [zde](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>Sada SDK s otevřeným zdrojovým kódem

[Přečtěte si a přispějte ke kódu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Další kroky

* [Pomocí rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md) můžete odesílat vlastní události a metriky pro podrobné zobrazení výkonu a využití aplikace.
* [Sledování dalších závislostí, které nejsou automaticky sledovány](../../azure-monitor/app/auto-collect-dependencies.md).
* [Obohaťte nebo filtrujte automaticky shromážděnou telemetrii](../../azure-monitor/app/api-filtering-sampling.md).
* [Vkládání závislostí v ASP.NET core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
