---
title: Přehledy aplikací Azure pro základní ASP.NET aplikace | Dokumenty společnosti Microsoft
description: Sledujte ASP.NET webových aplikacích Core dostupnost, výkon a využití.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: d6a0e507022452f1491e71651ba3bc8db3d1c090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284785"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Přehledy aplikací pro ASP.NET základní aplikace

Tento článek popisuje, jak povolit Application Insights pro [ASP.NET základní](https://docs.microsoft.com/aspnet/core) aplikace. Po dokončení pokynů v tomto článku application insights bude shromažďovat požadavky, závislosti, výjimky, čítače výkonu, prezenční signály a protokoly z aplikace ASP.NET základní.

Příklad, který zde použijeme, je aplikace `netcoreapp2.2` [MVC,](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) která se zaměřuje na aplikaci . Tyto pokyny můžete použít pro všechny aplikace ASP.NET Core.

## <a name="supported-scenarios"></a>Podporované scénáře

[Sada Application Insights SDK pro ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) může sledovat vaše aplikace bez ohledu na to, kde a jak běží. Pokud je vaše aplikace spuštěná a má připojení k síti k Azure, telemetrie se dá shromažďovat. Monitorování Přehledů aplikací je podporováno všude, kde je podporováno jádro .NET Core. Podpora zahrnuje:
* **Operační systém**: Windows, Linux nebo Mac.
* **Hosting metoda**: V procesu nebo mimo proces.
* **Metoda nasazení**: Framework závislé nebo samostatné.
* **Webový server**: IIS (Internet Information Server) nebo Kestrel.
* **Hostitelská platforma:** Funkce Webové aplikace služby Azure App Service, Virtuálního počítače Azure, Dockeru, služby Azure Kubernetes Service (AKS) a tak dále.
* **.NET Core Runtime verze**: 1.XX, 2.XX nebo 3.XX
* **IDE**: Visual Studio, Kód VS nebo příkazový řádek.

> [!NOTE]
> Pokud používáte ASP.NET Core 3.X spolu s Application Insights, použijte verzi [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) nebo vyšší. Toto je jediná verze, která podporuje ASP.NET Core 3.X.

## <a name="prerequisites"></a>Požadavky

- Funkční aplikace ASP.NET Core. Pokud potřebujete vytvořit aplikaci ASP.NET Core, postupujte podle tohoto [kurzu ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Platný klíč instrumentace Application Insights. Tento klíč je nutné odeslat všechny telemetrie application insights. Pokud potřebujete vytvořit nový prostředek Application Insights, abyste získali klíč instrumentace, přečtěte si informace [o vytvoření prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Povolení telemetrie na straně serveru Application Insights (Visual Studio)

1. Otevřete projekt v sadě Visual Studio.

    > [!TIP]
    > Pokud chcete, můžete nastavit správě zdrojového kódu pro váš projekt, takže můžete sledovat všechny změny, které aplikace Insights provede. Chcete-li povolit správou **zdrojového kódu,** > vyberte**možnost Přidat do správy zdrojového kódu**.

2. Vyberte **Project** > **Add Application Insights Telemetry**.

3. Vyberte **Možnost Začínáme**. Text tohoto výběru se může lišit v závislosti na verzi sady Visual Studio. Některé starší verze místo toho používají tlačítko **Start Free.**

4. Vyberte své předplatné. Potom vyberte **položku Registr** > **zdrojů**.

5. Po přidání Application Insights do projektu zkontrolujte, zda používáte nejnovější stabilní verzi sady SDK. Přejděte na **projekt** > **Správa nugetových balíčků** > **Microsoft.ApplicationInsights.AspNetCore**. V případě potřeby zvolte **Aktualizovat**.

     ![Snímek obrazovky s výběrem balíčku Application Insights pro aktualizaci](./media/asp-net-core/update-nuget-package.png)

6. Pokud jste postupovali podle volitelného tipu a přidali projekt do správy zdrojového kódu, přejděte na **zobrazení** > **změn****průzkumníka** > týmu . Pak vyberte každý soubor zobrazíte rozdílové zobrazení změn provedených telemetrií Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Povolení telemetrie na straně serveru Application Insights (bez sady Visual Studio)

1. Nainstalujte [balíček Application Insights SDK NuGet pro ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Doporučujeme vždy používat nejnovější stabilní verzi. Úplné poznámky k verzi sady SDK najdete v [open source úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Následující ukázka kódu ukazuje změny, které mají `.csproj` být přidány do souboru projektu.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Přidejte `services.AddApplicationInsightsTelemetry();` `ConfigureServices()` do metody `Startup` ve třídě, jako v tomto příkladu:

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

    I když můžete zadat instrumentace `AddApplicationInsightsTelemetry`klíč jako argument , doporučujeme zadat klíč instrumentace v konfiguraci. Následující ukázka kódu ukazuje, jak zadat `appsettings.json`klíč instrumentace v aplikaci . Ujistěte `appsettings.json` se, že je zkopírován do kořenové složky aplikace během publikování.

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

    Případně zadejte klíč instrumentace v jedné z následujících proměnných prostředí:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Například:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Obvykle `APPINSIGHTS_INSTRUMENTATIONKEY` určuje klíč instrumentace pro aplikace nasazené do Azure Web Apps.

    > [!NOTE]
    > Instrumentační klíč zadaný v kódu `APPINSIGHTS_INSTRUMENTATIONKEY`vítězí nad proměnnou prostředí , která vyhrává nad jinými možnostmi.

## <a name="run-your-application"></a>Spusťte aplikaci

Spusťte aplikaci a podejte na něj požadavky. Telemetrie by teď měla tok do Application Insights. Sada Application Insights SDK automaticky shromažďuje příchozí webové požadavky do vaší aplikace spolu s následující telemetrií.

### <a name="live-metrics"></a>Live Metrics

[Živé metriky](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) lze rychle ověřit, zda je monitorování přehledů aplikací správně nakonfigurováno. Zatímco to může trvat několik minut, než se telemetrie začne zobrazovat na portálu a v analýzách, živé metriky by zobrazovaly využití procesoru spuštěného procesu téměř v reálném čase. Může také zobrazit další telemetrie, jako jsou požadavky, závislosti, trasování atd.

### <a name="ilogger-logs"></a>Protokoly ILogger

Protokoly vyzařované `ILogger` prostřednictvím `Warning` závažnosti nebo vyšší jsou automaticky zachyceny. Postupujte [podle ILogger docs](ilogger.md#control-logging-level) přizpůsobit, které úrovně protokolu jsou zachyceny Application Insights.

### <a name="dependencies"></a>Závislosti

Kolekce závislostí je ve výchozím nastavení povolena. [Tento](asp-net-dependencies.md#automatically-tracked-dependencies) článek vysvětluje závislosti, které jsou automaticky shromažďovány a také obsahují kroky k ručnímu sledování.

### <a name="performance-counters"></a>Čítače výkonu

Podpora [čítačů výkonu](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) v ASP.NET Core je omezená:

* Sada SDK verze 2.4.1 a novější shromažďovat čítače výkonu, pokud je aplikace spuštěna v Azure Web Apps (Windows).
* Sada SDK verze 2.7.1 a novější shromažďovat čítače `NETSTANDARD2.0` výkonu, pokud je aplikace spuštěna v systému Windows a cíle nebo novější.
* Pro aplikace zaměřené na rozhraní .NET Framework podporují všechny verze sady SDK čítače výkonu.
* Sada SDK Verze 2.8.0 a novější podporují čítač cpu/paměti v Linuxu. V Linuxu není podporován žádný jiný čítač. Doporučený způsob, jak získat čítače systému v Linuxu (a jiných prostředích mimo Windows) je pomocí [EventCounters](#eventcounter)

### <a name="eventcounter"></a>Čítač událostí

`EventCounterCollectionModule`je ve výchozím nastavení povolena a bude shromažďovat výchozí sadu čítačů z aplikací .NET Core 3.X. Kurz [EventCounter](eventcounters.md) uvádí výchozí sadu čítačů shromážděných. Má také pokyny pro přizpůsobení seznamu.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Povolení telemetrie na straně klienta pro webové aplikace

Předchozí kroky jsou dostatečné k tomu, abyste začali shromažďovat telemetrii na straně serveru. Pokud vaše aplikace má součásti na straně klienta, postupujte podle následujících kroků a začněte shromažďovat [telemetrii využití](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. V `_ViewImports.cshtml`, přidejte injekci:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. V `_Layout.cshtml`, `HtmlHelper` vložte na `<head>` konci oddílu, ale před jakýkoli jiný skript. Pokud chcete ze stránky nahlásit jakoukoli vlastní telemetrii JavaScriptu, vložte ji po tomto úryvku:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Alternativně k `FullScript` použití `ScriptBody` je k dispozici počínaje SDK v2.14. Tuto možnost použijte, pokud `<script>` potřebujete určit značku pro nastavení zásad zabezpečení obsahu:

    ```cshtml
        <script> // apply custom changes to this script tag.
            @Html.Raw(JavaScriptSnippet.ScriptBody)
        </script>
    ```

Názvy `.cshtml` souborů, na které se odkazuje dříve, pocházejí z výchozí šablony aplikace MVC. Nakonec pokud chcete správně povolit monitorování na straně klienta pro vaši aplikaci, `<head>` fragment jazyka JavaScript se musí zobrazit v části každé stránky aplikace, kterou chcete sledovat. Tohoto cíle pro tuto šablonu aplikace můžete dosáhnout přidáním `_Layout.cshtml`fragmentu javascriptu do aplikace . 

Pokud projekt neobsahuje `_Layout.cshtml`, můžete stále přidat monitorování na straně [klienta](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). To lze provést přidáním fragmentu JavaScriptu do ekvivalentního `<head>` souboru, který řídí všechny stránky v aplikaci. Nebo můžete fragment přidat na více stránek, ale toto řešení je obtížné udržovat a obecně ho nedoporučujeme.

## <a name="configure-the-application-insights-sdk"></a>Konfigurace sady SDK SDK přehledů aplikací

Chcete-li změnit výchozí konfiguraci, můžete přizpůsobit sadu Application Insights SDK pro ASP.NET Core. Uživatelé sady Application Insights ASP.NET Sady SDK `ApplicationInsights.config` mohou být `TelemetryConfiguration.Active`obeznámeni se změnou konfigurace pomocí nebo úpravou . Pro ASP.NET Core změníte konfiguraci odlišně. Přidejte do aplikace ASP.NET základní sady SDK a nakonfigurujte ji pomocí integrovaného [vkládání závislostí](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)ASP.NET Core . Proveďte téměř všechny `ConfigureServices()` změny konfigurace `Startup.cs` v metodě vaší třídy, pokud nejste nasměrováni jinak. Další informace naleznete v následujících částech.

> [!NOTE]
> V ASP.NET základních aplikací není `TelemetryConfiguration.Active` změna konfigurace úpravou podporována.

### <a name="using-applicationinsightsserviceoptions"></a>Použití applicationinsightsmožnosti

Můžete upravit několik běžných `ApplicationInsightsServiceOptions` nastavení `AddApplicationInsightsTelemetry`předáním do , jako v tomto příkladu:

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

Úplný seznam nastavení v`ApplicationInsightsServiceOptions`

|Nastavení | Popis | Výchozí
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Povolit/zakázat`PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Povolit/zakázat`RequestTrackingTelemetryModule` | true
|Modul EnableEventCounterCollectionModule   | Povolit/zakázat`EventCounterCollectionModule` | true
|Modul EnableDependencyTrackingTelemetryModule   | Povolit/zakázat`DependencyTrackingTelemetryModule` | true
|Modul EnableAppServicesHeartbeatTelemetryModule  |  Povolit/zakázat`AppServicesHeartbeatTelemetryModule` | true
|Povolit modul AzureInstanceMetadataTelemetryModule   |  Povolit/zakázat`AzureInstanceMetadataTelemetryModule` | true
|PovolitQuickPulseMetricStream | Povolit nebo zakázat funkci LiveMetrics | true
|Povolit adaptivní vzorkování | Povolit nebo zakázat adaptivní vzorkování | true
|EnableHeartbeat | Funkce Povolit nebo zakázat prezenční signály, která pravidelně (15min default) odesílá vlastní metriku s názvem "HeartBeatState" s informacemi o běhu, jako je verze .NET, informace o prostředí Azure, pokud je to možné, atd. | true
|AddAutoCollectedMetricExtractor | Povolit nebo zakázat AutoCollectedMetrics extraktor, což je TelemetryProcessor, který odesílá předem agregované metriky o požadavky/závislosti před odběr vzorků probíhá. | true
|RequestCollectionOptions.TrackExceptions | Povolit nebo zakázat vykazování neošetřeného sledování výjimek modulem Kolekce požadavku. | false v NETSTANDARD2.0 (protože výjimky jsou sledovány s ApplicationInsightsLoggerProvider), true jinak.

Podívejte se na [konfigurovatelné nastavení v `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) nejaktuálnějším seznamu.

### <a name="sampling"></a>Vzorkování

Sada Application Insights SDK pro ASP.NET Core podporuje při vzorkování s pevnou rychlostí i adaptivní vzorkování. Adaptivní vzorkování je ve výchozím nastavení povoleno. 

Další informace naleznete v [tématu Konfigurace adaptivního vzorkování pro aplikace ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Přidání telemetrických inicializátorů

[Telemetrické inicializátory](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) použijte, pokud chcete definovat globální vlastnosti, které jsou odesílány se všemi telemetrii.

Přidejte `TelemetryInitializer` všechny `DependencyInjection` nové do kontejneru, jak je znázorněno v následujícím kódu. Sada SDK automaticky vyzvedne všechny, `TelemetryInitializer` `DependencyInjection` které jsou přidány do kontejneru.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Odebrání telemetrických inicializátorů

Telemetrické inicializátory jsou k dispozici ve výchozím nastavení. Chcete-li odebrat všechny nebo konkrétní telemetrie inicializátory, použijte následující ukázkový kód *po* volání `AddApplicationInsightsTelemetry()`.

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

### <a name="adding-telemetry-processors"></a>Přidání telemetrických procesorů

Vlastní telemetrické procesory můžete `TelemetryConfiguration` přidat pomocí `AddApplicationInsightsTelemetryProcessor` metody `IServiceCollection`rozšíření na . Telemetrické procesory se používají v [pokročilých scénářích filtrování](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer). Použijte následující příklad.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurace nebo odebrání výchozích modulů telemetrie

Application Insights používá telemetrické moduly k automatickému shromažďování užitečných telemetrických úloh o konkrétních úlohách bez nutnosti ručního sledování uživatelem.

Následující moduly automatického sběru jsou ve výchozím nastavení povoleny. Tyto moduly jsou zodpovědné za automatické shromažďování telemetrie. Můžete je zakázat nebo nakonfigurovat tak, aby měnily své výchozí chování.

* `RequestTrackingTelemetryModule`- Shromažďuje RequestTelemetry z příchozích webových požadavků.
* `DependencyTrackingTelemetryModule`- Shromažďuje závislostTelemetrie z odchozích http volání a volání SQL.
* `PerformanceCollectorModule`- Shromažďuje Windows PerformanceCounters.
* `QuickPulseTelemetryModule`- Shromažďuje telemetrii pro zobrazení na portálu Live Metrics.
* `AppServicesHeartbeatTelemetryModule`- Shromažďuje tlukot srdce (které se odesílají jako vlastní metriky), o prostředí Služby Azure App Service, kde je aplikace hostovaná.
* `AzureInstanceMetadataTelemetryModule`- Shromažďuje tlukot srdce (které se odesílají jako vlastní metriky), o prostředí virtuálních počítačích Azure, kde je aplikace hostovaná.
* `EventCounterCollectionModule`- Shromažďuje [EventCounters.](eventcounters.md) Tento modul je nová funkce a je k dispozici v SDK verze 2.8.0 a vyšší.

Chcete-li `TelemetryModule`nakonfigurovat výchozí `ConfigureTelemetryModule<T>` nastavení `IServiceCollection`, použijte metodu rozšíření na , jak je znázorněno v následujícím příkladu.

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
                module.Counters.Clear();
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

Počínaje verzí 2.12.2 [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) obsahuje snadnou možnost zakázat některý z výchozích modulů.

### <a name="configuring-a-telemetry-channel"></a>Konfigurace telemetrického kanálu

Výchozí kanál `ServerTelemetryChannel`je . Můžete přepsat, jak ukazuje následující příklad.

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

### <a name="disable-telemetry-dynamically"></a>Dynamicky zakázat telemetrii

Pokud chcete zakázat telemetrie podmíněně a `TelemetryConfiguration` dynamicky, můžete vyřešit instanci s `DisableTelemetry` ASP.NET core vkládání závislostí kontejnerkdekoli v kódu a nastavit příznak na něm.

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

Výše uvedené nebrání žádné moduly automatické kolekce ze shromažďování telemetrie. Pouze odesílání telemetrie do Application Insights získá zakázáno s výše uvedený přístup. Pokud konkrétní modul automatické kolekce není žádoucí, je nejlepší [odebrat modul telemetrie](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-application-insights-support-aspnet-core-3x"></a>Podporuje Application Insights ASP.NET Core 3.X?

Ano. Aktualizace [sady Application Insights SDK pro ASP.NET core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.8.0 nebo vyšší. Starší verze sady SDK nepodporují ASP.NET Core 3.X.

Také pokud používáte visual studio založené pokyny [z tohoto tady](#enable-application-insights-server-side-telemetry-visual-studio), aktualizovat na nejnovější verzi Visual Studio 2019 (16.3.0) na palubní. Předchozí verze Sady Visual Studio nepodporují automatické registrace pro aplikace ASP.NET Core 3.X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Jak lze sledovat telemetrii, která se neshromažďuje automaticky?

Získejte instanci `TelemetryClient` pomocí vkládání konstruktoru `TrackXXX()` a volání požadované metody na něm. Nedoporučujeme vytvářet nové `TelemetryClient` instance v aplikaci ASP.NET Core. Singleton instance `TelemetryClient` je již registrována v `DependencyInjection` `TelemetryConfiguration` kontejneru, který sdílí se zbytkem telemetrie. Vytvoření nové `TelemetryClient` instance se doporučuje pouze v případě, že potřebuje konfiguraci, která je oddělená od zbytku telemetrie.

Následující příklad ukazuje, jak sledovat další telemetrická data z řadiče.

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

Další informace o vlastním vytváření sestav dat v Application Insights najdete v [tématu Application Insights vlastní metriky ODKAZ NA ROZHRANÍ API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Některé šablony Sady Visual Studio používaly metodu rozšíření UseApplicationInsights() na iWebHostBuilder k povolení application insights. Je toto použití stále platné?

Zatímco metoda `UseApplicationInsights()` rozšíření je stále podporována, je označena jako zastaralá v application insights sdk verze 2.8.0 a dále. Bude odebrána v další hlavní verzi sady SDK. Doporučený způsob, jak povolit telemetrická data Application Insights, je pomocí, `AddApplicationInsightsTelemetry()` protože poskytuje přetížení pro řízení některé konfigurace. Také v ASP.NET aplikací Core `services.AddApplicationInsightsTelemetry()` 3.X, je jediný způsob, jak povolit přehledy aplikací.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Nasazuji svou aplikaci ASP.NET Core do webových aplikací. Mám stále povolit rozšíření Application Insights z webových aplikací?

Pokud je sada SDK nainstalována v době sestavení, jak je znázorněno v tomto článku, nemusíte povolit [rozšíření Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) z portálu služby App Service. I v případě, že rozšíření je nainstalován, bude ustoupit, když zjistí, že sada SDK je již přidán do aplikace. Pokud povolíte Application Insights z rozšíření, není nutné instalovat a aktualizovat sdk. Ale pokud povolíte Application Insights podle následujících pokynů v tomto článku, máte větší flexibilitu, protože:

   * Telemetrie Application Insights bude nadále fungovat v:
       * Všechny operační systémy, včetně Windows, Linux a Mac.
       * Všechny režimy publikování, včetně samostatných nebo framework závislé.
       * Všechny cílové architektury, včetně úplného rozhraní .NET Framework.
       * Všechny možnosti hostování, včetně webových aplikací, virtuálních stránek, Linuxu, kontejnerů, služby Azure Kubernetes service a hostingu mimo Azure.
       * Všechny verze .NET Core včetně verzí ve verzi preview.
   * Telemetrická data můžete zobrazit místně při ladění z Visual Studia.
   * Můžete sledovat další vlastní telemetrie pomocí `TrackXXX()` rozhraní API.
   * Máte plnou kontrolu nad konfigurací.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Můžu povolit monitorování přehledů aplikací pomocí nástrojů, jako je Sledování stavu?

Ne. [Sledování stavu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) a [Sledování stavu v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) aktuálně podporují pouze ASP.NET 4.x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Je Application Insights automaticky povolena pro mou aplikaci ASP.NET Core 2.0?

Metabalíček `Microsoft.AspNetCore.All` 2.0 zahrnoval sadu Application Insights SDK (verze 2.1.0). Pokud spustíte aplikaci v ladicím programu Visual Studio, Visual Studio povolí Application Insights a zobrazuje telemetrii místně v samotném ide. Telemetrie nebyla odeslána do služby Application Insights, pokud nebyl zadán klíč instrumentace. Doporučujeme postupujte podle pokynů v tomto článku povolit Application Insights, i pro aplikace 2.0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Pokud spustím svou aplikaci v Linuxu, jsou podporovány všechny funkce?

Ano. Podpora funkcí sady SDK je stejná na všech platformách, s následujícími výjimkami:

* Sada SDK shromažďuje [čítače událostí](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) v systému Linux, protože [čítače výkonu](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) jsou podporovány pouze v systému Windows. Většina metrik je stejná.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Je tato sada SDK podporována pro nové aplikace šablony pracovní chodu .NET Core 3.X?

Tato sada SDK vyžaduje `HttpContext`, a proto nefunguje v žádné aplikace bez http, včetně .NET Core 3.X worker service aplikace. V [tomto](worker-service.md) dokumentu můžete povolit přehledy aplikací v těchto aplikacích pomocí nově vydané sady Microsoft.ApplicationInsights.WorkerService SDK.

## <a name="open-source-sdk"></a>Sada SDK s otevřeným zdrojovým kódem

[Přečtěte si a přispějte ke kódu](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="video"></a>Video

- Podívejte se na toto externí video krok za krokem a [nakonfigurujte přehledy aplikací pomocí rozhraní .NET Core a Visual Studia](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) od začátku.
- Podívejte se na toto externí video krok za krokem a [nakonfigurujte přehledy aplikací pomocí kódu .NET Core a Visual Studio code](https://youtu.be/ygGt84GDync) od začátku.

## <a name="next-steps"></a>Další kroky

* [Prozkoumejte toky uživatelů,](../../azure-monitor/app/usage-flows.md) abyste pochopili, jak uživatelé procházejí vaší aplikací.
* [Nakonfigurujte kolekci snímků,](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) abyste viděli stav zdrojového kódu a proměnných v okamžiku, kdy je vyvolána výjimka.
* [Pomocí rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md) můžete odesílat vlastní události a metriky pro podrobné zobrazení výkonu a využití aplikace.
* Pomocí [testů dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) můžete aplikaci neustále kontrolovat z celého světa.
* [Vkládání závislostí v ASP.NET jádru](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
