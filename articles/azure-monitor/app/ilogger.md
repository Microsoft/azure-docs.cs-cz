---
title: Prozkoumejte protokoly trasování .NET pomocí ILogger – Azure Application Insights
description: Ukázky použití poskytovatele služby Azure Application Insights ILogger s aplikacemi ASP.NET Core a konzolou.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 171aaeb624bfedb9aa7408a736c11faca316b392
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87322631"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>Protokoly ApplicationInsightsLoggerProvider pro .NET Core ILogger

ASP.NET Core podporuje protokolovací rozhraní API, které funguje s různými druhy integrovaných a zprostředkovatelů protokolování třetích stran. Protokolování se provádí voláním **log ()** nebo varianty z *ILogger* instancí. Tento článek ukazuje, jak používat *ApplicationInsightsLoggerProvider* k zachycení protokolů ILogger v konzolových a ASP.NET Corech aplikacích. Tento článek také popisuje, jak se ApplicationInsightsLoggerProvider integruje s jinou telemetrie Application Insights.
Další informace najdete v tématu věnovaném [přihlášení ASP.NET Core](/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core aplikací

ApplicationInsightsLoggerProvider je ve výchozím nastavení povolená v [sadě Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) Version 2.7.1 (a novější), když zapnete běžné Application Insights monitorování prostřednictvím kterékoli z těchto metod:

- Voláním metody rozšíření **UseApplicationInsights** na IWebHostBuilder (nyní zastaralé)
- Voláním metody rozšíření **AddApplicationInsightsTelemetry** na IServiceCollection

Protokol ILogger, který ApplicationInsightsLoggerProvider zachycuje, podléhá stejné konfiguraci jako jakákoli jiná shromažďovaná telemetrie. Mají stejnou sadu TelemetryInitializers a TelemetryProcessors, používají stejné TelemetryChannel a jsou korelace a vzorky stejným způsobem jako u jiné telemetrie. Pokud používáte verzi 2.7.1 nebo novější, není nutné provádět žádnou akci pro zachycení protokolů ILogger.

Ve výchozím nastavení se Application Insights standardně odesílají jenom *výstrahy* nebo protokoly ILogger (ze všech [kategorií](/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)). Můžete ale [použít filtry a toto chování upravit](#control-logging-level). Pro zachycení protokolů ILogger z **program.cs** nebo **Startup.cs**se vyžadují další kroky. (Viz [zachycení protokolů ILogger z Startup.cs a program.cs v aplikacích ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Pokud používáte starší verzi sady Microsoft. ApplicationInsights. AspNet SDK nebo chcete použít pouze ApplicationInsightsLoggerProvider bez jakéhokoli dalšího monitorování Application Insights, použijte následující postup:

1. Nainstalujte balíček NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Upravte **program.cs** , jak je znázorněno zde:

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

Kód v kroku 2 nakonfiguruje `ApplicationInsightsLoggerProvider` . Následující kód ukazuje ukázkovou třídu kontroleru, která používá `ILogger` k odesílání protokolů. Protokoly jsou zachyceny Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Zachycení protokolů ILogger z Startup.cs a Program.cs v aplikacích ASP.NET Core

> [!NOTE]
> V ASP.NET Core 3,0 a novějším, již není možné vkládat `ILogger` do Startup.cs a program.cs. Další podrobnosti najdete v tématu https://github.com/aspnet/Announcements/issues/353 .

Nový ApplicationInsightsLoggerProvider může zachytit protokoly od začátku v kanálu spuštění aplikace. I když je ApplicationInsightsLoggerProvider automaticky povolený v Application Insights (počínaje verzí 2.7.1), nemá nastavený klíč instrumentace až do pozdějšího kanálu. Proto budou zachyceny pouze protokoly z tříd/další **kontroleru**. Pro zachycení každého protokolu počínaje **program.cs** a **Startup.cs** je nutné explicitně povolit instrumentaci klíče pro ApplicationInsightsLoggerProvider. *TelemetryConfiguration* se také nenastavuje zcela při protokolování z **program.cs** nebo **Startup.cs** . Takže tyto protokoly budou mít minimální konfiguraci, která používá InMemoryChannel, žádné vzorkování a žádné standardní Inicializátory telemetrie nebo procesory.

Následující příklady ukazují tuto možnost v **program.cs** a **Startup.cs**.

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrace ze starého ApplicationInsightsLoggerProvider

Microsoft. ApplicationInsights. AspNet SDK verze předtím, než 2.7.1 podporuje poskytovatele protokolování, který je nyní zastaralý. Tento zprostředkovatel byl povolen prostřednictvím rozšiřující metody **AddApplicationInsights ()** ILoggerFactory. Doporučujeme migrovat na nového poskytovatele, který zahrnuje dva kroky:

1. Odeberte volání *ILoggerFactory. AddApplicationInsights ()* z metody **Startup.Configurovat ()** , abyste se vyhnuli dvojímu protokolování.
2. Znovu použijte všechna pravidla filtrování v kódu, protože je nový zprostředkovatel nedodržuje. Přetížení *ILoggerFactory. AddApplicationInsights ()* převzaly minimální funkce LogLevel nebo Filter. U nového zprostředkovatele je filtrování součástí samotného protokolovacího rozhraní. Neprovádí se poskytovatelem Application Insights. Proto by se měly odebrat všechny filtry, které jsou poskytovány prostřednictvím *ILoggerFactory. AddApplicationInsights ()* . A pravidla filtrování by se měla poskytnout podle pokynů pro [úroveň protokolování ovládacího prvku](#control-logging-level) . Pokud použijete *appsettings.js* pro k filtrování protokolování, bude i nadále fungovat s novým poskytovatelem, protože používá stejný alias poskytovatele *ApplicationInsights*.

Můžete pořád používat starého poskytovatele. (Bude odebráno pouze v hlavní verzi, změna na 3. *XX*.), doporučujeme ale migrovat na nového poskytovatele z následujících důvodů:

- Předchozí poskytovatel nemá podporu pro [rozsahy protokolů](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). V novém zprostředkovateli jsou vlastnosti z oboru automaticky přidány do shromážděné telemetrie jako vlastní vlastnosti.
- Protokoly se teď můžou v kanálu spuštění aplikace zachytit mnohem dřív. Protokoly z **programu** a **spouštěcí** třídy se teď dají zachytit.
- U nového poskytovatele se filtrování provádí na úrovni architektury samotného. Protokoly můžete filtrovat do poskytovatele Application Insights stejným způsobem jako u jiných poskytovatelů, včetně integrovaných zprostředkovatelů, jako je konzola, ladění a tak dále. Můžete také použít stejné filtry na více zprostředkovatelů.
- V ASP.NET Core (2,0 a novější) doporučujeme, abyste  [poskytovatele protokolování povolili](https://github.com/aspnet/Announcements/issues/255) pomocí metod rozšíření v ILoggingBuilder v samotné **program.cs** .

> [!Note]
> Nový poskytovatel je k dispozici pro aplikace, které cílí na NETSTANDARD 2.0 nebo novější. Od [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.14.0 a vyšší jsou k dispozici také nové poskytovatele pro aplikace cílené .NET Framework NET461 nebo novější. Pokud vaše aplikace cílí na starší verze .NET Core, jako je například .NET Core 1,1, nebo pokud cílí na .NET Framework menší než NET46, pokračujte v používání starého poskytovatele.

## <a name="console-application"></a>Konzolová aplikace

> [!NOTE]
> K dispozici je nová Application Insights SDK označovaná jako [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , která se dá použít k povolení Application Insights (ILogger a jiné Application Insights telemetrie) pro jakékoli konzolové aplikace. Doporučuje se použít tento balíček a související pokyny [odsud.](./worker-service.md)

Následující kód ukazuje ukázkovou konzolovou aplikaci, která je nakonfigurovaná tak, aby odesílala ILogger trasování do Application Insights.

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

V tomto příkladu se používá samostatný balíček `Microsoft.Extensions.Logging.ApplicationInsights` . Ve výchozím nastavení tato konfigurace používá pro posílání dat Application Insights "neúplné" TelemetryConfiguration. Minimální znamená, že InMemoryChannel je kanál, který se používá. Neexistuje žádný odběr vzorků ani standardní TelemetryInitializers. Toto chování lze přepsat pro konzolovou aplikaci, jak ukazuje následující příklad.

Nainstalovat tento další balíček:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

Následující část ukazuje, jak přepsat výchozí TelemetryConfiguration pomocí metody **services.Configurovat \<TelemetryConfiguration> ()** . Tento příklad nastavuje `ServerTelemetryChannel` a vzorkování. Přidá vlastní ITelemetryInitializer do TelemetryConfiguration.

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

ASP.NET Coreý *ILogger* má integrovaný mechanismus pro použití [filtrování protokolu](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). To vám umožní řídit protokoly odesílané Každému zaregistrovanému zprostředkovateli, včetně poskytovatele Application Insights. Filtrování lze provést buď v konfiguraci (obvykle pomocí *appsettings.jsv* souboru), nebo v kódu. Toto zařízení je poskytované samotným rozhraním. Nejedná se o konkrétního poskytovatele Application Insights.

Následující příklady používají pravidla filtru pro ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Vytvořit pravidla filtru v konfiguraci s appsettings.jsna

Pro ApplicationInsightsLoggerProvider je alias poskytovatele `ApplicationInsights` . V následující části *appsettings.jsv* tématu je uvedeno, že poskytovatelé protokolování většinou přihlásí *Upozornění* na úrovni a výše. Pak přepíše `ApplicationInsightsLoggerProvider` Kategorie protokolů, které začínají na "Microsoft" na úrovni *Chyba* a vyšší.

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

### <a name="create-filter-rules-in-code"></a>Vytváření pravidel filtru v kódu

Následující fragment kódu Konfiguruje protokoly pro *Upozornění* a výše ze všech kategorií a pro *chyby* a vyšší z kategorií, které začínají na Microsoft, aby se odesílaly do `ApplicationInsightsLoggerProvider` . Tato konfigurace je stejná jako v předchozí části v *appsettings.jsna*.

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

[Sada Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) zahrnovala integrovanou ApplicationInsightsLoggerProvider (Microsoft. ApplicationInsights. AspNetCore. Logging. ApplicationInsightsLoggerProvider), která byla povolena prostřednictvím rozšiřujících metod **ILoggerFactory** . Tento zprostředkovatel je označený jako zastaralý z 2.7.1 verze. Dojde k úplnému odebrání v další změně hlavní verze. Samotný balíček [Microsoft. ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) není zastaralý. Je nutné povolit monitorování požadavků, závislostí a tak dále.

Navrhovaná alternativa je nový samostatný balíček [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), který obsahuje vylepšené ApplicationInsightsLoggerProvider (Microsoft. Extensions. Logging. ApplicationInsights. ApplicationInsightsLoggerProvider) a metody rozšíření pro ILoggerBuilder pro jeho povolení.

[Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.7.1 přebírá závislost na novém balíčku a povoluje automatické zachytávání ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Proč jsou některé protokoly ILogger dvakrát zobrazené v Application Insights?

Duplikace může nastat, pokud máte starší (nyní zastaralou) verzi ApplicationInsightsLoggerProvider povolenou zavoláním metody `AddApplicationInsights` `ILoggerFactory` . Ověřte, zda vaše metoda **Konfigurace** má následující příkaz, a odeberte ji:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Pokud při ladění ze sady Visual Studio dojde k dvojímu protokolování, nastavte `EnableDebugLogger` na  *hodnotu false* v kódu, který umožňuje Application Insights, následovně. Tato duplicita a oprava je relevantní pouze při ladění aplikace.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Aktualizoval (a) jsem se na [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.7.1 a protokoly z ILogger jsou zachyceny automaticky. Návody zcela tuto funkci vypnout?

Informace o tom, jak filtrovat protokoly obecně, najdete v části [řízení úrovně protokolování](#control-logging-level) . Pro vypnutí ApplicationInsightsLoggerProvider použijte `LogLevel.None` :

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

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Proč některé protokoly ILogger nemají stejné vlastnosti jako jiné?

Application Insights zachycuje a odesílá protokoly ILogger pomocí stejného TelemetryConfiguration, který se používá pro všechny ostatní telemetrie. Ale existuje výjimka. Ve výchozím nastavení není TelemetryConfiguration plně nastaven při protokolování z **program.cs** nebo **Startup.cs**. Protokoly z těchto míst nebudou mít výchozí konfiguraci, takže nebudou spouštět všechny TelemetryInitializers a TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Používám samostatný balíček Microsoft. Extensions. Logging. ApplicationInsights a chci ručně zaznamenat některé další vlastní telemetrie. Jak to mám udělat?

Při použití samostatného balíčku není `TelemetryClient` vložena do kontejneru di, takže je třeba vytvořit novou instanci `TelemetryClient` a použít stejnou konfiguraci jako poskytovatel protokolovacího nástroje, jak ukazuje následující kód. Tím se zajistí, že se stejná konfigurace použije pro všechny vlastní telemetrie i telemetrie z ILogger.

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
> Použijete-li balíček Microsoft. ApplicationInsights. AspNetCore k povolení Application Insights, upravte tento kód tak, aby se `TelemetryClient` přímo v konstruktoru získal. Příklad najdete v [těchto nejčastějších dotazech](./asp-net-core.md#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Jaký typ telemetrie Application Insights se vyrábí z protokolů ILogger? Nebo kde můžu zobrazit protokoly ILogger v Application Insights?

ApplicationInsightsLoggerProvider zachycuje protokoly ILogger a z nich vytvoří TraceTelemetry. Pokud je objekt výjimky předán metodě **log ()** v ILogger, vytvoří se *ExceptionTelemetry* namísto TraceTelemetry. Tyto položky telemetrie se dají najít na stejných místech jako jakékoli jiné TraceTelemetry nebo ExceptionTelemetry pro Application Insights, včetně portálu, analýzy nebo místního ladicího programu sady Visual Studio.

Pokud chcete vždy odeslat TraceTelemetry, použijte tento fragment kódu: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nemám nainstalovanou sadu SDK a k povolení Application Insights pro moje ASP.NET Core aplikace používám rozšíření Azure Web Apps. Návody použít nového poskytovatele? 

Rozšíření Application Insights v Azure Web Apps používá nového poskytovatele. Pravidla filtrování můžete upravit v *appsettings.js* souboru pro vaši aplikaci.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Používám samostatný balíček Microsoft. Extensions. Logging. ApplicationInsights a povoluje poskytovatele Application Insights voláním **Tvůrce. AddApplicationInsights ("ikey")**. Existuje možnost získat klíč instrumentace z konfigurace?


Upravte Program.cs a appsettings.js, jak je znázorněno níže:

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

   Relevantní část z `appsettings.json` :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Tento kód je vyžadován pouze v případě, že používáte samostatného zprostředkovatele protokolování. Pro běžné Application Insights monitorování se klíč instrumentace načítá automaticky z konfigurační cesty *ApplicationInsights: Instrumentationkey*. Appsettings.jsby měl vypadat takto:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace:

* [Přihlašování ASP.NET Core](/aspnet/core/fundamentals/logging)
* [Protokoly trasování .NET v Application Insights](./asp-net-trace-logs.md)

