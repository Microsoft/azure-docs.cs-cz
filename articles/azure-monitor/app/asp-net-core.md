---
title: Application Insights Azure pro ASP.NET Core aplikace | Microsoft Docs
description: Monitorujte ASP.NET Core webové aplikace pro účely dostupnosti, výkonu a využití.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/30/2020
ms.openlocfilehash: be4cbe5f95fa6901ae4299662750c232d4700a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711497"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights pro ASP.NET Core aplikace

Tento článek popisuje, jak povolit Application Insights pro [ASP.NET Core](/aspnet/core) aplikaci. Po dokončení pokynů v tomto článku bude Application Insights shromažďovat požadavky, závislosti, výjimky, čítače výkonu, prezenční signály a protokoly z vaší aplikace ASP.NET Core.

Příklad, který budeme používat, je [aplikace MVC](/aspnet/core/tutorials/first-mvc-app) , která se zaměřuje na `netcoreapp3.0` . Tyto pokyny můžete použít pro všechny ASP.NET Core aplikace. Pokud používáte [službu pracovního procesu](/aspnet/core/fundamentals/host/hosted-services#worker-service-template), postupujte podle pokynů [zde](./worker-service.md).

## <a name="supported-scenarios"></a>Podporované scénáře

[Sada Application Insights SDK pro ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) může monitorovat aplikace bez ohledu na to, kde nebo jak jsou spuštěny. Pokud vaše aplikace běží a má síťové připojení k Azure, je možné shromažďovat telemetrii. Monitorování Application Insights je podporováno všude, kde je podporováno rozhraní .NET Core. Podpora zahrnuje:
* **Operační systém**: Windows, Linux nebo Mac.
* **Metoda hostování**: v procesu nebo mimo proces.
* **Metoda nasazení**: závislá na architektuře nebo samostatně obsažená.
* **Webový server**: IIS (Internet Information Server) nebo Kestrel.
* **Hostující platforma**: funkce Web Apps Azure App Service, virtuální počítač Azure, Docker, Azure Kubernetes Service (AKS) a tak dále.
* **Verze .NET Core**: všechny oficiálně [podporované](https://dotnet.microsoft.com/download/dotnet-core) verze .NET Core.
* **IDE**: Visual Studio, vs Code nebo příkazový řádek.

> [!NOTE]
> ASP.NET Core 3,1 vyžaduje [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) nebo novější.

## <a name="prerequisites"></a>Požadavky

- Funkční aplikace ASP.NET Core. Pokud potřebujete vytvořit aplikaci ASP.NET Core, postupujte podle tohoto [ASP.NET Core kurzu](/aspnet/core/getting-started/).
- Platný klíč instrumentace Application Insights. Tento klíč je nutný k odeslání jakékoli telemetrie do Application Insights. Pokud potřebujete vytvořit nový prostředek Application Insights, abyste získali klíč instrumentace, přečtěte si téma [vytvoření prostředku Application Insights](./create-new-resource.md).

> [!IMPORTANT]
> Nové oblasti Azure **vyžadují** použití připojovacích řetězců místo klíčů instrumentace. [Připojovací řetězec](./sdk-connection-string.md?tabs=net) identifikuje prostředek, ke kterému chcete přidružit data telemetrie. Umožňuje také upravit koncové body, které prostředek použije jako cíl pro vaši telemetrii. Budete muset zkopírovat připojovací řetězec a přidat ho do kódu aplikace nebo do proměnné prostředí.


## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Povolit Application Insights telemetrie na straně serveru (Visual Studio)

Pro Visual Studio pro Mac použít [Ruční pokyny](#enable-application-insights-server-side-telemetry-no-visual-studio). Tento postup podporuje pouze verze systému Windows sady Visual Studio.

1. Otevřete svůj projekt v sadě Visual Studio.

    > [!TIP]
    > Pokud chcete, můžete pro svůj projekt nastavit správu zdrojového kódu, abyste mohli sledovat všechny změny, které Application Insights provede. Chcete-li povolit správu zdrojového kódu, vyberte **soubor**  >  **Přidat do správy zdrojového kódu**.

2. Vyberte **projekt**  >  **Přidat telemetrie Application Insights**.

3. Vyberte **Začínáme**. Text tohoto výběru se může lišit v závislosti na vaší verzi sady Visual Studio. Některé starší verze používají místo toho tlačítko **Spustit zdarma** .

4. Vyberte své předplatné. Pak vyberte položku  >  **registr** prostředků.

5. Po přidání Application Insights do projektu ověřte, že používáte nejnovější stabilní verzi sady SDK. Přejít na **projekt**  >  **Správa balíčků NuGet**  >  **Microsoft. ApplicationInsights. AspNetCore** Pokud potřebujete, klikněte na tlačítko **aktualizovat**.

     ![Snímek obrazovky s informacemi o tom, kde vybrat balíček Application Insights pro aktualizaci](./media/asp-net-core/update-nuget-package.png)

6. Pokud jste postupovali podle volitelného tipu a Přidali jste projekt do správy zdrojových kódů, přejdete na **Zobrazit**  >  **Team Explorer**  >  **změny**. Pak vyberte jednotlivé soubory, abyste viděli rozdílové zobrazení změn provedených v telemetrie Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Povolit Application Insights telemetrie na straně serveru (bez sady Visual Studio)

1. Nainstalujte [balíček NuGet sady Application Insights SDK pro ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Doporučujeme vždy používat nejnovější stabilní verzi. Vyhledejte úplné poznámky k verzi pro sadu SDK na [Open Source úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/releases).

    Následující ukázka kódu ukazuje změny, které mají být přidány do `.csproj` souboru projektu.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.16.0" />
        </ItemGroup>
    ```

2. Přidejte `services.AddApplicationInsightsTelemetry();` do `ConfigureServices()` metody ve `Startup` třídě, jako v tomto příkladu:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Nastavte klíč instrumentace.

    I když můžete zadat klíč instrumentace jako argument pro, doporučujeme `AddApplicationInsightsTelemetry` zadat klíč instrumentace v konfiguraci. Následující ukázka kódu ukazuje, jak zadat klíč instrumentace v `appsettings.json` . Ujistěte se, že `appsettings.json` se během publikování kopíruje do kořenové složky aplikace.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Případně můžete zadat klíč instrumentace v některé z následujících proměnných prostředí:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Například:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY` se obvykle používá v [Azure Web Apps](./azure-web-apps.md?tabs=net), ale dá se použít i na všech místech, kde je tato sada SDK podporovaná. (Pokud provádíte monitorování webové aplikace bez kódu, je tento formát vyžadován, pokud nepoužíváte připojovací řetězce.)

    Místo nastavování klíčů instrumentace teď můžete také použít [připojovací řetězce](./sdk-connection-string.md?tabs=net).

    > [!NOTE]
    > Klíč instrumentace zadaný v kódu služby WINS přes proměnnou prostředí `APPINSIGHTS_INSTRUMENTATIONKEY` , která je službou WINS nad jinými možnostmi.

### <a name="user-secrets-and-other-configuration-providers"></a>Uživatelské klíče a další poskytovatelé konfigurace

Pokud chcete uložit klíč instrumentace v ASP.NET Core uživatelských tajných klíčích nebo si ho načíst z jiného poskytovatele konfigurace, můžete použít přetížení s `Microsoft.Extensions.Configuration.IConfiguration` parametrem. Například, `services.AddApplicationInsightsTelemetry(Configuration);`.
Od Microsoft. ApplicationInsights. AspNetCore verze [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)volání `services.AddApplicationInsightsTelemetry()` automaticky přečte klíč instrumentace z `Microsoft.Extensions.Configuration.IConfiguration` aplikace. Není nutné explicitně poskytnout `IConfiguration` .

## <a name="run-your-application"></a>Spusťte aplikaci

Spusťte aplikaci a proveďte na ni požadavky. Telemetrii by teď měla přesměrovat do Application Insights. Sada Application Insights SDK automaticky shromažďuje příchozí webové požadavky do vaší aplikace spolu s následující telemetrii.

### <a name="live-metrics"></a>Live Metrics

[Živé metriky](./live-stream.md) lze použít k rychlému ověření, zda je monitorování Application Insights správně nakonfigurováno. I když může trvat několik minut, než se telemetrie spustí na portálu a v analýze, zobrazí se v reálném čase využití CPU běžícího procesu téměř v reálném čase. Může také zobrazit další telemetrie, jako jsou požadavky, závislosti, trasování atd.

### <a name="ilogger-logs"></a>Protokoly ILogger

Výchozí konfigurace shromažďuje `ILogger` protokoly závažnost `Warning` a vyšší. Tato konfigurace se dá [přizpůsobit](#how-do-i-customize-ilogger-logs-collection).

### <a name="dependencies"></a>Závislosti

Kolekce závislostí je ve výchozím nastavení povolená. [Tento](asp-net-dependencies.md#automatically-tracked-dependencies) článek vysvětluje závislosti, které jsou shromažďovány automaticky, a obsahuje také Postup ručního sledování.

### <a name="performance-counters"></a>Čítače výkonu

Podpora [čítačů výkonu](./performance-counters.md) v ASP.NET Core je omezená:

* Sady SDK verze 2.4.1 a novější shromažďují čítače výkonu, pokud aplikace běží v Azure Web Apps (Windows).
* Verze sady SDK 2.7.1 a novější shromažďují čítače výkonu, pokud je aplikace spuštěná ve Windows a cílících `NETSTANDARD2.0` nebo novějších.
* Pro aplikace cílené na .NET Framework všechny verze sady SDK podporují čítače výkonu.
* Verze sady SDK 2.8.0 a novější podporují čítač procesorů a paměti v systému Linux. V systému Linux není podporován žádný jiný čítač. Doporučený způsob, jak získat systémové čítače v systémech Linux (a dalších prostředích mimo Windows), je použití [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` je ve výchozím nastavení povoleno. V kurzu [EventCounter](eventcounters.md) najdete pokyny ke konfiguraci seznamu čítačů, které se mají shromažďovat.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Povolení telemetrie na straně klienta pro webové aplikace

Předchozí kroky jsou dostatečné, aby vám pomohly začít shromažďovat telemetrie na straně serveru. Pokud má aplikace komponenty na straně klienta, spusťte následující postup a zahajte shromažďování [telemetrie využití](./usage-overview.md).

1. Do `_ViewImports.cshtml` přidejte injektáže:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. V nástroji `_Layout.cshtml` vložte `HtmlHelper` na konec `<head>` oddílu, ale před jakýkoli jiný skript. Pokud chcete vykázat jakékoli vlastní telemetrie JavaScriptu ze stránky, zastavte ji za tento fragment kódu:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Alternativně můžete použít `FullScript` sadu, která `ScriptBody` je k dispozici od verze sady SDK v 2.14. Tento postup použijte v případě, že potřebujete řídit `<script>` značku pro nastavení zásad zabezpečení obsahu:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

Názvy souborů, na které se `.cshtml` odkazuje dříve, jsou z výchozí šablony aplikace MVC. V konečném případě, pokud chcete pro vaši aplikaci správně povolit monitorování na straně klienta, musí být fragment kódu JavaScriptu uveden v `<head>` části každé stránky aplikace, kterou chcete monitorovat. Tento cíl můžete pro tuto šablonu aplikace dosáhnout přidáním fragmentu JavaScriptu do `_Layout.cshtml` . 

Pokud váš projekt nezahrnuje `_Layout.cshtml` , můžete přesto přidat [monitorování na straně klienta](./website-monitoring.md). To můžete provést tak, že přidáte fragment kódu jazyka JavaScript do ekvivalentního souboru, který ovládá `<head>` všechny stránky v aplikaci. Nebo můžete fragment přidat na více stránek, ale toto řešení je obtížné udržovat a obecně nedoporučujeme.

## <a name="configure-the-application-insights-sdk"></a>Konfigurace sady Application Insights SDK

Můžete přizpůsobit sadu Application Insights SDK, aby ASP.NET Core změnila výchozí konfiguraci. Uživatelé sady Application Insights ASP.NET SDK mohou být obeznámeni se změnou konfigurace pomocí nástroje `ApplicationInsights.config` nebo úpravou `TelemetryConfiguration.Active` . Pro ASP.NET Core se téměř všechny změny konfigurace provádí v `ConfigureServices()` metodě vaší `Startup.cs` třídy, pokud nebudete přesměrováni jinak. Následující části obsahují další informace.

> [!NOTE]
> V ASP.NET Corech aplikacích se změna konfigurace podle úpravy `TelemetryConfiguration.Active` nepodporuje.

### <a name="using-applicationinsightsserviceoptions"></a>Použití ApplicationInsightsServiceOptions

Můžete upravit několik běžných nastavení tak, že předáte `ApplicationInsightsServiceOptions` do `AddApplicationInsightsTelemetry` , jako v tomto příkladu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

Úplný seznam nastavení v `ApplicationInsightsServiceOptions`

|Nastavení | Popis | Výchozí
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Povolit/zakázat `PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Povolit/zakázat `RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Povolit/zakázat `EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Povolit/zakázat `DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Povolit/zakázat `AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Povolit/zakázat `AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Povolit nebo zakázat funkci LiveMetrics | true
|EnableAdaptiveSampling | Povolit/zakázat adaptivní vzorkování | true
|EnableHeartbeat | Povolí nebo zakáže funkci prezenčních signálů, které pravidelně (ve výchozím nastavení 15 minut) pošle vlastní metriku s názvem HeartbeatState s informacemi o modulu runtime, jako je verze .NET, informace o prostředí Azure, pokud jsou k dispozici atd. | true
|AddAutoCollectedMetricExtractor | Povolí nebo zakáže extraktor AutoCollectedMetrics, což je TelemetryProcessor, který posílá předem agregované metriky o požadavcích a závislostech, než proběhne vzorkování. | true
|RequestCollectionOptions.TrackExceptions | Povolí nebo zakáže vytváření sestav neošetřené sledování výjimek v modulu shromažďování požadavků. | false v NETSTANDARD 2.0 (protože výjimky jsou sledovány pomocí ApplicationInsightsLoggerProvider), v opačném případě true.
|EnableDiagnosticsTelemetryModule | Povolit/zakázat `DiagnosticsTelemetryModule` . Zakázáním této možnosti dojde k ignorování následujících nastavení; `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

Seznam [konfigurovatelných nastavení v nástroji `ApplicationInsightsServiceOptions` najdete v](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) tématu seznam nejaktuálnějších dat.

### <a name="configuration-recommendation-for-microsoftapplicationinsightsaspnetcore-sdk-2150--above"></a>Doporučení konfigurace pro Microsoft. ApplicationInsights. AspNetCore SDK 2.15.0 & výše

Od Microsoft. ApplicationInsights. AspNetCore SDK verze [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0)se doporučuje nakonfigurovat všechna nastavení dostupná v `ApplicationInsightsServiceOptions` , včetně instrumentationkey použití `IConfiguration` instance aplikace. Nastavení musí být v části "ApplicationInsights", jak je znázorněno v následujícím příkladu. Následující část appsettings.jsv tématu Konfigurace klíče instrumentace a také vypnutí adaptivního vzorkování a shromažďování čítačů výkonu.

```json
{
    "ApplicationInsights": {
    "InstrumentationKey": "putinstrumentationkeyhere",
    "EnableAdaptiveSampling": false,
    "EnablePerformanceCounterCollectionModule": false
    }
}
```

Pokud `services.AddApplicationInsightsTelemetry(aiOptions)` je použit, přepíše nastavení z `Microsoft.Extensions.Configuration.IConfiguration` .

### <a name="sampling"></a>Vzorkování

Sada Application Insights SDK pro ASP.NET Core podporuje fixní i adaptivní vzorkování. Adaptivní vzorkování je ve výchozím nastavení povolené.

Další informace najdete v tématu [Konfigurace adaptivního vzorkování pro aplikace ASP.NET Core](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Přidání TelemetryInitializers

[Inicializátory telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) použijte, když chcete rozšířit telemetrii s dalšími informacemi.

Přidejte všechny nové `TelemetryInitializer` kontejnery do `DependencyInjection` kontejneru, jak je znázorněno v následujícím kódu. Sada SDK automaticky vybere všechny `TelemetryInitializer` , které jsou přidány do `DependencyInjection` kontejneru.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

> [!NOTE]
> `services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();` funguje pro jednoduché Inicializátory. Pro ostatní se vyžadují následující: `services.AddSingleton(new MyCustomTelemetryInitializer() { fieldName = "myfieldName" });`
    
### <a name="removing-telemetryinitializers"></a>Odebírá se TelemetryInitializers

Ve výchozím nastavení jsou Inicializátory telemetrie přítomné. Chcete-li odebrat všechny nebo konkrétní Inicializátory telemetrie, použijte následující vzorový kód *po* volání `AddApplicationInsightsTelemetry()` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

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

Vlastní procesory telemetrie můžete přidat `TelemetryConfiguration` pomocí metody rozšíření `AddApplicationInsightsTelemetryProcessor` na `IServiceCollection` . Procesory telemetrie se používají ve [scénářích pokročilého filtrování](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer). Použijte následující příklad.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurace nebo odebrání výchozích TelemetryModules

Application Insights používá moduly telemetrie k automatickému shromažďování užitečných telemetrie o konkrétních úlohách, aniž by bylo nutné ručně sledovat uživatele.

Ve výchozím nastavení jsou povoleny následující moduly automatických kolekcí. Tyto moduly zodpovídají za automatické shromažďování telemetrie. Můžete je zakázat nebo nakonfigurovat, aby se změnily jejich výchozí chování.

* `RequestTrackingTelemetryModule` – Shromáždí RequestTelemetry z příchozích webových požadavků.
* `DependencyTrackingTelemetryModule` – Shromažďuje [DependencyTelemetry](./asp-net-dependencies.md) z odchozích volání http a volání SQL.
* `PerformanceCollectorModule` -Shromažďuje Windows čítače výkonu.
* `QuickPulseTelemetryModule` – Shromažďuje telemetrii pro zobrazení na portálu živých metrik.
* `AppServicesHeartbeatTelemetryModule` – Shromažďuje srdce Beats (které se odesílají jako vlastní metriky) o Azure App Service prostředí, ve kterém je aplikace hostovaná.
* `AzureInstanceMetadataTelemetryModule` – Shromažďuje srdce Beats (které se odesílají jako vlastní metriky) o prostředí Azure VM, ve kterém je aplikace hostovaná.
* `EventCounterCollectionModule` – Shromažďuje [EventCounters.](eventcounters.md) Tento modul je novou funkcí a je k dispozici v sadě SDK verze 2.8.0 a vyšších.

Pro konfiguraci všech výchozích možností `TelemetryModule` použijte metodu rozšíření `ConfigureTelemetryModule<T>` na `IServiceCollection` , jak je znázorněno v následujícím příkladu.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

Počínaje verzí 2.12.2 [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) obsahuje snadnou možnost pro zákaz všech výchozích modulů.

### <a name="configuring-a-telemetry-channel"></a>Konfigurace kanálu telemetrie

Výchozí [kanál telemetrie](./telemetry-channels.md) je `ServerTelemetryChannel` . Můžete ho přepsat, jak ukazuje následující příklad.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Dynamické vypnutí telemetrie

Pokud chcete vypnout telemetrii podmíněně a dynamicky, můžete vyřešit `TelemetryConfiguration` instanci s ASP.NET Core kontejnerem vkládání závislostí kdekoli v kódu a nastavit `DisableTelemetry` příznak.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

Výše uvedené nebrání žádnému modulu automatické kolekce v shromažďování telemetrie. U výše uvedeného přístupu se neaktivuje jenom odesílání telemetrie na Application Insights. Pokud není určitý modul automatické kolekce žádoucí, je nejlepší [odebrat modul telemetrie](#configuring-or-removing-default-telemetrymodules) .

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-application-insights-support-aspnet-core-3x"></a>Podporuje Application Insights ASP.NET Core 3. X?

Ano. Aktualizace na [sadu Application Insights SDK pro ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.8.0 nebo vyšší. Starší verze sady SDK nepodporují ASP.NET Core 3. X.

Také Pokud používáte pokyny na základě sady Visual Studio z [tohoto místa](#enable-application-insights-server-side-telemetry-visual-studio), aktualizujte na zprovoznění nejnovější verzi sady visual Studio 2019 (16.3.0). Předchozí verze sady Visual Studio nepodporují automatickou registraci pro aplikace ASP.NET Core 3. X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Jak můžu sledovat telemetrii, která se automaticky neshromažďuje?

Získání instance `TelemetryClient` pomocí injektáže konstruktoru a volání požadované `TrackXXX()` metody. Nedoporučujeme vytvářet nové `TelemetryClient` `TelemetryConfiguration` instance ani instance v ASP.NET Core aplikaci. Instance typu Singleton `TelemetryClient` je již v kontejneru zaregistrována `DependencyInjection` , která sdílí `TelemetryConfiguration` se zbytkem telemetrie. Vytvoření nové `TelemetryClient` instance se doporučuje jenom v případě, že potřebuje konfiguraci, která je oddělená od zbytku telemetrie.

Následující příklad ukazuje, jak sledovat další telemetrii z kontroleru.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Další informace o vlastních datových sestavách v Application Insights najdete v tématu [Application Insights referenční informace k rozhraní API pro vlastní metriky](./api-custom-events-metrics.md). Podobný přístup lze použít k posílání vlastních metrik pro Application Insights pomocí [rozhraní Getmetric API](./get-metric.md).

### <a name="how-do-i-customize-ilogger-logs-collection"></a>Návody přizpůsobit kolekci protokolů ILogger?

Ve výchozím nastavení `Warning` jsou zachyceny pouze protokoly závažnost a výše. Chcete-li toto chování změnit, explicitně popište konfiguraci protokolování pro poskytovatele `ApplicationInsights` , jak je uvedeno níže.
Následující konfigurace umožňuje ApplicationInsights zachytit všechny protokoly závažnosti `Information` a vyšší.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
}
```

Je důležité si uvědomit, že následující informace nezpůsobí, že poskytovatel ApplicationInsights zachytí `Information` protokoly. Důvodem je to, že sada SDK přidává výchozí filtr protokolování, `ApplicationInsights` který dává pokyn k zachycení jenom `Warning` a vyšších. Z tohoto důvodu je pro ApplicationInsights vyžadováno Explicitní přepsání.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

Přečtěte si další informace o [konfiguraci ILogger](ilogger.md#control-logging-level).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Některé šablony sady Visual Studio používaly metodu rozšíření UseApplicationInsights () na IWebHostBuilder pro povolení Application Insights. Je toto použití stále platné?

I když je rozšiřující metoda `UseApplicationInsights()` stále podporovaná, je označená jako zastaralá v sadě Application Insights SDK verze 2.8.0 a vyšší. V další hlavní verzi sady SDK se odebere. Doporučený způsob, jak povolit telemetrii Application Insights, je použití, `AddApplicationInsightsTelemetry()` protože poskytuje přetížení k řízení některých konfigurací. V aplikacích ASP.NET Core 3. X `services.AddApplicationInsightsTelemetry()` je také jediným způsobem, jak povolit službu Application Insights.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Nasazujem moji aplikaci ASP.NET Core do Web Apps. Mám pořád povolit rozšíření Application Insights z Web Apps?

Pokud je sada SDK nainstalována v době sestavení, jak je znázorněno v tomto článku, není nutné povolit [rozšíření Application Insights](./azure-web-apps.md) z portálu App Service. I v případě, že je rozšíření nainstalováno, bude po zjištění, že sada SDK již přidána do aplikace, vrácena. Pokud povolíte Application Insights z rozšíření, nemusíte instalovat a aktualizovat sadu SDK. Pokud ale povolíte Application Insights podle pokynů v tomto článku, máte větší flexibilitu z těchto důvodů:

   * Application Insights telemetrie bude i nadále fungovat v:
       * Všechny operační systémy, včetně systémů Windows, Linux a Mac.
       * Všechny režimy publikování, včetně samostatného nebo závislého rozhraní.
       * Všechny cílové architektury, včetně úplného .NET Framework.
       * Všechny možnosti hostování, včetně Web Apps, virtuálních počítačů, Linuxů, kontejnerů, služby Azure Kubernetes a hostování mimo Azure.
       * Všechny verze .NET Core včetně verze Preview
   * Telemetrii můžete zobrazit lokálně při ladění ze sady Visual Studio.
   * Můžete sledovat další vlastní telemetrii pomocí `TrackXXX()` rozhraní API.
   * Máte plnou kontrolu nad konfigurací.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Můžu Application Insights monitorování povolit pomocí nástrojů jako Monitorování stavu?

No. [Monitorování stavu](./monitor-performance-live-website-now.md) a [monitorování stavu v2](./status-monitor-v2-overview.md) aktuálně podporují pouze ASP.NET 4. x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Pokud Spouštím aplikaci v systému Linux, jsou podporovány všechny funkce?

Ano. Podpora funkcí pro sadu SDK je stejná na všech platformách, s následujícími výjimkami:

* Sada SDK shromažďuje [čítače událostí](./eventcounters.md) v systému Linux, protože [čítače výkonu](./performance-counters.md) jsou podporovány pouze ve Windows. Většina metrik je stejná.
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
        services.AddApplicationInsightsTelemetry();
    }
```

Toto omezení se nevztahuje na [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) a novější verze.

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Podporuje se tato sada SDK pro nové aplikace šablon pracovních služeb .NET Core 3. X?

Tato sada SDK vyžaduje `HttpContext` , a proto nefunguje v aplikacích, které nepoužívají protokol HTTP, včetně aplikací pracovní služby .NET Core 3. X Worker. V [tomto](worker-service.md) dokumentu najdete informace o povolení Application Insights v takových aplikacích s využitím nově vydané sady Microsoft. ApplicationInsights. WorkerService SDK.

## <a name="open-source-sdk"></a>Open-Source sada SDK

* [Číst a přispívat do kódu](https://github.com/microsoft/ApplicationInsights-dotnet).

Nejnovější aktualizace a opravy chyb [najdete v poznámkách k verzi](./release-notes.md).

## <a name="next-steps"></a>Další kroky

* [Prozkoumejte toky uživatelů](./usage-flows.md) a pochopte, jak uživatelé procházejí vaší aplikací.
* [Konfigurace kolekce snímků](./snapshot-debugger.md) pro zobrazení stavu zdrojového kódu a proměnných v okamžiku, kdy je vyvolána výjimka.
* [Použijte rozhraní API](./api-custom-events-metrics.md) k posílání vlastních událostí a metrik pro podrobné zobrazení výkonu a využití vaší aplikace.
* Pomocí [testů dostupnosti](./monitor-web-app-availability.md) můžete svou aplikaci průběžně kontrolovat z celého světa.
* [Injektáž závislostí v ASP.NET Core](/aspnet/core/fundamentals/dependency-injection)
