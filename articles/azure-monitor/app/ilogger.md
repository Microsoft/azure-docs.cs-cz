---
title: Prozkoumejte protokoly trasování .NET ve službě Azure Application Insights s ILogger
description: Ukázky použití zprostředkovatele Azure Application Insights ILogger s ASP.NET Core a konzolové aplikace.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 615eaa3df7cabad72ac321978eb01d93a7bfa988
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608281"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider pro .NET Core ILogger protokoly

ASP.NET Core podporuje protokolování rozhraní API, která funguje s různými typy poskytovatelů třetích stran a vestavěné protokolování. Protokolování se provádí voláním Log() nebo jeho variantě ho na `ILogger` instancí. Tento článek popisuje, jak používat `ApplicationInsightsLoggerProvider` zachycení `ILogger` protokoly konzoly a aplikací ASP.NET Core. Tento článek také popisuje, jak `ApplicationInsightsLoggerProvider` je integrovaná s další telemetrie Application Insights.
Přečtěte si další protokolování v Asp.Net Core, najdete v článku [v tomto článku](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Aplikace ASP.NET Core

Počínaje [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) a vyšší verze 2.7.0-beta3 `ApplicationInsightsLoggerProvider` je ve výchozím nastavení povolené, při povolování regulární monitorování pomocí Application Insights pomocí standardních metod – podle volání `UseApplicationInsights` rozšiřující metody na IWebHostBuilder nebo `AddApplicationInsightsTelemetry` rozšiřující metody na IServiceCollection. `ILogger` protokoly nezachytává `ApplicationInsightsLoggerProvider` podléhají stejnou konfiguraci jako další telemetrie shromážděných. i.e mají stejnou sadu `TelemetryInitializer`s, `TelemetryProcessor`s, používá stejný `TelemetryChannel`a bude korelační a vzorkovány stejně jako každý další telemetrie.  Pokud jste na tuto verzi sady SDK nebo vyšší, pak je potřeba zaznamenat `ILogger` protokoly.

Ve výchozím nastavení, pouze `ILogger` protokoly z `Warning` nebo nad (ze všech kategorií) se odesílají do Application Insights. Toto chování můžete změnit tak, jak je znázorněno použití filtrů [tady](#control-logging-level). Další kroky jsou také vyžaduje, pokud `ILogger` protokoly z `Program.cs` nebo `Startup.cs` se dají zachytit, jak je znázorněno [tady](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications).

Pokud používáte starší verzi sady Microsoft.ApplicationInsights.AspNet SDK nebo chcete použít jenom ApplicationInsightsLoggerProvider, bez jakékoli další monitorování pomocí Application Insights, postupujte podle následujících kroků.

1. Nainstalujte balíček nuget.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. upravit `Program.cs` níže

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
            builder =>
            {
                // Providing an instrumentation key here is required if you are using
                // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                // or if you want to capture logs from early in the application startup
                // pipeline from Startup.cs or Program.cs itself.
                builder.AddApplicationInsights("ikey");

                // Optional: Apply filters to control what logs are sent to Application Insights.
                // The following configures LogLevel Information or above to be sent to
                // Application Insights for all categories.
                builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                 ("", LogLevel.Information);
            }
        );
}
```

Ve výše uvedeném kódu nakonfiguruje `ApplicationInsightsLoggerProvider`. Následující příklad zobrazuje příklad třídy Kontroleru, který používá `ILogger` k odeslání protokolů, které jsou zachyceny na základě ApplicationInsights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>Zachytávání ILogger protokoly ze souboru Startup.cs Program.cs v aplikacích Asp.Net Core

S novou ApplicationInsightsLoggerProvider je možné zachytit protokoly z kanálu při spuštění aplikace v rané fázi. I když je ApplicationInsightsLoggerProvider automaticky povolili nástroje Application Insights (z 2.7.0-beta3 a vyšší), nemají nastavení klíče instrumentace až do pozdějšího kanálu, takže pouze protokoly z řadiče / jiné třídy budou zachyceny. K zachycení všech protokolů, počínaje `Program.cs` a `Startup.cs` samostatně, jeden potřeba explicitně povolit ApplicationInsightsLoggerProvider s instrumentačním klíčem. Je také důležité si uvědomit, že `TelemetryConfiguration` není plně nastavení při protokolování něco z `Program.cs` nebo `Startup.cs` samostatně, takže tyto protokoly budou používat úplné minimální konfigurace, která používá InMemoryChannel, žádné vzorkování a žádné standardní telemetrie Inicializátory nebo procesorů.

Následující příklad zobrazuje příklady `Program.cs` a `Startup.cs` díky této funkci.

#### <a name="example-programcs"></a>Příklad souboru Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Příklad souboru Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>Migrace ze starého ApplicationInsightsLoggerProvider

Verze sady SDK Microsoft.ApplicationInsights.AspNet před 2.7.0-beta2, nepodporuje protokolování poskytovatele, který je nyní zastaralá. Tento zprostředkovatel byl povolen `AddApplicationInsights()` metodu rozšíření `ILoggerFactory`. Tento zprostředkovatel je zastaralý a uživatelé jsou navržené k migraci do nového poskytovatele. Migrace zahrnuje dva kroky.

1. Odeberte volání ILoggerFactory.AddApplicationInsights() z `Startup.Configure()` metoda vyhnout double protokolování.
2. Znovu použijte jakékoli pravidlo filtrování v kódu, jako nebude dodržovat nového poskytovatele. Přetížení ILoggerFactory.AddApplicationInsights() trvalo minimální funkce LogLevel nebo filtru. S novým poskytovatelem filtrování je součástí protokolovacího rozhraní a ne provádí poskytovatele Application Insights. Proto všechny filtry, které poskytuje prostřednictvím `ILoggerFactory.AddApplicationInsights()` přetížení by měly být odstraněny a pravidla filtrování by měla být k dispozici následující [tyto](#control-logging-level) pokyny. Pokud používáte `appsettings.json` filtrovat protokolování, bude nadále fungovat s novým poskytovatelem jako používají stejný zprostředkovatel Alias - **ApplicationInsights**.

Zatímco je stále možné předchozího poskytovatele (to je nyní zastaralá a odebere pouze v hlavní verze změnu 3.xx), migrace do novější zprostředkovatele z následujících důvodů důrazně doporučujeme.

1. Předchozí zprostředkovatel chybějící podpora [obory](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). V poskytovateli nové vlastnosti z oboru jsou automaticky přidány jako vlastní vlastnosti shromažďovat telemetrii.
2. Protokoly se dají zachytit nyní mnohem dříve v kanálu při spuštění aplikace. i.e Protokoly z programu a spuštění tříd se teď dají zachytit.
3. Pomocí nového poskytovatele toto filtrování se provádí na úrovni rozhraní framework, samotného. Filtrování protokolů poskytovatele Application Insights můžete udělat v přesně stejným způsobem jako u jiných poskytovatelů, včetně předdefinovaných poskytovatelů, jako je konzola, ladění a tak dále. Je také možné použít stejné filtry pro více poskytovatelů.
4. [Doporučuje](https://github.com/aspnet/Announcements/issues/255) způsob v ASP.NET Core (2.0 a vyšší), jak povolit protokolování zprostředkovatelů je pomocí metody rozšíření na ILoggingBuilder v `Program.cs` samotný.

> [!Note]
> Je k dispozici pro aplikace určené pro nového poskytovatele `NETSTANDARD2.0` nebo vyšší. Pokud vaše aplikace cílí na starší verze rozhraní .NET Core, jako je .NET Core 1.1 nebo cílí na rozhraní .NET Framework, dál používat staré zprostředkovatele.

## <a name="console-application"></a>Konzolová aplikace

Následující kód ukazuje ukázková Konzolová aplikace, konfigurované k odesílání `ILogger` trasování do Application Insights.

Nainstalované balíčky:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
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

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

V příkladu výše, samostatného balíčku `Microsoft.Extensions.Logging.ApplicationInsights` se používá. Ve výchozím nastavení, tato konfigurace používá minimální `TelemetryConfiguration` pro odesílání dat do služby Application Insights. Úplném znamená, že bude kanál, který slouží `InMemoryChannel`, žádné vzorkování a žádné standardní TelemetryInitializers. Toto chování lze přepsat pro konzolovou aplikaci, jak je znázorněno v následujícím příkladu.

Nainstalujte tuto další balíčky:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

Následující části ukazuje, jak přepsat výchozí `TelemetryConfiguration` pomocí `services.Configure<TelemetryConfiguration>()` metoda. V tomto příkladu nastaví `ServerTelemetryChannel`vzorkování a přidá vlastní `ITelemetryInitializer` k `TelemetryConfiguration`.

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

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Řízení úrovně protokolování

Asp.Net Core `ILogger` infra nemá předdefinovaný mechanismus použít [filtrování](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) protokolů, které umožňuje uživatelům řídit protokoly odeslané do jednotlivých registrovaných zprostředkovatelů, včetně poskytovatele Application Insights. Toto filtrování lze provést buď v konfiguraci (obvykle s využitím `appsettings.json` soubor) nebo v kódu. Azure je k dispozici v rámci samotného rozhraní a není specifická pro poskytovatele Application Insights.

Příklady použití pravidel filtru ApplicationInsightsLoggerProvider jsou uvedena níže.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Vytvoření pravidla filtru v konfiguraci s appsettings.json

Pro ApplicationInsightsLoggerProvider, poskytovatele alias je `ApplicationInsights`. Níže zobrazené v části `appsettings.json` nakonfiguruje protokoly `Warning` a vyšší ze všech kategorií `Error` a vyšší z kategorie počínaje "Microsoft" k odeslání do `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Vytvoření pravidla filtru v kódu

