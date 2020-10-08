---
title: Application Insights pro aplikace pracovní služby Worker (jiné aplikace než HTTP)
description: Monitorování aplikací .NET Core/. NET Framework bez protokolu HTTP pomocí Azure Monitor Application Insights.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.openlocfilehash: 643edf81d6a98c8f423267b657feb9dfb6da1070
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816396"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights pro aplikace služby Worker (aplikace jiného typu než HTTP)

[Sada Application Insights SDK for Worker Service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) je nová sada SDK, která je nejvhodnější pro úlohy jiné než HTTP, jako je zasílání zpráv, úlohy na pozadí, konzolové aplikace atd. Tyto typy aplikací nemají pojem příchozího požadavku HTTP, jako je tradiční webová aplikace ASP.NET/ASP.NET Core, a proto použití balíčků Application Insights pro [ASP.NET](asp-net.md) nebo aplikace [ASP.NET Core](asp-net-core.md) není podporované.

Nová sada SDK nedělá žádné kolekce telemetrie sám o sobě. Místo toho přináší další známé Application Insights automatické shromažďování, jako je [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) atd. Tato sada SDK zpřístupňuje metody rozšíření `IServiceCollection` pro povolení a konfiguraci kolekce telemetrie.

## <a name="supported-scenarios"></a>Podporované scénáře

