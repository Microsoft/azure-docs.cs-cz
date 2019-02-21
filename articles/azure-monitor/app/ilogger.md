---
title: Prozkoumejte protokoly trasování .NET ve službě Azure Application Insights s ILogger
description: Ukázky z Azure Application Insights ILogger implementace pomocí ASP.NET Core a konzolové aplikace.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: c456f8f7f08fdbd0020bfc49ceeec262fa0ac773
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56457976"
---
# <a name="ilogger"></a>ILogger

ASP.NET Core podporuje protokolování rozhraní API, která funguje s různých poskytovatelů třetích stran a vestavěné protokolování. Tento článek ukazuje, jak zpracovávat protokolování s implementací objektu ILogger Application Insights v konzole a aplikace ASP.NET Core. Další informace o protokolování na základě ILogger, naleznete v tématu [v tomto článku](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="console-application"></a>Konzolová aplikace

Následuje ukázková Konzolová aplikace, konfigurované k odesílání objektu ILogger trasování do Application Insights.

Nainstalované balíčky:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();
            
        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }
    }
}
```

## <a name="aspnet-core-application"></a>Aplikace ASP.NET Core

Následující příklad zobrazuje ukázku, kterou aplikace ASP.NET Core konfigurované k odesílání objektu ILogger trasování do application insights. V tomto příkladu platí k odesílání trasování ILogger z Program.cs Startup.cs či jiné řadiče/aplikace logiky.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()                
        .ConfigureLogging(logging =>
        {                
        logging.AddApplicationInsights("ikeyhere");
                
        // Optional: Apply filters to configure LogLevel Trace or above is sent to
        // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
                
            // Additional filtering For category starting in "Microsoft",
        // only Warning or above will be sent to Application Insights.
        logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    
    // The following be picked up up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up up by Application Insights.   
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked upby Application Insights.
    // and all have ("MyKey", "MyValue") in Properties.
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
        {           
        _logger.LogInformation("An example of a Information trace..");
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogTrace("An example of a Trace level message");
        }

        return new string[] { "value1", "value2" };
    }
}
```

V obou příkladech nad samostatného balíčku se používá Microsoft.Extensions.Logging.ApplicationInsights. Ve výchozím nastavení, tato konfigurace používá minimální `TelemetryConfiguration` pro odesílání dat do služby Application Insights. Úplném znamená, že bude kanál, který slouží `InMemoryChannel`, žádné vzorkování a žádné standardní TelemetryInitializers. Toto chování lze přepsat pro konzolovou aplikaci, jak je znázorněno v následujícím příkladu.

Nainstalujte tuto další balíčky:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

Následující části ukazuje, jak přepsat výchozí `TelemetryConfiguration`. Tento příklad konfiguruje `ServerTelemetryChannel`, vzorkování a vlastní `ITelemetryInitializer`.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {                            
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });
```

I když výše uvedený přístup můžete použít v aplikaci ASP.NET Core, více běžný postup by zkombinovat regulární application monitoring pro aplikace (požadavky, závislosti atd.) pomocí objektu ILogger zachytávání jak je znázorněno níže.

Nainstalujte tuto další balíčky:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

Přidejte následující text do `ConfigureServices` metody. Tento kód vám umožní regulární aplikace monitorování s výchozí konfigurací (ServerTelemetryChannel LiveMetrics, požadavek nebo závislosti, korelace atd.)

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

V tomto příkladu používá konfiguraci `ApplicationInsightsLoggerProvider` je stejný jako regulární application monitoring pro aplikace. Proto i `ILogger` trasy a další telemetrie (požadavky, závislosti atd.) bude používat stejnou sadu `TelemetryInitializers`, `TelemetryProcessors`, a `TelemetryChannel`. Bude možné korelační a vzorkovány nebo není vzorkovány stejným způsobem.

Je však výjimka tohoto chování. Výchozí hodnota `TelemetryConfiguration` není plně nastavení při protokolování něco z `Program.cs` nebo `Startup.cs` samostatně, takže tyto protokoly nebude mít výchozí konfiguraci. Každých dalších protokolů (například protokoly z řadiče, modely atd.) by sdílenou složkou konfigurace.

## <a name="next-steps"></a>Další postup

Další informace:

- [Na základě ILogger protokolování](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [Protokoly trasování rozhraní .NET](../../azure-monitor/app/asp-net-trace-logs.md)
