---
title: Azure Application Insights pro ASP.NET Core | Dokumentace Microsoftu
description: Monitorování webových aplikací ASP.NET Core pro dostupnost, výkon a využití.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: cb7ace20fd0a59dafff3d7f8240f54c3c8e12492
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226403"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights pro aplikace ASP.NET Core

Tento článek popisuje, jak povolit Application Insights pro [ASP.NET Core](https://docs.microsoft.com/aspnet/core) aplikace. Pokud provedete všechny pokyny v tomto článku, Application Insights začne shromažďování požadavky, závislosti, výjimky, čítače výkonu, prezenční signály a protokoly z aplikace ASP.NET Core. Ukázková aplikace je [aplikace MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) cílení `netcoreapp2.2`, ale tyto pokyny platí pro všechny aplikace ASP.NET Core.

## <a name="supported-scenarios"></a>Podporované scénáře

[Application Insights SDK (Software Development Kit) pro ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) může sledovat vaše aplikace bez ohledu na to, kdy nebo jak při spuštění aplikace. Pokud vaše aplikace běží a má síťové připojení k Azure, se můžou shromažďovat telemetrii. To znamená, že monitorování pomocí Application Insights je podporován všude, kde se podporuje .NET Core. Tato podpora zahrnuje všechny operačního systému (Windows, Linux, Mac), metodu hostování (vs v procesu mimo proces), metoda nasazení (závisí na architektuře vs samostatná), webový Server (IIS, Kestrel), hostující platforma (Azure Web Apps, virtuální počítač Azure, Docker, Azure Kubernetes Service (AKS) a tak dále.) nebo integrované vývojové prostředí (Visual Studio, VS Code, příkazový řádek.)

## <a name="prerequisites"></a>Požadavky

- Funkční aplikace ASP.NET Core. Postupujte podle [příručku Začínáme s ASP.NET Core získávání](https://docs.microsoft.com/aspnet/core/getting-started/) k vytvoření aplikace ASP.NET Core, v případě potřeby.
- Platný Instrumentační klíč Application Insights, který je vyžadován pro odeslání všech telemetrických dat do služby Application Insights. Postupujte podle [vytvořit prostředek pokyny](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) a vytvořte nový prostředek Application Insights v případě potřeby získat Instrumentační klíč.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Povolit telemetrii Application Insights na straně serveru (Visual Studio)

1. Otevřete svůj projekt v sadě Visual Studio.

    > [!TIP]
    > Při krok není povinný může být užitečné nastavit správu zdrojového kódu pro váš projekt, takže můžete sledovat všechny změny provedené v Application Insights. Umožňuje vybrat ovládací prvek zdroje **souboru** > **přidat do správy zdrojových kódů**.

2. Vyberte **projektu** > **přidat Telemetrii Application Insights**.

3. Vyberte **Začínáme**. (V závislosti na vaší verzi sady Visual Studio, text se mohou mírně lišit. Mají některé starší verze **začít zdarma** tlačítko místo.)

4. Vyberte své předplatné a pak vyberte **prostředků** > **zaregistrovat**.

5. Po přidání Application Insights do projektu, zaškrtněte, pokud chcete potvrdit, že používáte nejnovější stabilní verzi sady SDK. Přejděte na **projektu** > **spravovat balíčky NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > v případě potřeby vyberte **Aktualizace**.

     ![Snímek obrazovky spravovat obrazovce balíček NuGet s vybrána pro aktualizaci balíčku Application Insights](./media/asp-net-core/update-nuget-package.png)

6. Pokud a volitelný popis tlačítka a přidá projekt do správy zdrojového kódu můžete přejít k **zobrazení** > **Team Exploreru** > **změny** a Vyberte jednotlivých souborů pro rozdílové zobrazení změn provedených v přidání telemetrie Application Insights.

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>Povolit telemetrii Application Insights na straně serveru (bez sady Visual Studio)

1. Nainstalujte [balíček NuGet sady SDK pro Application Insights pro ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Doporučujeme vždy používat nejnovější stabilní verzi. Kompletní poznámky k verzi sady SDK můžete najít na [otevřete úložiště zdrojového kódu GitHub](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Následující fragment kódu ukazuje změny se přidají do vašeho projektu `.csproj` souboru.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
        </ItemGroup>
    ```

2. Přidat `services.AddApplicationInsightsTelemetry();` k `ConfigureServices()` metoda ve vaší `Startup` třídy. Úplný příklad níže.

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // code adding other services for your application
            services.AddMvc();
        }
    ```

3. Nastavte klíč instrumentace.

    I když je možné zadat Instrumentační klíč jako argument `AddApplicationInsightsTelemetry`, doporučujeme zadat Instrumentační klíč v konfiguraci. Následující znázorňuje způsob zadávání instrumentačního klíče v `appsettings.json`. Ujistěte se, že `appsettings.json` zkopírována do kořenové složky aplikace při publikování.

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

    Klíč instrumentace můžete také můžete také uvést v některém z následujících proměnných prostředí.

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    Příklad:

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` Obvykle se používá k určení Instrumentační klíč pro aplikace nasazené do služby Azure Web Apps.

    > [!NOTE]
    > Instrumentačním klíčem zadaný ve službě wins kódu přes proměnnou prostředí `APPINSIGHTS_INSTRUMENTATIONKEY`, které služby wins za další možnosti.

## <a name="run-your-application"></a>Spusťte aplikaci

 Spusťte aplikaci a požadavky na ni. Telemetrická data by teď měly začít odesílaných do Application Insights. Následující telemetrická data se automaticky shromažďují pomocí Application Insights SDK.

|Požadavky a závislosti |Podrobnosti|
|---------------|-------|
|Požadavky | Příchozích webových požadavků do vaší aplikace. |
|Http/Https | Volání s `HttpClient`. |
|SQL | Volání s `SqlClient`. |
|[Úložiště Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) | Volání s klienta úložiště Azure. |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Verze 1.1.0 a vyšší. |
|[Klientská sada SDK pro služby Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Verze 3.0.0 a vyšší. |
|Azure Cosmos DB | Pokud se používá HTTP/HTTPS, pouze sledována automaticky. Režim TCP nebude zachycena Application Insights. |

### <a name="performance-counters"></a>Čítače výkonu

Podpora pro [čítače výkonu](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) v ASP.NET Core je omezená na následující

   * Sada SDK verze 2.4.1 a nad shromažďuje čítače výkonu, pokud je aplikace spuštěná ve službě Azure Web App (Windows)
   * Sada SDK verze 2.7.0-beta3 a nad shromažďuje čítače výkonu, pokud aplikace běží ve Windows a cílení `NETSTANDARD2.0` nebo vyšší.
   * Pro aplikace cílené na rozhraní .NET Framework čítače výkonu jsou podporovány ve všech verzích SDK.
   * Tento článek bude aktualizován, když se přidá podpora čítače výkonu v systému Linux.

### <a name="ilogger-logs"></a>ILogger protokoly

[Protokoly ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) závažnosti `Warning` nebo výše jsou automaticky zachycené ze sady SDK verze 2.7.0-beta3 nebo vyšší.

### <a name="live-metrics"></a>Živé metriky

Může trvat několik minut, než se telemetrická data začnou zobrazovat na portálu. Chcete-li rychle zkontrolovat, zda vše funguje, je nejvhodnější použít [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)při posílání požadavků na běžící aplikaci.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Povolit Telemetrii na straně klienta pro webové aplikace

Výše uvedené kroky jsou dostatečné pro začal shromažďovat telemetrii na straně serveru. Pokud má vaše aplikace komponenty na straně klienta, postupujte podle následujících kroků a spustit shromažďování [telemetrii jejich využívání](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) z něj.

1. V `_ViewImports.cshtml`, přidejte vkládání:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. V `_Layout.cshtml`, vložit na konec HtmlHelper `<head>` oddílu, ale před veškerým skriptem. Vlastní telemetrii jazyka JavaScript, které chcete do sestavy ze stránky by měl vloží po tento fragment kódu:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml` Názvy souborů uvedené výše jsou z výchozí šablony aplikace MVC. Nakonec správně povolit monitorování na straně klienta pro vaši aplikaci, je třeba fragment kódu jazyka JavaScript v `<head>` části všech stránek aplikace, kterou chcete monitorovat. Pro tuto šablonu aplikace javascriptový fragment kódu pro přidání `_Layout.cshtml` bude efektivně dosažení tohoto cíle. Pokud váš projekt nemá tento konkrétní soubor můžete přesto přidat [monitorování na straně klienta](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). By stačí potřebovat buď přidat jazyka JavaScript do souboru ekvivalentní ovládací prvky `<head>` všech stránek v rámci vaší aplikace, případně můžete přidat fragment kódu pro více jednotlivých stránek, i když to by bylo obtížné udržovat a není obecně doporučené.

## <a name="configuring-application-insights-sdk"></a>Konfigurace Application Insights SDK

Application Insights SDK pro ASP.NET Core je možné přizpůsobit pro změnu výchozí konfigurace. Uživatele sadu SDK Application Insights technologie ASP.NET pravděpodobně mají zkušenosti s pomocí konfigurace `ApplicationInsights.config`, nebo tak, že upravíte `TelemetryConfiguration.Active`. Pro ASP.NET Core se provádí konfigurace odlišně. ASP.NET Core SDK je přidané do aplikace a nakonfigurovat pomocí integrované technologie ASP.NET Core [injektáž závislostí](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Téměř všechny změny konfigurace se provádějí v `ConfigureServices()` metodu vaše `Startup.cs` třídy, pokud není uvedeno jinak. Použijte následující části obsahují další informace.

> [!NOTE]
>  Mění se konfigurace tak, že upravíte `TelemetryConfiguration.Active` se nedoporučuje v aplikacích ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Konfigurace pomocí ApplicationInsightsServiceOptions

Je možné změnit několik běžných nastavení předáním `ApplicationInsightsServiceOptions` k `AddApplicationInsightsTelemetry`. Příklad je uveden níže.

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

Přesný seznam konfigurovatelných nastavení v `ApplicationInsightsServiceOptions` najdete [tady](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Vzorkování

Application Insights SDK pro ASP.NET Core podporuje FixedRate a adaptivní vzorkování. Ve výchozím nastavení je povolené adaptivní vzorkování. Postupujte podle našich [pokyny na adaptivním vzorkování](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications), chcete-li další informace o konfiguraci vzorkování pro aplikace ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Přidání TelemetryInitializers

[Inicializátory telemetrie](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) se používají, když chcete definovat globální vlastnosti, které se odesílají s veškerou telemetrii.

Chcete-li přidat nový `TelemetryInitializer`, přidat do kontejneru DependencyInjection, jak je znázorněno níže. `TelemetryInitializer`s DependencyInjection kontejneru přidá neexistoval, použije sada SDK automaticky.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Odebrání TelemetryInitializers

Chcete-li odebrat všechny nebo konkrétní TelemetryInitializers, které jsou k dispozici ve výchozím nastavení, použijte následující ukázkový kód **po** volání `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>Přidání TelemetryProcessors

Procesory vlastní telemetrická data mohou být přidány do `TelemetryConfiguration` pomocí metody rozšíření `AddApplicationInsightsTelemetryProcessor` na `IServiceCollection`. Procesory telemetrická data se používají v [rozšířené filtrování scénáře](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) povolit pro více přímou kontrolu nad co je zahrnuty nebo vyloučeny ze telemetrická data odesílat do služby Application Insights. Pomocí následujícího příkladu.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurace a odebrání výchozí telemetrymodules následující

Application Insights využívá telemetrická data moduly jako způsob, jak [automatické shromažďování užitečné informace](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) o konkrétních úloh bez nutnosti další konfigurace.

Následující moduly automaticky kolekce jsou ve výchozím nastavení povolené a odpovídáte za automaticky shromažďuje telemetrická data. Může být zakázán a nakonfigurovat tak, aby změnit výchozí chování.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Ke konfiguraci žádné výchozí `TelemetryModule`, použijte metodu rozšíření `ConfigureTelemetryModule<T>` na `IServiceCollection` jak je znázorněno v následujícím příkladu.

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

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>Konfigurace Telemetrie kanálu

Výchozí kanál používá `ServerTelemetryChannel`. Může být přepsána následující příklad níže.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>Chci sledovat další telemetrické údaje než automaticky shromažďovat telemetrická data. Jak to udělám?

Získání instance `TelemetryClient` tím, že pomocí konstruktoru vkládání a volat požadované `TrackXXX()` metoda na něj. Není doporučeno vytvořit novou `TelemetryClient` instancí aplikace ASP.NET Core jako instanci typu singleton `TelemetryClient` je už zaregistrovaný do kontejnerů DI, který sdílí `TelemetryConfiguration` se zbytkem telemetrická data. Vytvoření nového `TelemetryClient` instance se doporučuje jenom v případě, že má samostatné konfigurace od zbytku telemetrická data. Následující příklad ukazuje, jak sledovat další telemetrické údaje z kontroleru.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Odkazovat na [vlastní metriky reference k rozhraní API služby Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) popis vlastních dat, vytváření sestav ve službě Application Insights.

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Některé šablony sady Visual Studio využít UseApplicationInsights() rozšiřující metoda na IWebHostBuilder Application Insights. Toto použití je stále platný?

Povolením Application Insights s touto metodou je platný a se používá v sadě Visual Studio v rámci zprovozňování a také rozšíření webové aplikace Azure. Doporučujeme však použít `services.AddApplicationInsightsTelemetry()` poskytuje přetížení k řízení určitou konfiguraci. Jak interně to samé udělejte metody, pokud není žádná vlastní konfigurace použije, volání buď je v pořádku.

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Můžu jsem nasazení Moje aplikace ASP.NET Core do Azure Web Apps. Měli stále povolit rozšíření Application Insights z webových aplikací?

Pokud sada SDK je nainstalovaná v okamžiku sestavení, jak je znázorněno v tomto článku, není nutné k povolení rozšíření Application Insights z portálu služby App Service. I v případě, že rozšíření je nainstalované, bude opět vypnout, když zjistí, že sada SDK je už přidané do aplikace. Povolením Application Insights z rozšíření není instalaci a aktualizaci sady SDK. Povolením Application Insights podle tohoto článku je ale flexibilnější z důvodů níže.
   * Telemetrie Application Insights bude pokračovat v práci:
       * Všechny operační systémy – Windows, Linuxu a macu.
       * Publikovat všechny režimy – samostatná nebo závisí na architektuře.
       * Všechny cílové architektury, včetně úplné rozhraní .NET Framework.
       * Všechny možnosti hostování – webové aplikace Azure, virtuálních počítačů, Linux, kontejnery, AKS, mimo Azure.
   * Telemetrii můžete zobrazit místně, při ladění ze sady Visual Studio.
   * Umožňuje používat další vlastní telemetrická data sledování `TrackXXX()` rozhraní API.
   * Máte plnou kontrolu nad konfigurace.

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>Můžete povolit monitorování pomocí nástrojů, jako je monitorování stavu Application Insights?

Ne. [Monitorování stavu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) a jejím nahrazení nadcházející [monitorování stavu v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) technologie ASP.NET v současné době podporuje pouze 4.x.

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>Mám aplikaci ASP.NET Core 2.0. Není Application Insights automaticky povoleno bez mi teď zrovna nic nedělá?

`Microsoft.AspNetCore.All` 2.0 Microsoft.aspnetcore.all zahrnuté sadu SDK Application Insights (verze 2.1.0), a pokud spustíte aplikaci v ladicím programu sady Visual Studio, Visual Studio Application Insights umožňuje a zobrazuje telemetrická data místně v samotném integrovaném vývojovém prostředí. Telemetrie nebyla proto odeslána do služby Application Insights, výslovně uvedeno Instrumentační klíč. Doporučujeme následující pokyny v tomto článku pro povolení Application Insights, i pro 2.0 aplikace.

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>Spustit aplikaci v Linuxu. Všechny funkce podporuje Linux i?

* Ano. Podpora funkce sady SDK je stejná ve všech platformách, s následujícími výjimkami:

    * Čítače výkonu se zatím nepodporují v Non-Windows.
    * I když `ServerTelemetryChannel` je povolená ve výchozím nastavení, pokud je aplikace spuštěna v systému Linux nebo MacOS, kanál neprovádí automatické vytváření složky místní úložiště dočasně zachovat telemetrická data, pokud existují problémy se sítí. Toto omezení způsobí, že telemetrická data ztratí, pokud jsou dočasné síť nebo server problémy. Alternativní řešení tohoto problému je pro uživatele nakonfigurovat místní složku pro kanál, jak je znázorněno níže.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>Open source sad SDK
[Číst a přidávat do kódu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Video

- Externí krok za krokem videu o [konfigurace Application Insights pomocí .NET Core a Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) úplně od začátku.
- Externí krok za krokem videu o [konfigurace Application Insights pomocí .NET Core a Visual Studio Code](https://youtu.be/ygGt84GDync) úplně od začátku.

## <a name="next-steps"></a>Další postup
* [Prozkoumejte toky uživatelů](../../azure-monitor/app/usage-flows.md) pochopit, jak uživatelé procházejí vaši aplikaci.
* [Konfigurace shromažďování snímků](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) zobrazíte stav zdrojového kódu a proměnné v tuto chvíli dojde k výjimce.
* [Použití rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md) k odesílání vlastních událostí a metrik pro podrobnější přehled výkonu a využití vaší aplikace.
* Použití [testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) ke kontrole neustále z aplikace po celém světě.