[Služba Application Insights SDK for Worker](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) se nejlépe hodí pro aplikace bez protokolu HTTP bez ohledu na to, kde a jak se spouštějí. Pokud vaše aplikace běží a má síťové připojení k Azure, je možné shromažďovat telemetrii. Monitorování Application Insights je podporováno všude, kde je podporováno rozhraní .NET Core. Tento balíček se dá použít v nově zavedené [službě .NET Core 3,0 Worker](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [úlohy na pozadí v ASP.NET Core 2.1/2.2](/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&preserve-view=true), konzolové aplikace (.NET Core/.NET Framework) atd.

## <a name="prerequisites"></a>Předpoklady

Platný klíč instrumentace Application Insights. Tento klíč je nutný k odeslání jakékoli telemetrie do Application Insights. Pokud potřebujete vytvořit nový prostředek Application Insights, abyste získali klíč instrumentace, přečtěte si téma [vytvoření prostředku Application Insights](./create-new-resource.md).

## <a name="using-application-insights-sdk-for-worker-services"></a>Používání sady Application Insights SDK pro služby pracovních procesů

1. Nainstalujte do aplikace balíček [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) .
   Následující fragment kódu ukazuje změny, které je třeba přidat do `.csproj` souboru projektu.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Zavolejte `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` metodu rozšíření on `IServiceCollection` a poskytněte klíč instrumentace. Tato metoda by měla být volána na začátku aplikace. Přesné umístění závisí na typu aplikace.

1. Načtěte `ILogger` instanci nebo `TelemetryClient` instanci z kontejneru vkládání závislostí (di) voláním `serviceProvider.GetRequiredService<TelemetryClient>();` nebo použitím injektáže konstruktoru. Tento krok spustí nastavení `TelemetryConfiguration` a automatické moduly kolekcí.

Konkrétní pokyny pro jednotlivé typy aplikací jsou popsány v následujících částech.

## <a name="net-core-30-worker-service-application"></a>Aplikace služby pracovního procesu .NET Core 3,0

Úplný příklad se [tady](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) sdílí.

1. Stažení a instalace [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Vytvoření nového projektu služby pracovního procesu buď pomocí šablony nového projektu sady Visual Studio nebo příkazového řádku `dotnet new worker`
3. Nainstalujte do aplikace balíček [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) .

4. Přidejte `services.AddApplicationInsightsTelemetryWorkerService();` do `CreateHostBuilder()` metody ve `Program.cs` třídě, jako v tomto příkladu:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Upravte `Worker.cs` podle níže uvedeného příkladu.

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

    I když můžete zadat klíč instrumentace jako argument pro, doporučujeme `AddApplicationInsightsTelemetryWorkerService` zadat klíč instrumentace v konfiguraci. Následující ukázka kódu ukazuje, jak zadat klíč instrumentace v `appsettings.json` . Ujistěte se, že `appsettings.json` se během publikování kopíruje do kořenové složky aplikace.

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

Případně můžete zadat klíč instrumentace v některé z následujících proměnných prostředí.
`APPINSIGHTS_INSTRUMENTATIONKEY` nebo `ApplicationInsights:InstrumentationKey`

Příklad: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
ANI `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Obvykle `APPINSIGHTS_INSTRUMENTATIONKEY` Určuje klíč instrumentace pro aplikace nasazené pro Web Apps jako webové úlohy.

> [!NOTE]
> Klíč instrumentace zadaný v kódu služby WINS přes proměnnou prostředí `APPINSIGHTS_INSTRUMENTATIONKEY` , která je službou WINS nad jinými možnostmi.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Core úlohy na pozadí pomocí hostovaných služeb

[Tento](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio&view=aspnetcore-2.2&preserve-view=true) dokument popisuje, jak vytvořit úlohy na pozadí v aplikaci ASP.NET Core 2.1/2.2.

Úplný příklad se [tady](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) sdílí.

1. Nainstalujte do aplikace balíček [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) .
2. Přidejte `services.AddApplicationInsightsTelemetryWorkerService();` do `ConfigureServices()` metody, jako v tomto příkladu:

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
   Použijte stejný `appsettings.json` příklad jako ve výše uvedeném příkladu .NET Core 3,0 Worker Service.

## <a name="net-corenet-framework-console-application"></a>Konzolová aplikace .NET Core/. NET Framework

Jak je uvedeno na začátku tohoto článku, můžete nový balíček použít k povolení Telemetrie Application Insights z konzoly i z běžné konzolové aplikace. Tento balíček cílí [`NetStandard2.0`](/dotnet/standard/net-standard) , a proto se dá použít pro konzolové aplikace v .NET Core 2,0 nebo vyšší a .NET Framework 4.7.2 nebo vyšší.

Úplný příklad se [tady](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) sdílí.

1. Nainstalujte do aplikace balíček [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) .

2. Upravte Program.cs podle níže uvedeného příkladu.

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

Tato Konzolová aplikace také používá stejnou výchozí hodnotu `TelemetryConfiguration` a lze ji přizpůsobit stejným způsobem jako příklady v předchozí části.

## <a name="run-your-application"></a>Spusťte aplikaci

Spusťte aplikaci. Ukázkové pracovní procesy ze všech výše uvedených provedly volání http každou sekundu do bing.com a také vygenerují několik protokolů pomocí `ILogger` . Tyto řádky jsou zabaleny uvnitř `StartOperation` volání `TelemetryClient` , které se používá k vytvoření operace (v tomto příkladu `RequestTelemetry` s názvem "operace"). Application Insights shromáždí tyto protokoly ILogger (ve výchozím nastavení je to upozornění nebo vyšší) a závislosti a bude koreluje `RequestTelemetry` s relací nadřazenosti a podřízenosti. Korelace taky funguje na hranici mezi procesy a sítě. Například pokud bylo volání provedeno na jinou monitorovanou součást, bude koreluje také s tímto nadřazeným prvkem.

Tato vlastní operace `RequestTelemetry` se dá představit jako ekvivalent příchozího webového požadavku v typické webové aplikaci. I když není nutné použít operaci, je nejlepší pro [Application Insights relační datový model](./correlation.md) `RequestTelemetry` , který funguje jako nadřazená operace, a každá telemetrie vygenerovaná v rámci iterace pracovního procesu se považuje za logickou, která patří do stejné operace. Tento přístup také zajišťuje, že všechna vygenerovaná telemetrie (automatická a ruční) bude mít stejnou `operation_id` . Když je vzorkování založené na `operation_id` , algoritmus vzorkování buď udržuje, nebo vyřazuje veškerou telemetrii z jedné iterace.

Následující seznam uvádí úplnou telemetrii automaticky shromážděnou nástrojem Application Insights.

### <a name="live-metrics"></a>Live Metrics

[Živé metriky](./live-stream.md) lze použít k rychlému ověření, zda je monitorování Application Insights správně nakonfigurováno. I když může trvat několik minut, než se telemetrie spustí na portálu a v analýze, zobrazí se v reálném čase využití CPU běžícího procesu téměř v reálném čase. Může také zobrazit další telemetrie, jako jsou požadavky, závislosti, trasování atd.

### <a name="ilogger-logs"></a>Protokoly ILogger

Protokoly emitované pomocí `ILogger` závažnosti `Warning` nebo většího jsou zachyceny automaticky. Dodržujte [ILogger docs](ilogger.md#control-logging-level) k přizpůsobení, které úrovně protokolu jsou zachyceny Application Insights.

### <a name="dependencies"></a>Závislosti

Kolekce závislostí je ve výchozím nastavení povolená. [Tento](asp-net-dependencies.md#automatically-tracked-dependencies) článek vysvětluje závislosti, které jsou shromažďovány automaticky, a obsahuje také Postup ručního sledování.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` je ve výchozím nastavení povolená a bude shromažďovat výchozí sadu čítačů z aplikací .NET Core 3,0. Kurz [EventCounter](eventcounters.md) obsahuje seznam výchozích sad čítačů, které jsou shromažďovány. Obsahuje také pokyny k přizpůsobení seznamu.

### <a name="manually-tracking-additional-telemetry"></a>Ruční sledování další telemetrie

I když SDK automaticky shromažďuje telemetrii, jak je vysvětleno výše, ve většině případů bude uživatel muset Application Insights službě odeslat další telemetrii. Doporučený způsob, jak sledovat další telemetrii, je získání instance `TelemetryClient` z injektáže závislosti a následná volání jedné z podporovaných `TrackXXX()` metod [rozhraní API](api-custom-events-metrics.md) . Dalším typickým případem použití je [vlastní sledování operací](custom-operations-tracking.md). Tento přístup je znázorněný v příkladech pracovních procesů výše.

## <a name="configure-the-application-insights-sdk"></a>Konfigurace sady Application Insights SDK

Výchozí nastavení `TelemetryConfiguration` používané sadou SDK služby Worker je podobné automatické konfiguraci, která se používá v ASP.NET nebo v aplikaci ASP.NET Core, mínus TelemetryInitializers použité k obohacení telemetrie z `HttpContext` .

Můžete přizpůsobit sadu Application Insights SDK pro službu pracovního procesu, abyste změnili výchozí konfiguraci. Uživatelé Application Insights ASP.NET Core SDK mohou být obeznámeni se změnou konfigurace pomocí ASP.NET Core vestavěného [vkládání závislostí](/aspnet/core/fundamentals/dependency-injection). Sada WorkerService SDK je také založená na podobných zásadách. Proveďte téměř všechny změny konfigurace v `ConfigureServices()` sekci voláním odpovídajících metod `IServiceCollection` , jak je uvedeno níže.

> [!NOTE]
> Při použití této sady SDK se změna konfigurace změnou `TelemetryConfiguration.Active` nepodporuje a změny se neprojeví.

### <a name="using-applicationinsightsserviceoptions"></a>Použití ApplicationInsightsServiceOptions

Můžete upravit několik běžných nastavení tak, že předáte `ApplicationInsightsServiceOptions` do `AddApplicationInsightsTelemetryWorkerService` , jako v tomto příkladu:

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

Všimněte si, že `ApplicationInsightsServiceOptions` v této sadě SDK je v oboru názvů na `Microsoft.ApplicationInsights.WorkerService` rozdíl od `Microsoft.ApplicationInsights.AspNetCore.Extensions` ASP.NET Core SDK.

Běžně používaná nastavení v `ApplicationInsightsServiceOptions`

|Nastavení | Popis | Výchozí
|---------------|-------|-------
|EnableQuickPulseMetricStream | Povolit nebo zakázat funkci LiveMetrics | true
|EnableAdaptiveSampling | Povolit/zakázat adaptivní vzorkování | true
|EnableHeartbeat | Povolí nebo zakáže funkci prezenčních signálů, které pravidelně (ve výchozím nastavení 15 minut) pošle vlastní metriku s názvem HeartBeatState s informacemi o modulu runtime, jako je verze .NET, informace o prostředí Azure, pokud jsou k dispozici atd. | true
|AddAutoCollectedMetricExtractor | Povolí nebo zakáže extraktor AutoCollectedMetrics, což je TelemetryProcessor, který posílá předem agregované metriky o požadavcích a závislostech, než proběhne vzorkování. | true
|EnableDiagnosticsTelemetryModule | Povolit/zakázat `DiagnosticsTelemetryModule` . Zakázáním této možnosti dojde k ignorování následujících nastavení; `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

Seznam [konfigurovatelných nastavení v nástroji `ApplicationInsightsServiceOptions` najdete v](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) tématu seznam nejaktuálnějších dat.

### <a name="sampling"></a>Vzorkování

Sada SDK Application Insights pro službu pracovního procesu podporuje jak pevné, tak adaptivní vzorkování. Adaptivní vzorkování je ve výchozím nastavení povolené. Konfigurace vzorkování pro službu pracovního procesu se provádí stejným způsobem jako u [aplikací ASP.NET Core](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Přidání TelemetryInitializers

[Inicializátory telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) použijte, když chcete definovat vlastnosti, které se odesílají se všemi telemetrie.

Přidejte všechny nové `TelemetryInitializer` kontejnery do `DependencyInjection` kontejneru a sada SDK bude automaticky přidána do nástroje `TelemetryConfiguration` .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Odebírá se TelemetryInitializers

Ve výchozím nastavení jsou Inicializátory telemetrie přítomné. Chcete-li odebrat všechny nebo konkrétní Inicializátory telemetrie, použijte následující vzorový kód *po* volání `AddApplicationInsightsTelemetryWorkerService()` .

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

### <a name="adding-telemetry-processors"></a>Přidávání procesorů telemetrie

Vlastní procesory telemetrie můžete přidat `TelemetryConfiguration` pomocí metody rozšíření `AddApplicationInsightsTelemetryProcessor` na `IServiceCollection` . Pomocí procesorů telemetrie v [pokročilých scénářích filtrování](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer) umožníte větší kontrolu nad tím, co je zahrnuto nebo vyloučeno z telemetrie, kterou odesíláte do služby Application Insights. Použijte následující příklad.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurace nebo odebrání výchozích TelemetryModules

Application Insights používá moduly telemetrie k automatickému shromažďování telemetrie o konkrétních úlohách bez nutnosti ručního sledování.

Ve výchozím nastavení jsou povoleny následující moduly automatických kolekcí. Tyto moduly zodpovídají za automatické shromažďování telemetrie. Můžete je zakázat nebo nakonfigurovat, aby se změnily jejich výchozí chování.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule` – (Aktuálně se jedná o problém týkající se tohoto modulu telemetrie. Dočasné řešení najdete v článku věnovaném [problému GitHub 1689](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
).)
* `AzureInstanceMetadataTelemetryModule`

Pro konfiguraci všech výchozích možností `TelemetryModule` použijte metodu rozšíření `ConfigureTelemetryModule<T>` na `IServiceCollection` , jak je znázorněno v následujícím příkladu.

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

### <a name="configuring-telemetry-channel"></a>Konfigurace kanálu telemetrie

Výchozí kanál je `ServerTelemetryChannel` . Můžete ho přepsat, jak ukazuje následující příklad.

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

### <a name="disable-telemetry-dynamically"></a>Dynamické vypnutí telemetrie

Pokud chcete vypnout telemetrii podmíněně a dynamicky, můžete vyřešit `TelemetryConfiguration` instanci s ASP.NET Core kontejnerem vkládání závislostí kdekoli v kódu a nastavit `DisableTelemetry` příznak.

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

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Jak můžu sledovat telemetrii, která se automaticky neshromažďuje?

Získání instance `TelemetryClient` pomocí injektáže konstruktoru a volání požadované `TrackXXX()` metody. Nedoporučujeme vytvářet nové `TelemetryClient` instance. Instance typu Singleton `TelemetryClient` je již v kontejneru zaregistrována `DependencyInjection` , která sdílí `TelemetryConfiguration` se zbytkem telemetrie. Vytvoření nové `TelemetryClient` instance se doporučuje jenom v případě, že potřebuje konfiguraci, která je oddělená od zbytku telemetrie.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Můžu použít integrované vývojové prostředí (IDE) sady Visual Studio k zaregistrování Application Insights do projektu služby pracovních procesů?

Připojování k integrovanému vývojovému prostředí (IDE) sady Visual Studio se momentálně podporuje jenom pro aplikace ASP.NET/ASP.NET Core. Tento dokument bude aktualizován, pokud aplikace Visual Studio dodává podporu pro připojování aplikací služby Worker.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Můžu Application Insights monitorování povolit pomocí nástrojů jako Monitorování stavu?

No. [Monitorování stavu](./monitor-performance-live-website-now.md) a [monitorování stavu v2](./status-monitor-v2-overview.md) aktuálně podporují pouze ASP.NET 4. x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Pokud Spouštím aplikaci v systému Linux, jsou podporovány všechny funkce?

Ano. Podpora funkcí pro tuto sadu SDK je stejná na všech platformách, s následujícími výjimkami:

* Čítače výkonu jsou podporovány pouze ve Windows s výjimkou procesoru procesu/paměti, která je znázorněna v živých metrikách.
* I když `ServerTelemetryChannel` je ve výchozím nastavení povolená, pokud je aplikace spuštěná v systému Linux nebo MacOS, kanál automaticky nevytvoří místní složku úložiště, aby se telemetrie dočasně zachovala v případě, že dojde k problémům se sítí. Z důvodu tohoto omezení dojde ke ztrátě telemetrie, pokud dojde k dočasným problémům se sítí nebo serverem. Pokud chcete tento problém obejít, nakonfigurujte pro tento kanál místní složku:

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

[Konzolová aplikace .NET Core](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Tuto ukázku použijte, pokud používáte konzolovou aplikaci napsanou v rozhraní .NET Core (2,0 nebo vyšší) nebo .NET Framework (4.7.2 nebo vyšší).

[Úlohy na pozadí ASP .NET Core s HostedServices](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Tuto ukázku použijte, pokud pracujete v Asp.Net Core 2.1/2.2 a vytváříte úlohy na pozadí podle oficiálních [pokynů.](/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&preserve-view=true)

[Služba pracovního procesu .NET Core 3,0](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Tuto ukázku použijte, pokud máte aplikaci pracovní služby .NET Core 3,0 na základě oficiálních pokynů [.](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio&view=aspnetcore-3.0&preserve-view=true#worker-service-template)

## <a name="open-source-sdk"></a>Open-Source sada SDK

[Číst a přispívat do kódu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Další kroky

* [Použijte rozhraní API](./api-custom-events-metrics.md) k posílání vlastních událostí a metrik pro podrobné zobrazení výkonu a využití vaší aplikace.
* [Sledovat další závislosti, které nejsou automaticky sledovány](./auto-collect-dependencies.md).
* [Rozšířit nebo filtrovat automaticky shromážděnou telemetrii](./api-filtering-sampling.md).
* [Vkládání závislostí v ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).
