---
title: Azure Application Insights pro aplikace ASP.NET Core | Dokumentace Microsoftu
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
ms.openlocfilehash: 5ea7ec41ccc721e8eafda56aa7463505ba089845
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827813"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights pro aplikace ASP.NET Core

Tento článek popisuje, jak povolit Application Insights pro [ASP.NET Core](https://docs.microsoft.com/aspnet/core) aplikace. Až dokončíte pokyny v tomto článku, Application Insights bude shromažďovat požadavky, závislosti, výjimky, čítače výkonu, prezenční signály a protokoly z aplikace ASP.NET Core. 

Příklad zde použijeme je [aplikace MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) , který cílí `netcoreapp2.2`. Tyto pokyny můžete provést u všech aplikací ASP.NET Core.

## <a name="supported-scenarios"></a>Podporované scénáře

[Application Insights SDK pro ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) můžete sledovat vaše aplikace bez ohledu na to, kdy nebo jak použijí. Pokud vaše aplikace běží a má síťové připojení k Azure, se můžou shromažďovat telemetrii. Sledování Application Insights podporuje všude, kde se podporuje .NET Core. Podpora zahrnuje:
* **Operační systém**: Windows, Linux nebo Mac.
* **Hostování metoda**: V procesu nebo mimo proces. 
* **Metoda nasazení**: Architektura závislé nebo samostatné.
* **Webový server**: Služba IIS (Internet Information Server) nebo Kestrel. 
* **Hostující platforma**: Funkce Web Apps služby Azure App Service, virtuálního počítače Azure, Docker, Azure Kubernetes Service (AKS) a tak dále.
* **INTEGROVANÉ VÝVOJOVÉ PROSTŘEDÍ**: Visual Studio, VS Code nebo příkazového řádku.

## <a name="prerequisites"></a>Požadavky

- Funkční aplikace ASP.NET Core. Pokud potřebujete k vytvoření aplikace ASP.NET Core, proveďte to [kurz ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Platný klíč instrumentace Application Insights. Tento klíč je vyžadován pro odeslání žádnou telemetrii do Application Insights. Pokud je potřeba vytvořit nový prostředek Application Insights, chcete-li získat Instrumentační klíče najdete v tématu [vytvořte prostředek Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Povolit telemetrii Application Insights na straně serveru (Visual Studio)

1. Otevřete svůj projekt v sadě Visual Studio.

    > [!TIP]
    > Pokud chcete, můžete nastavíte správy zdrojového kódu pro váš projekt, můžete sledovat všechny změny, které díky Application Insights. Chcete-li povolit správu zdrojového kódu, vyberte **souboru** > **přidat do správy zdrojových kódů**.

2. Vyberte **projektu** > **přidat Telemetrii Application Insights**.

3. Vyberte **Začínáme**. Tento výběr textu se může lišit v závislosti na vaší verzi sady Visual Studio. Použít některé starší verze **začít zdarma** tlačítko místo.

4. Vyberte své předplatné. Potom vyberte **prostředků** > **zaregistrovat**.

5. Po přidání Application Insights do projektu, zaškrtněte, pokud chcete potvrdit, že používáte nejnovější stabilní verzi sady SDK. Přejděte na **projektu** > **spravovat balíčky NuGet** > **Microsoft.ApplicationInsights.AspNetCore**. Pokud potřebujete, zvolte **aktualizace**.

     ![Snímek obrazovky znázorňující, kde vyberte balíček Application Insights pro aktualizaci](./media/asp-net-core/update-nuget-package.png)

6. Pokud a volitelný popis tlačítka a přidá projekt do správy zdrojového kódu, přejděte na **zobrazení** > **Team Exploreru** > **změny**. Vyberte každý soubor zobrazení rozdílové změny provedené v telemetrii Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Povolit telemetrii Application Insights na straně serveru (bez sady Visual Studio)

1. Nainstalujte [balíček NuGet sady SDK pro Application Insights pro ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Doporučujeme vždy používat nejnovější stabilní verzi. Najdete kompletní poznámky k verzi sady SDK na [úložiště GitHub open source](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Následující příklad kódu ukazuje změny se přidají do vašeho projektu `.csproj` souboru.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Přidat `services.AddApplicationInsightsTelemetry();` k `ConfigureServices()` metoda ve vaší `Startup` třídy, jako v následujícím příkladu:

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

    I když Instrumentační klíč můžete zadat jako argument `AddApplicationInsightsTelemetry`, doporučujeme zadat Instrumentační klíč v konfiguraci. Následující příklad kódu ukazuje, jak zadat Instrumentační klíč v `appsettings.json`. Ujistěte se, že `appsettings.json` zkopírována do kořenové složky aplikace během publikování.

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

    Můžete také zadejte Instrumentační klíč v některém z následující proměnné prostředí:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Příklad:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Obvykle `APPINSIGHTS_INSTRUMENTATIONKEY` určuje Instrumentační klíč pro aplikace nasazené do služby Web Apps.

    > [!NOTE]
    > Instrumentačním klíčem zadaný ve službě wins kódu přes proměnnou prostředí `APPINSIGHTS_INSTRUMENTATIONKEY`, které služby wins za další možnosti.

## <a name="run-your-application"></a>Spusťte aplikaci

Spusťte aplikaci a požadavky na ni. Telemetrická data by nyní směrovat do Application Insights. Application Insights SDK automaticky shromažďuje následující telemetrická data.

|Požadavky a závislosti |Podrobnosti|
|---------------|-------|
|Požadavky | Příchozích webových požadavků do vaší aplikace. |
|Protokol HTTP nebo HTTPS | Volání s `HttpClient`. |
|SQL | Volání s `SqlClient`. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Volání s klientem služby Azure Storage. |
|[EventHubs klientskou sadou SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Verze 1.1.0 nebo novější. |
|[Klientská sada SDK pro služby Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Verze 3.0.0 nebo novější. |
|Azure Cosmos DB | Automaticky sledována pouze v případě, že se používá HTTP/HTTPS. Application Insights nezachytí režim TCP. |

### <a name="performance-counters"></a>Čítače výkonu

Podpora pro [čítače výkonu](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) v ASP.NET Core je omezený:

   * Verze sady SDK 2.4.1 a později shromažďování výkonu čítače, pokud je aplikace spuštěná ve službě Web Apps (Windows).
   * 2\.7.0-beta3 verze sady SDK a vyšší výkon shromáždit čítače, pokud aplikace běží ve Windows a cíle `NETSTANDARD2.0` nebo novější.
   * Pro aplikace cílené na rozhraní .NET Framework všechny verze sady SDK podporují čítače výkonu.
 
Tento článek bude aktualizován, když se přidá podpora čítače výkonu v systému Linux.

### <a name="ilogger-logs"></a>ILogger protokoly

[Protokoly ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) závažnosti `Warning` nebo větší automatickým zachycením 2.7.0-beta3 verze sady SDK a novější.

### <a name="live-metrics"></a>Živé metriky

Může trvat několik minut, než začne telemetrie povolí, na portálu. Chcete-li rychle Ujistěte se, že všechno funguje, je nejvhodnější použít [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) když nastavíte žádosti do spuštěné aplikace.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Povolit telemetrii na straně klienta pro webové aplikace

V předchozích krocích jsou dost informací k vám pomohou v začátcích shromažďování telemetrie na straně serveru. Pokud má vaše aplikace komponenty na straně klienta, postupujte podle dalších kroků při spuštění shromažďování [telemetrii jejich využívání](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. V `_ViewImports.cshtml`, přidejte vkládání:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. V `_Layout.cshtml`, Vložit `HtmlHelper` na konci `<head>` oddílu, ale před veškerým skriptem. Pokud chcete nahlásit žádnou vlastní telemetrii JavaScript na stránce, vložit ho po tento fragment kódu:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml` z výchozí šablony MVC aplikace jsou názvy souborů uvedené dříve. Nakonec, pokud chcete správně povolit monitorování na straně klienta pro vaši aplikaci, javascriptový fragment kódu musí být uvedena v `<head>` části všech stránek aplikace, kterou chcete monitorovat. Tento cíl pro tuto šablonu aplikace můžete provést přidáním fragment kódu jazyka JavaScript pro `_Layout.cshtml`. 

Pokud projekt neobsahuje `_Layout.cshtml`, můžete přesto přidat [monitorování na straně klienta](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Můžete to provést tak, že přidáte fragment kódu jazyka JavaScript do souboru ekvivalentní ovládací prvky `<head>` všech stránek ve vaší aplikaci. Nebo můžete přidat fragment kódu pro více stránek, ale toto řešení je obtížné udržovat a obecně nedoporučujeme.

## <a name="configure-the-application-insights-sdk"></a>Nakonfigurovat Application Insights SDK

Můžete přizpůsobit sadu Application Insights SDK pro ASP.NET Core, chcete-li změnit výchozí konfiguraci. Uživatele sadu SDK Application Insights technologie ASP.NET pravděpodobně znáte změna konfigurace s použitím `ApplicationInsights.config` nebo úpravou `TelemetryConfiguration.Active`. Můžete změnit konfiguraci pro ASP.NET Core. Přidat do aplikace ASP.NET Core SDK a konfigurovat pomocí ASP.NET Core integrované [injektáž závislostí](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Provést téměř všechny změny konfigurace v `ConfigureServices()` metodu vaše `Startup.cs` třídy, pokud jste přesměrováni jinak. Následující části poskytují další informace.

> [!NOTE]
> V aplikacích ASP.NET Core, změna konfigurace tak, že upravíte `TelemetryConfiguration.Active` se nepodporuje.

### <a name="using-applicationinsightsserviceoptions"></a>Using ApplicationInsightsServiceOptions

Můžete upravit několik běžných nastavení předáním `ApplicationInsightsServiceOptions` k `AddApplicationInsightsTelemetry`, jako v tomto příkladu:

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

Další informace najdete v tématu [konfigurovatelné nastavení v `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Vzorkování

Application Insights SDK pro ASP.NET Core podporuje-rate a adaptivní vzorkování. Ve výchozím nastavení je povolené adaptivní vzorkování. 

Další informace najdete v tématu [konfigurace adaptivního vzorkování pro aplikace ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Přidání TelemetryInitializers

Použití [telemetrie inicializátory](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) Pokud chcete definovat globální vlastnosti, které se odesílají s veškerou telemetrii.

Přidat žádný nový `TelemetryInitializer` k `DependencyInjection` kontejneru, jak je znázorněno v následujícím kódu. Sada SDK automaticky vybere všechny `TelemetryInitializer` , který je přidán do `DependencyInjection` kontejneru.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Odebrání TelemetryInitializers

Inicializátory telemetrická data jsou k dispozici ve výchozím nastavení. Chcete-li odebrat všechny nebo inicializátory konkrétní telemetrická data, použijte následující ukázkový kód *po* zavoláte `AddApplicationInsightsTelemetry()`.

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

### <a name="adding-telemetry-processors"></a>Přidání telemetrické procesorů

Můžete přidat vlastní telemetrii procesorů na `TelemetryConfiguration` pomocí metody rozšíření `AddApplicationInsightsTelemetryProcessor` na `IServiceCollection`. Používají procesory telemetrie v [rozšířené filtrování scénáře](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) povolit pro více přímou kontrolu nad co má zahrnuty nebo vyloučeny ze telemetrická data odesílat do služby Application Insights. Pomocí následujícího příkladu.

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

Application Insights využívá telemetrická data z modulů do [automaticky shromažďovat užitečné informace](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) o konkrétních úloh bez nutnosti další konfigurace.

Ve výchozím nastavení jsou povoleny následující moduly automatické shromažďování. Tyto moduly jsou zodpovědné za automaticky shromažďuje telemetrická data. Můžete zakázat nebo změnit jejich chování je nakonfigurovat.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Ke konfiguraci žádné výchozí `TelemetryModule`, použijte metodu rozšíření `ConfigureTelemetryModule<T>` na `IServiceCollection`, jak je znázorněno v následujícím příkladu.

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

### <a name="configuring-a-telemetry-channel"></a>Konfigurace telemetrie kanálu

Výchozí kanál `ServerTelemetryChannel`. Je možné přepsat jako v následujícím příkladu.

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

### <a name="disable-telemetry-dynamically"></a>Zakázat telemetrii dynamicky

Pokud chcete zakázat telemetrii podmíněně a dynamicky, lze vyřešit `TelemetryConfiguration` instanci s ASP.NET Core kontejneru pro vkládání závislosti kdekoli v kódu a nastavte `DisableTelemetry` příznak na něj.

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

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Jak mohu sledovat telemetrická data, která se automaticky shromažďují?

Získat instanci `TelemetryClient` tím, že pomocí konstruktoru vkládání a volat požadované `TrackXXX()` metoda na něj. Nedoporučujeme ale, vytváří se nová `TelemetryClient` instance v aplikaci ASP.NET Core. Instanci typu singleton `TelemetryClient` už je zaregistrovaný v `DependencyInjection` kontejneru, který sdílí `TelemetryConfiguration` se zbytkem telemetrická data. Vytvoření nového `TelemetryClient` instance se doporučuje jenom Pokud je nutné konfiguraci, která je nezávislá na zbývající telemetrická data. 

Následující příklad ukazuje, jak sledovat další telemetrické údaje z kontroleru.

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

Další informace o vlastních dat, vytváření sestav ve službě Application Insights najdete v tématu [vlastní metriky reference k rozhraní API služby Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Některé šablony sady Visual Studio využít metodu rozšíření UseApplicationInsights() na IWebHostBuilder Application Insights. Toto použití je stále platný?

Ano, povolení Application Insights s touto metodou je platný. Tento postup se používá v připojení sady Visual Studio a rozšíření webové aplikace. Doporučujeme však používat `services.AddApplicationInsightsTelemetry()` protože poskytuje přetížení k řízení určitou konfiguraci. Obě metody stejnou věc udělat interně, takže pokud není nutné použít vlastní konfiguraci, můžete volat některé z metod.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Můžu jsem nasazení Moje aplikace ASP.NET Core do služby Web Apps. Měli stále povolit rozšíření Application Insights z webových aplikací?

Pokud sada SDK je nainstalovaná v okamžiku sestavení, jak je znázorněno v tomto článku, není nutné povolit rozšíření Application Insights z portálu služby App Service. I v případě, že rozšíření je nainstalované, bude opět vypnout, když zjistí, že sada SDK je už přidané do aplikace. Pokud povolíte Application Insights z rozšíření, nemusíte instalovat a aktualizovat sadu SDK. Ale pokud povolíte Application Insights podle pokynů v tomto článku, máte větší flexibilitu, protože:

   * Telemetrie Application Insights bude pokračovat v práci:
       * Všechny operační systémy, včetně Windows, Linuxu a macu.
       * Publikovat všechny režimy, včetně samostatná nebo framework závislé.
       * Všechny cílové architektury, včetně úplné rozhraní .NET Framework.
       * Všechny možnosti hostování včetně webových aplikací, virtuálních počítačů, Linux, kontejnery, služby Azure Kubernetes Service a hostování mimo Azure.
   * Telemetrii můžete zobrazit místně při ladění ze sady Visual Studio.
   * Další vlastní telemetrii můžete sledovat pomocí `TrackXXX()` rozhraní API.
   * Máte plnou kontrolu nad konfigurace.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Můžete povolit monitorování pomocí Application Insights s využitím nástrojů, jako je monitorování stavu?

Ne. [Monitorování stavu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) a [monitorování stavu v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) aktuálně podporují ASP.NET 4.x pouze.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights automaticky zapnutá pro Moje aplikace ASP.NET Core 2.0?

`Microsoft.AspNetCore.All` 2.0 Microsoft.aspnetcore.all zahrnuté sadu SDK Application Insights (verze 2.1.0). Pokud spustíte aplikaci v ladicím programu sady Visual Studio, Visual Studio Application Insights umožňuje a zobrazuje telemetrická data místně v samotném integrovaném vývojovém prostředí. Telemetrie nebyla proto odeslána do služby Application Insights byla určena Instrumentační klíč. Doporučujeme následující pokyny v tomto článku pro povolení Application Insights, i pro 2.0 aplikace.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Je-li spustit aplikaci v Linuxu, podporují všechny funkce?

Ano. Podpora funkce sady SDK je stejně ve všech platformách, s následujícími výjimkami:

* Čítače výkonu jsou podporovány pouze ve Windows.
* I když `ServerTelemetryChannel` je povolená ve výchozím nastavení, pokud je aplikace spuštěna v systému Linux nebo MacOS, kanál nevytváří automaticky místní úložiště složky dočasně zachovat telemetrická data, pokud existují problémy se sítí. Vzhledem k tomuto omezení dojde ke ztrátě telemetrická data, když dojde k problémům dočasná síť nebo server. Chcete-li tento problém obejít, nakonfigurujte místní složku pro kanál:

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

## <a name="open-source-sdk"></a>Open source sad SDK

[Číst a přidávat do kódu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Video

- Podívejte se na video externí podrobné [nakonfigurovat Application Insights s .NET Core a Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) úplně od začátku.
- Podívejte se na video externí podrobné [nakonfigurovat Application Insights s .NET Core a Visual Studio Code](https://youtu.be/ygGt84GDync) úplně od začátku.

## <a name="next-steps"></a>Další postup

* [Prozkoumejte toky uživatelů](../../azure-monitor/app/usage-flows.md) pochopit, jak uživatelé procházejí vaši aplikaci.
* [Konfigurace shromažďování snímků](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) zobrazíte stav zdrojového kódu a proměnné v tuto chvíli dojde k výjimce.
* [Použití rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md) k odesílání vlastních událostí a metrik pro podrobné zobrazení výkonu a využití vaší aplikace.
* Použití [testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) ke kontrole neustále z aplikace po celém světě.
* [Injektáž závislostí v ASP.NET Core](https://docs.microsoft.com/aspnet/fundamentals/dependency-injection)
