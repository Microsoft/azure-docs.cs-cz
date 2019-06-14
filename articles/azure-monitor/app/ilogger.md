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
ms.openlocfilehash: 0691c35661a6d185a6aa5ed3383ad600653359d3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058591"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider pro .NET Core ILogger protokoly

ASP.NET Core podporuje protokolování rozhraní API, která funguje s různými typy poskytovatelů třetích stran a vestavěné protokolování. Protokolování se provádí voláním **Log()** nebo jeho variantě na *ILogger* instancí. Tento článek ukazuje, jak používat *ApplicationInsightsLoggerProvider* zachycení ILogger protokoly konzoly a aplikace ASP.NET Core. Tento článek také popisuje, jak ApplicationInsightsLoggerProvider integruje s další telemetrie Application Insights.
Další informace najdete v tématu [protokolování v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Aplikace ASP.NET Core

ApplicationInsightsLoggerProvider je povolena ve výchozím nastavení [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 verzi (a novější) když zapnete regulární monitorování pomocí Application Insights pomocí kteréhokoliv z standard metody:
- Při volání **UseApplicationInsights** rozšiřující metody na IWebHostBuilder 
- Při volání **AddApplicationInsightsTelemetry** rozšiřující metody na IServiceCollection

Protokoly ILogger, které zachycuje ApplicationInsightsLoggerProvider podléhají stejnou konfiguraci jako další telemetrie, která je shromažďována. Mají stejnou sadu TelemetryInitializers a TelemetryProcessors, použijte stejný TelemetryChannel a se korelují a vzorkovány stejným způsobem jako ostatní telemetrická data. Pokud používáte verzi 2.7.0-beta3 nebo později, nemusíte nic dělat zachycení ILogger protokoly.

Pouze *upozornění* nebo vyšší ILogger protokoly (ze všech kategorií) jsou ve výchozím nastavení odesílají do Application Insights. Ale můžete [použít filtry, které toto chování upravit](#control-logging-level). Je potřeba provést další kroky zaznamenání ILogger protokoly z **Program.cs** nebo **Startup.cs**. (Viz [zachytávání ILogger protokoly ze souboru Startup.cs a Program.cs v aplikacích ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Pokud používáte starší verzi sady Microsoft.ApplicationInsights.AspNet SDK nebo chcete použít jenom ApplicationInsightsLoggerProvider bez jakékoli další monitorování pomocí Application Insights, použijte následující postup:

1. Nainstalujte balíček NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Upravit **Program.cs** jak je znázorněno zde:

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
                   // Providing an instrumentation key here is required if you're using
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

Kód v kroku 2 konfiguruje `ApplicationInsightsLoggerProvider`. Následující kód ukazuje příklad třídy Kontroleru, který používá `ILogger` odeslat protokoly. Protokoly jsou zachyceny na základě Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Zachycení ILogger protokoly ze souboru Startup.cs a Program.cs v aplikacích ASP.NET Core

> [!NOTE]
> V ASP.NET Core 3.0 nebo novější, se už nedají vložit `ILogger` v souboru Startup.cs a souboru Program.cs. Zobrazit https://github.com/aspnet/Announcements/issues/353 další podrobnosti.

Nové ApplicationInsightsLoggerProvider můžete zachytit protokoly z aplikace po spuštění kanálu v rané fázi. I když ApplicationInsightsLoggerProvider je automaticky povolené ve službě Application Insights (počínaje verzí 2.7.0-beta3), nemusí kód instrumentace nastavit nahoru, až později v kanálu. Proto pouze protokoly z **řadič**/ jiné třídy budou zachyceny. Pro každý protokol počínaje zachytávání **Program.cs** a **Startup.cs** samostatně, je potřeba explicitně povolit Instrumentační klíč pro ApplicationInsightsLoggerProvider. Navíc *TelemetryConfiguration* není plně nastavené, když se připojujete z **Program.cs** nebo **Startup.cs** samotný. Tyto protokoly proto bude mít minimální konfigurace, který používá InMemoryChannel, žádné vzorkování a standardní telemetrie inicializátory nebo procesorů.

Následující příklady ukazují tuto funkci s **Program.cs** a **Startup.cs**.

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
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrace ze starého ApplicationInsightsLoggerProvider

Verze sady SDK Microsoft.ApplicationInsights.AspNet 2.7.0-beta2 podporována protokolování poskytovatele, který je nyní zastaralé. Tento zprostředkovatel bylo povoleno prostřednictvím **AddApplicationInsights()** rozšiřující metoda implementaci třídy ILoggerFactory. Doporučujeme vám, že migrujete do nového poskytovatele, který zahrnuje dva kroky:

1. Odeberte *ILoggerFactory.AddApplicationInsights()* volání z **Startup.Configure()** metoda vyhnout double protokolování.
2. Znovu použijte všechna pravidla filtrování v kódu, protože nebude dodržovat nového poskytovatele. Přetížení *ILoggerFactory.AddApplicationInsights()* trvalo minimální funkce LogLevel nebo filtru. S novým poskytovatelem filtrování je součástí samotného rozhraní protokolování. Neprovádí se zprostředkovatelem Application Insights. Proto všechny filtry, které jsou k dispozici prostřednictvím *ILoggerFactory.AddApplicationInsights()* přetížení by se měly odebrat. A pravidla filtrování měly by být podle [řízení úrovně protokolování](#control-logging-level) pokyny. Pokud používáte *appsettings.json* filtrovat protokolování, ho pokračovat pro práci s novým poskytovatelem, protože používají stejný zprostředkovatel alias *ApplicationInsights*.

Stále můžete předchozího poskytovatele. (Odebere se jenom v mění číslo hlavní verze 3. *xx*.) Ale doporučujeme migrovat do nového zprostředkovatele z následujících důvodů:

- Předchozí zprostředkovatel schází podpora [protokolu obory](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). V poskytovateli nové vlastnosti z oboru jsou automaticky přidány jako vlastní vlastnosti shromažďovat telemetrii.
- Protokoly se dají zachytit nyní mnohem dříve v kanálu při spuštění aplikace. Protokoly z **Program** a **spuštění** třídy teď se dají zachytit.
- S novým poskytovatelem filtrování se provádí na úrovni rozhraní framework, samotného. Můžete filtrovat protokoly k poskytovateli služby Application Insights v stejným způsobem jako u jiných poskytovatelů, včetně předdefinovaných poskytovatelů, jako je konzola, ladění a tak dále. Můžete také provést stejné filtry u více poskytovatelů.
- V ASP.NET Core (2.0 nebo novější), se doporučuje [povolit protokolování poskytovatelé](https://github.com/aspnet/Announcements/issues/255) pomocí metody rozšíření na ILoggingBuilder v **Program.cs** samotný.

> [!Note]
> Nový zprostředkovatel je k dispozici pro aplikace, které se zaměřují NETSTANDARD2.0 nebo novější. Pokud vaše aplikace cílí na starší verze rozhraní .NET Core, jako je .NET Core 1.1, nebo pokud je zaměřena rozhraní .NET Framework, dál používat staré zprostředkovatele.

## <a name="console-application"></a>Konzolová aplikace

Následující kód ukazuje ukázková Konzolová aplikace, která je nakonfigurovaná k odesílání objektu ILogger trasování do Application Insights.

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
        // Create the DI container.
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

Tento příklad používá samostatného balíčku `Microsoft.Extensions.Logging.ApplicationInsights`. Ve výchozím nastavení, tato konfigurace používá "minimální" TelemetryConfiguration pro odesílání dat do služby Application Insights. Úplném znamená, že InMemoryChannel je kanál, který se používá. Neexistuje žádný odběr vzorků a ne standardní TelemetryInitializers. Toto chování lze přepsat pro konzolovou aplikaci, jak ukazuje následující příklad.

Nainstalujte tuto další balíčky:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

Následující části ukazuje, jak přepsat výchozí TelemetryConfiguration pomocí **služby. Konfigurace<TelemetryConfiguration>()** metody. V tomto příkladu nastaví `ServerTelemetryChannel` a vzorkování. Přidá vlastní ITelemetryInitializer TelemetryConfiguration.

```csharp
    // Create the DI container.
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

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Řízení úrovně protokolování

ASP.NET Core *ILogger* infra nemá předdefinovaný mechanismus použít [filtrování protokolu](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). To vám umožňuje řídit protokoly, které se pošlou každý registrovaný poskytovatel, včetně poskytovatele Application Insights. Filtrování lze provést buď v konfiguraci (obvykle s využitím *appsettings.json* soubor) nebo v kódu. Azure poskytuje samotného rozhraní. Není specifický pro poskytovatele Application Insights.

Následující příklady použití pravidel filtru pro ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Vytvoření pravidla filtru v konfiguraci s appsettings.json

Pro ApplicationInsightsLoggerProvider, poskytovatele alias je `ApplicationInsights`. Následující část *appsettings.json* nakonfiguruje protokoly pro *upozornění* a vyšší ze všech kategorií a *chyba* a vyšší z kategorií, které začínají znakem " Společnost Microsoft"k odeslání do `ApplicationInsightsLoggerProvider`.

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

Následující fragment kódu nakonfiguruje protokoly pro *upozornění* a vyšší ze všech kategorií a pro *chyba* a vyšší z kategorií, které začínají znakem "Microsoft" k odeslání do `ApplicationInsightsLoggerProvider`. Tato konfigurace je stejný jako v předchozí části *appsettings.json*.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Co jsou staré a nové verze ApplicationInsightsLoggerProvider?

[Sada SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) zahrnuté integrované ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), který byl povolen prostřednictvím  **Implementaci třídy ILoggerFactory** metody rozšíření. Tento zprostředkovatel je označená jako zastaralá z 2.7.0-beta2 verze. Odebere se zcela v další hlavní verze změně. [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) samotném balíčku není zastaralá. Je potřeba povolit monitorování požadavků, závislosti a tak dále.

Navrhované alternativou je nový samostatný balíček [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), který obsahuje vylepšené (ApplicationInsightsLoggerProvider Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) a rozšiřující metody na ILoggerBuilder pro jeho povolení.

[Sada SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 verze je závislá na nový balíček a povolí zachytávání ILogger automaticky.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Proč jsou uvedeny některé protokoly ILogger dvakrát ve službě Application Insights?

Duplikování může dojít, pokud máte starší verzi (teď zastaralé) ApplicationInsightsLoggerProvider povoleno voláním `AddApplicationInsights` na `ILoggerFactory`. Zkontrolujte, jestli vaše **konfigurovat** metoda má následující a odebrat ho:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Pokud dochází k protokolování double při ladění ze sady Visual Studio, nastavte `EnableDebugLogger` k *false* v kódu, který povolí Application Insights, následujícím způsobem. Tato duplikace a oprava platí pouze při ladění aplikace.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Aktualizoval(a) jsem [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 verzi a protokoly z objektu ILogger ukládány automaticky. Jak můžu vypnout tuto funkci úplně?

Zobrazit [řízení úrovně protokolování](../../azure-monitor/app/ilogger.md#control-logging-level) části se dozvíte, jak filtrovat protokoly obecně. Chcete-li ApplicationInsightsLoggerProvider nevypnete, použijte `LogLevel.None`:

**V kódu:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**V konfiguraci:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Proč několik protokolů ILogger nemají stejné vlastnosti jako ostatní?

Application Insights shromažďuje a odesílá ILogger protokoly s použitím stejného TelemetryConfiguration, který se používá pro každý další telemetrie. Ale dojde k výjimce. Ve výchozím nastavení, TelemetryConfiguration není nastavený plně při přihlašování z **Program.cs** nebo **Startup.cs**. Protokoly z těchto umístění nebude mít výchozí konfiguraci, tak nesmí být spuštěné všechny TelemetryInitializers a TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Používám samostatného balíčku Microsoft.Extensions.Logging.ApplicationInsights a chci, aby k protokolování některé další vlastní telemetrická data ručně. Jak to mám udělat, který?

Při použití samostatného balíčku `TelemetryClient` není aplikován na kontejnerů DI, takže budete muset vytvořit novou instanci třídy `TelemetryClient` a použijte stejnou konfiguraci jako protokolovací nástroj poskytovatele, jak ukazuje následující kód. Tím se zajistí, že stejné konfigurace se použije pro všechny vlastní telemetrická data také telemetrická data z objektu ILogger.

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
> Používáte-li povolit Application Insights Microsoft.ApplicationInsights.AspNetCore balíček, můžete upravit tento kód získat `TelemetryClient` přímo v konstruktoru. Příklad najdete v tématu [tyto Nejčastější dotazy](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Jaký typ telemetrie Application Insights je vytvořen z objektu ILogger protokolů? Nebo kde uvidím ILogger protokolů ve službě Application Insights?

ApplicationInsightsLoggerProvider zachytávat protokoly ILogger a vytvoří TraceTelemetry z nich. Pokud je předán objekt výjimky **Log()** metodu na objektu ILogger, *ExceptionTelemetry* vytvořil, a nikoli TraceTelemetry. Tyto položky telemetrie najdete ve stejných míst jako další TraceTelemetry nebo ExceptionTelemetry pro službu Application Insights, včetně portálu analytics a místní ladicí program sady Visual Studio.

Pokud chcete vždy odesílat TraceTelemetry, použijte tento fragment kódu: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nejsou nainstalovaná sada SDK a povolte Application Insights pro aplikace ASP.NET Core pomocí rozšíření Azure Web Apps. Použití nového poskytovatele 

Rozšíření Application Insights ve službě Azure Web Apps využívá předchozího poskytovatele. Můžete upravit pravidla filtrování v *appsettings.json* souboru pro vaši aplikaci. Pokud chcete využít výhod nového poskytovatele, použijte čas sestavení instrumentace provedením závislostí NuGet v sadě SDK. Tento článek bude aktualizován, při rozšíření přepne na používání nového poskytovatele.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Jsem pomocí samostatného balíčku Microsoft.Extensions.Logging.ApplicationInsights a povolení Application Insights poskytovatele voláním **Tvůrce. AddApplicationInsights("ikey")** . Je možné získat instrumentačním klíčem z konfigurace?


Upravte soubor Program.cs a appsettings.json následujícím způsobem:

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

   Relevantní části `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Tento kód je povinné jenom v případě, že používáte samostatné protokolování poskytovatele. Pro pravidelné monitorování pomocí Application Insights, je automaticky načíst klíč instrumentace z konfigurační cesty *ApplicationInsights: Instrumentationkey*. AppSettings.JSON by měl vypadat nějak takto:

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

* [Protokolování v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Protokoly trasování .NET ve službě Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