Níže uvedeného kódu fragment kódu nakonfiguruje protokoly `Warning` a vyšší ze všech kategorií `Error` a vyšší z kategorie počínaje "Microsoft" k odeslání do `ApplicationInsightsLoggerProvider`. Tato konfigurace je stejný jako výše konfigurace v `appsettings.json`.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

*1. Co je ApplicationInsightsLoggerProvider staré a nové?*

* [Sada SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) dodaných s integrovanou ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), který se aktivuje pomocí implementaci třídy ILoggerFactory metody rozšíření. Tento zprostředkovatel je označená jako zastaralá z 2.7.0-beta2 a vyšší a bude v příští hlavní verze změně zcela odebrána. [To](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) samotného nezastaralý balíček a je nutné k povolení monitorování požadavků, závislosti atd.

* Navrhované alternativou je nový samostatný balíček [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), který obsahuje vylepšené (ApplicationInsightsLoggerProvider Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) a metody rozšíření na ILoggerBuilder pro jeho povolení.

* [Sada SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 verze a vyšší budou zavést závislost na balíčku výše a umožňuje `ILogger` zachycení automaticky.

*2. Objevují se některé `ILogger` dvakrát ve službě Application Insights se zobrazí protokoly?*

* Tato duplikace je možné, pokud máte starší verzi (teď zastaralé) `ApplicationInsightsLoggerProvider` povoleno voláním `AddApplicationInsights` na `ILoggerFactory`. Zkontrolujte, jestli vaše `Configure` metoda má následující a odeberte ji.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Pokud se při ladění ze sady Visual Studio zobrazuje double protokolování, změňte kód používaný k následujícím způsobem povolte Application Insights tak, že nastavíte `EnableDebugLogger` jako NEPRAVDA. Tento problém duplikace a oprava platí pouze při ladění aplikace.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3. Aktualizovat, aby [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 verzi a mám teď zobrazují, které protokoly z `ILogger` ukládány automaticky. Jak lze vypnout tuto funkci úplně?*

* Zobrazit [to](../../azure-monitor/app/ilogger.md#control-logging-level) části vědět, jak filtrovat protokoly obecně. Chcete-li vypínací ApplicationInsightsLoggerProvider použijte `LogLevel.None` pro něj.

  V kódu

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  V konfiguraci

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4. Objevují se některé `ILogger` protokoly nemají stejné vlastnosti jako ostatní?*

* Application Insights zachycení a odešle `ILogger` přihlásí pomocí stejného `TelemetryConfiguration` použitý pro každý další telemetrie. Existuje výjimka tohoto pravidla. Výchozí hodnota `TelemetryConfiguration` není plně nastavení při protokolování něco z `Program.cs` nebo `Startup.cs` samostatně, takže protokolů z těchto míst nebude mít výchozí konfigurace a proto nebude spuštěn všechny `TelemetryInitializer`s a `TelemetryProcessor`s.

*5. Používám samostatného balíčku Microsoft.Extensions.Logging.ApplicationInsights a chci, aby k protokolování některé další vlastní telemetrická data ručně. Jak to mám udělat, který?*

* Při použití samostatného balíčku `TelemetryClient` není aplikován na kontejnerů DI, takže se očekává, že uživatelé vytvořit novou instanci třídy `TelemetryClient` pomocí stejné konfigurace jako poskytovatel protokolovacího nástroje, jak je znázorněno níže. Tím se zajistí, že stejné konfigurace se použije pro všechny vlastní telemetrická data, jakož i zachycených ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Mějte prosím na paměti, že pokud balíček Microsoft.ApplicationInsights.AspNetCore balíčku se používá k povolení Application Insights, klikněte výše uvedeném příkladu by měl být upraven zobrazíte `TelemetryClient` přímo v konstruktoru. Zobrazit [to](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions) pro úplný příklad.


*6. Jaký typ telemetrie Application Insights je vytvořen z `ILogger` protokoly? nebo kde lze zobrazit `ILogger` protokolů ve službě Application Insights?*

* Zaznamená ApplicationInsightsLoggerProvider `ILogger` protokoly a vytvoří `TraceTelemetry` z něj. Pokud objektu výjimky je předán metodě Log() na objektu ILogger, pak namísto `TraceTelemetry`, `ExceptionTelemetry` se vytvoří. Tyto položky telemetrie najdete ve stejných míst jako jakýkoli jiný `TraceTelemetry` nebo `ExceptionTelemetry` pro službu Application Insights, včetně portálu analytics a místní ladicí program sady Visual Studio.
Pokud chcete vždy odesílat `TraceTelemetry`, potom pomocí fragmentu kódu ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```.

*7. Nejsou nainstalované sady SDK a povolte Application Insights pro aplikace Asp.Net Core pomocí rozšíření webové aplikace Azure. Použití nového poskytovatele*

* Rozšíření Application Insights v Azure Web App pomocí předchozího poskytovatele. Pravidla filtrování můžete upravit v `appsettings.json` pro vaši aplikaci. Pokud chcete využít výhod nového poskytovatele, použijte čas sestavení instrumentace provedením závislostí nuget v sadě SDK. Tento dokument se aktualizují při rozšíření přepne na používání nového poskytovatele.

*8. Jsem pomocí samostatného balíčku Microsoft.Extensions.Logging.ApplicationInsights a povolení Application Insights poskytovatele volání Tvůrce. AddApplicationInsights("ikey"). Je možné získat klíč instrumentace z konfigurace?*


* Upravit `Program.cs` a `appsettings.json` jak je znázorněno níže.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .ConfigureLogging((hostingContext, logging) =>
            {
                // hostingContext.HostingEnvironment can be used to determine environments as well.
                var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                logging.AddApplicationInsights(appInsightKey);
            });
}
```

Příslušné části z `appsettings.json`

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

Ve výše uvedeném kódu je požadované jenom při použití samostatných zprostředkovatele. Pro pravidelné monitorování pomocí Application Insights, je automaticky načíst klíč instrumentace z konfigurační cesty `ApplicationInsights:Instrumentationkey` a `appsettings.json` by měl vypadat jako v následujícím příkladu.

```json
{
  "ApplicationInsights":
    {
        "Instrumentationkey":"putrealikeyhere"
    }
}
```

## <a name="next-steps"></a>Další postup

Další informace:

* [Protokolování v Asp.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Protokoly trasování .NET ve službě Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
