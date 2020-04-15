---
title: Prozkoumejte protokoly trasování rozhraní .NET pomocí protokolu ILogger – Přehledy aplikací Azure
description: Ukázky použití poskytovatele Azure Application Insights ILogger s ASP.NET aplikace core a konzolové aplikace.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 33effe9cfec6d766d573617ff03b58564e5b34d1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313659"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider pro protokoly ILogger jádra .NET

ASP.NET Core podporuje rozhraní API pro protokolování, které funguje s různými druhy integrovaných poskytovatelů protokolování a protokolování třetích stran. Protokolování se provádí voláním **Log()** nebo jeho variantou na instancích *ILogger.* Tento článek ukazuje, jak pomocí *ApplicationInsightsLoggerProvider* zachytit protokoly ILogger v konzoli a ASP.NET základní aplikace. Tento článek také popisuje, jak ApplicationInsightsLoggerProvider integruje s jinými application insights telemetrie.
Další informace najdete [v tématu Protokolování ASP.NET jádra](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET základní aplikace

ApplicationInsightsLoggerProvider je ve výchozím nastavení povolen v [sadě Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.7.1 (a novější), když zapnete pravidelné monitorování přehledů aplikací pomocí některé z metod:

- Voláním metody rozšíření **UseApplicationInsights** na IWebHostBuilder (nyní zastaralé)
- Voláním metody rozšíření **AddApplicationInsightsTelemetry** v kolekci IServiceCollection

Protokoly ILogger, které ApplicationInsightsLoggerProvider zachycuje podléhají stejné konfiguraci jako všechny ostatní telemetrie, která je shromažďována. Mají stejnou sadu TelemetryInitializers a TelemetryProcesors, používají stejný TelemetryChannel a jsou korelovány a vzorky stejným způsobem jako ostatní telemetrie. Pokud používáte verzi 2.7.1 nebo novější, není nutná žádná akce k zachycení protokolů ILogger.

Pouze *upozornění* nebo vyšší protokoly ILogger (ze všech [kategorií](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)) jsou odesílány do Application Insights ve výchozím nastavení. Ale můžete [použít filtry upravit toto chování](#control-logging-level). Další kroky jsou nutné k zachycení protokolů ILogger z **Program.cs** nebo **Startup.cs**. (Viz [Zachycení protokolů ILogger z Startup.cs a Program.cs v aplikacích ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Pokud používáte starší verzi sady Microsoft.ApplicationInsights.AspNet SDK nebo chcete použít pouze ApplicationInsightsLoggerProvider bez jakéhokoli jiného monitorování přehledů aplikací, použijte následující postup:

1. Nainstalujte balíček NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Upravte **Program.cs,** jak je znázorněno zde:

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

Kód v kroku 2 `ApplicationInsightsLoggerProvider`konfiguruje . Následující kód ukazuje příklad Controller třídy, která používá `ILogger` k odesílání protokolů. Protokoly jsou zachyceny Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Zachyťte protokoly ILogger u Startup.cs a Program.cs v aplikacích ASP.NET Core

> [!NOTE]
> V ASP.NET Core 3.0 a novějším již `ILogger` není možné aplikovat injekci do Startup.cs a Program.cs. Viz https://github.com/aspnet/Announcements/issues/353 další podrobnosti.

Nový ApplicationInsightsLoggerProvider můžete zachytit protokoly z počátku kanálu spuštění aplikace. Přestože ApplicationInsightsLoggerProvider je automaticky povolena v Application Insights (počínaje verzí 2.7.1), nemá instrumentace klíč nastavit až později v kanálu. Takže budou zachyceny pouze protokoly z **Controller**/other třídy. Chcete-li zachytit každý protokol začínající **Program.cs** a **Startup.cs** sám, musíte explicitně povolit klíč instrumentace pro ApplicationInsightsLoggerProvider. *TelemetryConfiguration* také není plně nastavena při přihlášení z **Program.cs** nebo **Startup.cs** sám. Takže tyto protokoly bude mít minimální konfiguraci, která používá InMemoryChannel, žádné vzorkování a žádné standardní telemetrické inicializátory nebo procesory.

Následující příklady ukazují tuto schopnost s **Program.cs** a **Startup.cs**.

#### <a name="example-programcs"></a>Příklad Program.cs

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

#### <a name="example-startupcs"></a>Příklad Startup.cs

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrace ze starého applicationinsightsloggeru

Microsoft.ApplicationInsights.AspNet SDK verze před 2.7.1 podporované poskytovatele protokolování, který je nyní zastaralý. Tento zprostředkovatel byl povolen prostřednictvím metody rozšíření **AddApplicationInsights()** iLoggerFactory. Doporučujeme migrovat na nového zprostředkovatele, který zahrnuje dva kroky:

1. Odeberte volání *ILoggerFactory.AddApplicationInsights()* z metody **Startup.Configure(),** abyste zabránili dvojitému protokolování.
2. Znovu použít všechna pravidla filtrování v kódu, protože nebudou respektovány novým zprostředkovatelem. Přetížení *ILoggerFactory.AddApplicationInsights()* trvalo minimální LogLevel nebo funkce filtru. S novým zprostředkovatelem je filtrování součástí samotného rámce protokolování. Neprovádí to poskytovatel Application Insights. Takže všechny filtry, které jsou poskytovány prostřednictvím *iLoggerFactory.AddApplicationInsights()* přetížení by měly být odstraněny. A filtrování pravidla by měla být poskytnuta podle [pokynů úrovně protokolování řízení.](#control-logging-level) Pokud k filtrování protokolování použijete soubor *appsettings.json,* bude nadále spolupracovat s novým zprostředkovatelem, protože oba používají stejný alias zprostředkovatele *ApplicationInsights*.

Stále můžete použít starého zprostředkovatele. (Bude odebránpouze v hlavní verzi změnit na 3. *xx*.) Doporučujeme však migrovat na nového poskytovatele z následujících důvodů:

- Předchozí zprostředkovatel postrádá podporu pro [obory protokolu](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). V novém zprostředkovateli vlastnosti z oboru jsou automaticky přidány jako vlastní vlastnosti shromážděné telemetrie.
- Protokoly lze nyní zachytit mnohem dříve v kanálu spuštění aplikace. Protokoly z **programových** a **spouštěcích** tříd lze nyní zachytit.
- S novým zprostředkovatelem filtrování se provádí na úrovni rozhraní samotné. Protokoly můžete filtrovat k poskytovateli Application Insights stejným způsobem jako u jiných poskytovatelů, včetně předdefinovaných poskytovatelů, jako je Console, Debug a tak dále. Stejné filtry můžete použít také u více poskytovatelů.
- V ASP.NET Core (2.0 a novější) je doporučeným způsobem, jak [povolit poskytovatele protokolování,](https://github.com/aspnet/Announcements/issues/255) použití metod rozšíření na ILoggingBuilder v **samotném Program.cs.**

> [!Note]
> Nový zprostředkovatel je k dispozici pro aplikace, které cílí na NETSTANDARD2.0 nebo novější. Od [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.14.0 dále nový zprostředkovatel je k dispozici také pro aplikace, které cílí na rozhraní .NET Framework NET461 nebo novější. Pokud vaše aplikace cílí na starší verze .NET Core, například .NET Core 1.1, nebo pokud cílí na rozhraní .NET Framework menší než NET46, pokračujte v používání starého zprostředkovatele.

## <a name="console-application"></a>Konzolová aplikace

> [!NOTE]
> K dispozici je nová sada Application Insights SDK s názvem [Microsoft.ApplicationInsights.WorkerService,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) která slouží k povolení application insights (ILogger a další telemetrie Application Insights) pro všechny konzolové aplikace. Doporučuje se použít tento balíček a související pokyny [zde](../../azure-monitor/app/worker-service.md).

Následující kód zobrazuje ukázkovou konzolovou aplikaci, která je nakonfigurovaná pro odesílání trasování ILogger u Application Insights.

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

Tento příklad používá samostatný `Microsoft.Extensions.Logging.ApplicationInsights`balíček . Ve výchozím nastavení tato konfigurace používá "minimum" TelemetryConfiguration pro odesílání dat do Application Insights. Minimum znamená, že InMemoryChannel je kanál, který se používá. Neexistuje žádné vzorkování a žádné standardní telemetrieInitializers. Toto chování může být přepsáno pro konzolovou aplikaci, jak ukazuje následující příklad.

Nainstalujte tento další balíček:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

Následující část ukazuje, jak přepsat výchozí TelemetryConfiguration pomocí **služeb. Konfigurace\<metody TelemetryConfiguration>().** Tento příklad `ServerTelemetryChannel` nastaví a vzorkování. Přidá vlastní ITelemetryInitializer telemetryconfiguration.

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

ASP.NET Core *ILogger* infra má vestavěný mechanismus pro použití [filtrování protokolů](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). To umožňuje řídit protokoly, které jsou odesílány každému registrovanému zprostředkovateli, včetně poskytovatele Application Insights. Filtrování lze provést buď v konfiguraci (obvykle pomocí souboru *appsettings.json)* nebo v kódu. Toto zařízení je poskytováno samotným rámcem. Není to specifické pro poskytovatele Application Insights.

Následující příklady platí pravidla filtru applicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Vytvoření pravidel filtru v konfiguraci pomocí souboru appsettings.json

Pro ApplicationInsightsLoggerProvider je `ApplicationInsights`alias zprostředkovatele . Následující část *appsettings.json* instruuje poskytovatele protokolování obecně protokolovat na úrovni *Upozornění* a výše. Potom přepíše `ApplicationInsightsLoggerProvider` do protokolu kategorie, které začínají "Microsoft" na úrovni *Error* a výše.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Vytvoření pravidel filtru v kódu

Následující fragment kódu konfiguruje protokoly pro *upozornění* a výše ze všech kategorií a pro *chyby* a výše z kategorií, které začínají "Microsoft" chcete odeslat do `ApplicationInsightsLoggerProvider`. Tato konfigurace je stejná jako v předchozí části v *appsettings.json*.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Jaké jsou staré a nové verze ApplicationInsightsLoggerProvider?

[Sada Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) obsahovala předdefinovaný applicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), který byl povolen pomocí rozšiřujících metod **ILoggerFactory.** Tento zprostředkovatel je označen jako zastaralý z verze 2.7.1. Bude zcela odstraněn v příští hlavní verzi změny. Samotný balíček [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) není zastaralý. Je nutné povolit monitorování požadavků, závislostí a tak dále.

Navrhovanou alternativou je nový samostatný balíček [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), který obsahuje vylepšenou applicationinsightsloggerprovider (Microsoft.Extensions.ApplicationInsights.ApplicationInsights.ApplicationInsightsLoggerProvider) a metody rozšíření na ILoggerBuilder pro jeho povolení.

[Sada Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.7.1 přebírá závislost na novém balíčku a umožňuje automatické zachycení iLoggeru.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Proč jsou některé protokoly ILogger uněkterých v Application Insights zobrazeny dvakrát?

Duplikace může dojít, pokud máte starší (nyní zastaralé) verze `AddApplicationInsights` `ILoggerFactory`ApplicationInsightsLoggerProvider povoleno voláním na . Zkontrolujte, zda má metoda **Configure** následující, a odeberte ji:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Pokud dojde k dvojité protokolování při ladění `EnableDebugLogger` z Visual Studio, nastavte na *false* v kódu, který umožňuje Application Insights, takto. Tato duplikace a oprava je relevantní pouze při ladění aplikace.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Aktualizoval(a) jsem se na [soubor Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.7.1 a protokoly z iLoggeru jsou zachyceny automaticky. Jak tuto funkci úplně vypnu?

V části [Řízení úrovně protokolování](../../azure-monitor/app/ilogger.md#control-logging-level) se podívejte, jak filtrovat protokoly obecně. Chcete-li vypnout ApplicationInsightsLoggerProvider, použijte `LogLevel.None`:

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

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Proč některé protokoly ILogger nemají stejné vlastnosti jako ostatní?

Application Insights zachycuje a odesílá protokoly ILogger pomocí stejné TelemetryConfiguration, který se používá pro všechny ostatní telemetrie. Ale je tu výjimka. Ve výchozím nastavení telemetryconfiguration není plně nastavena při protokolování z **Program.cs** nebo **Startup.cs**. Protokoly z těchto míst nebude mít výchozí konfiguraci, takže nebudou spouštět všechny telemetrické initializéry a telemetrické procesory.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Používám samostatný balíček Microsoft.Extensions.Logging.ApplicationInsights a chci protokolovat některé další vlastní telemetrie ručně. Jak to mám udělat?

Při použití samostatného balíčku `TelemetryClient` není vložen do kontejneru DI, takže je třeba `TelemetryClient` vytvořit novou instanci a použít stejnou konfiguraci jako zprostředkovatel úhozu protokolu, jak ukazuje následující kód. Tím je zajištěno, že stejná konfigurace se používá pro všechny vlastní telemetrie, stejně jako telemetrie z ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> Pokud k povolení application insights použijete balíček Microsoft.ApplicationInsights.AspNetCore, upravte tento kód tak, abyste se dostali `TelemetryClient` přímo do konstruktoru. Viz například [tyto nejčastější dotazy](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Jaký typ telemetrie Application Insights se vyrábí z protokolů ILogger? Nebo kde mohu vidět protokoly ILogger v Application Insights?

ApplicationInsightsLoggerProvider zachycuje protokoly ILogger a vytváří tracetelemetimetrie z nich. Pokud Exception objekt je předán **Log()** metoda na ILogger, *ExceptionTelemetry* je vytvořen namísto TraceTelemetry. Tyto položky telemetrie lze nalézt na stejných místech jako jakékoli jiné TraceTelemetry nebo ExceptionTelemetry pro application insights, včetně portálu, analýzy nebo Visual Studio místní ladicí program.

Pokud dáváte přednost vždy odeslat TraceTelemetry, použijte tento úryvek:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nemám nainstalovanou sadu SDK a pomocí rozšíření Azure Web Apps povoluji přehledy aplikací pro ASP.NET základní aplikace. Jak mohu použít nového zprostředkovatele? 

Rozšíření Application Insights v Azure Web Apps používá nového poskytovatele. Pravidla filtrování můžete upravit v souboru *appsettings.json* pro vaši aplikaci.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Používám samostatný balíček Microsoft.Extensions.Logging.ApplicationInsights a povolení application insights poskytovatele voláním **tvůrce. AddApplicationInsights("ikey")**. Existuje možnost získat klíč instrumentace z konfigurace?


Upravte Program.cs a appsettings.json takto:

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

   Příslušný oddíl `appsettings.json`od :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Tento kód je vyžadován pouze v případě, že používáte samostatného zprostředkovatele protokolování. Pro pravidelné monitorování Application Insights je klíč instrumentace automaticky načten z cesty konfigurace *ApplicationInsights: Instrumentationkey*. Appsettings.json by měl vypadat takto:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Další kroky

Další informace:

* [Přihlášení ASP.NET jádra](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Protokoly trasování .NET v application insights](../../azure-monitor/app/asp-net-trace-logs.md)
