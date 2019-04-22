---
title: Azure Application Insights pro ASP.NET Core bez sady Visual Studio | Dokumentace Microsoftu
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
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288363"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights pro aplikace ASP.NET Core

Tento článek popisuje kroky pro povolení Application Insights pro [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) aplikace bez použití integrovaného vývojového prostředí sady Visual Studio. Pokud máte nainstalován, pak IDE sady Visual Studio [to](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) dokument obsahuje pokyny pro konkrétní sady Visual Studio. Pomocí pokynů v tomto článku, Application Insights začne shromažďování požadavky, závislosti, výjimky, čítače výkonu, prezenční signály a protokoly z aplikace ASP.NET Core. Je ukázková aplikace použitá [aplikace MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2) cílení `netcoreapp2.2`, ale zde pokynů se dají použít u všech aplikací ASP.NET Core. Jakékoli výjimky jsou uvedeny v případě potřeby.

## <a name="supported-scenarios"></a>Podporované scénáře

Application Insights SDK (Software Development Kit) pro ASP.NET Core můžete sledovat vaše aplikace bez ohledu na to, kdy nebo jak při spuštění aplikace. Pokud vaše aplikace běží a má síťové připojení ke službě Application Insights, očekává se telemetrická data se mají shromažďovat. Tato podpora zahrnuje, ale není omezena pouze na libovolný operační systém (Windows, Linux, Mac), metodu hostování (vs v procesu mimo proces), metoda nasazení (závisí na architektuře vs samostatná), webový Server (IIS, Kestrel), platforma (Azure Web Apps, virtuální počítač Azure, Docker, AKS atd.) nebo integrované vývojové prostředí (Visual Studio, VS Code, příkazový řádek.)

## <a name="prerequisites"></a>Požadavky

- Funkční aplikace ASP.NET Core. Postupujte podle [to](https://docs.microsoft.com/aspnet/core/getting-started/) průvodce k vytvoření aplikace ASP.NET Core, v případě potřeby.
- Platný Instrumentační klíč Application Insights, který je vyžadován pro odeslání všech telemetrických dat do služby Application Insights. Postupujte podle [tyto](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) pokyny, jak vytvořit nový prostředek Application Insights v případě potřeby a získat Instrumentační klíč.

## <a name="enabling-application-insights-server-side-telemetry"></a>Povolení Telemetrie Application Insights na straně serveru

1. Nainstalujte sadu Application Insights SDK pro ASP.NET Core, který je pravidelně balíček NuGet. Doporučujeme vždy používat nejnovější stabilní verzi. Některé funkce popsané v tomto článku nemusí být k dispozici ve starších verzích. Kompletní poznámky k verzi sady SDK najdete [tady](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases). 

Následuje ukázka změny, které mají být přidány do souboru .csproj projektu.

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

   I když je možné zadat Instrumentační klíč jako argument `services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`, doporučujeme zadat Instrumentační klíč v konfiguraci. Následující znázorňuje způsob zadávání instrumentačního klíče v `appsettings.json`. Ujistěte se, že `appsettings.json` zkopírována do kořenové složky aplikace při publikování.

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

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` Obvykle se používá k určení Instrumentační klíč pro aplikace nasazené do služby Azure Web Apps.

> [!NOTE]
> Instrumentačním klíčem zadaný ve službě wins kódu přes proměnnou prostředí `APPINSIGHTS_INSTRUMENTATIONKEY`, které služby wins za další možnosti.

4. Spusťte aplikaci a požadavky na ni. Telemetrická data by teď měly začít odesílaných do Application Insights. Následující telemetrická data se automaticky shromažďují pomocí Application Insights SDK.

    1. Požadavky – příchozí webové požadavky do vaší aplikace.
    1. Závislosti
        1. Http/Https volání s `HttpClient`
        1. Volání SQL s `SqlClient`
        1. Volání služby Azure storage s [klienta úložiště Azure](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [Klientské sady SDK centra událostí](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) verze 1.1.0 a vyšší
        1. [Klientské sady SDK služby Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) verze 3.0.0 a vyšší

             * Azure Cosmos DB se automaticky sleduje pouze v případě, že se používá HTTP/HTTPS. Režim TCP nebude zachycena Application Insights.


    1. [Čítače výkonu](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. Podpora pro čítače výkonu v ASP.NET Core je omezena na následující
            1. Sada SDK verze 2.4.1 a nad shromažďuje čítače výkonu, pokud je aplikace spuštěná ve službě Azure Web App (Windows)
            1. Sada SDK verze 2.7.0-beta3 a nad shromažďuje čítače výkonu, pokud aplikace běží ve Windows a cílení `NETSTANDARD2.0` nebo vyšší.
            1. Pro aplikace, které cílí na úplné rozhraní .NET Framework čítače výkonu jsou podporovány ve všech verzích SDK.

            Když se přidá podpora čítače výkonu v systému Linux, bude tento dokument aktualizován.
    1. [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` protokoly závažnost `Warning` nebo výše jsou automaticky zachycené ze sady SDK verze 2.7.0-beta3 nebo vyšší. Přečtěte si další [tady](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

Může trvat několik minut, než začnou zobrazovat na portálu telemetrická data. Chcete-li rychle zkontrolovat, zda vše funguje, je nejvhodnější použít [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)při posílání požadavků na běžící aplikaci.

## <a name="send-ilogger-logs-to-application-insights"></a>Odeslání protokolů s ILogger Application Insights

Application Insights podporuje zachytávající protokoly se odeslaly prostřednictvím objektu ILogger. Přečtěte si úplné dokumentaci [tady](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="enable-client-side-telemetry-for-web-applications"></a>Povolit Telemetrii na straně klienta pro webové aplikace

Výše uvedené kroky jsou dostatečná spuštění shromažďování telemetrie na straně serveru. Pokud má vaše aplikace klientské komponenty, postupujte podle následujících kroků a spustit shromažďování [telemetrii jejich využívání](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) z něj.

1. V _ViewImports.cshtml přidejte vkládání:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. V _Layout.cshtml, vložit na konec HtmlHelper `<head>` oddílu, ale před veškerým skriptem. Vlastní telemetrii jazyka JavaScript, které chcete do sestavy ze stránky by měl vloží po tento fragment kódu:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Upravte názvy souborů podle aplikace skutečný. Názvy využité nad se z výchozí šablony MVC aplikace.

## <a name="configuring-application-insights-sdk"></a>Konfigurace Application Insights SDK

Application Insights SDK pro ASP.NET Core je možné přizpůsobit pro změnu výchozí konfigurace. Uživatele sadu SDK Application Insights technologie ASP.NET pravděpodobně mají zkušenosti s pomocí konfigurace `ApplicationInsights.config`, nebo tak, že upravíte `TelemetryConfiguration.Active`. Pro ASP.NET Core se provádí konfigurace odlišně. ASP.NET Core SDK se přidá do aplikace pomocí ASP.NET Core integrované [DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) mechanismus a konfigurace stejné by také používat DependencyInjection. Téměř všechny změny konfigurace se provádějí v `ConfigureServices()` metodu vaše `Startup.cs` třídy, pokud není uvedeno jinak. Použijte následující části obsahují další informace.

> [!NOTE]
> Je důležité si uvědomit, že změna konfigurace tak, že upravíte `TelemetryConfiguration.Active` se nedoporučuje v aplikacích ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Konfigurace pomocí ApplicationInsightsServiceOptions

Je možné změnit několik běžných nastavení předáním `ApplicationInsightsServiceOptions` k `services.AddApplicationInsightsTelemetry();`. Příklad je uveden níže.

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

Přesný seznam konfigurovatelných nastavení v `ApplicationInsightsServiceOptions` najdete [tady](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Vzorkování

Application Insights SDK pro ASP.NET Core podporuje FixedRate a adaptivní vzorkování. Ve výchozím nastavení je povolené adaptivní vzorkování. Postupujte podle [to](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) dokumentu se naučíte konfigurovat vzorkování pro aplikace ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Přidání TelemetryInitializers

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

Procesory vlastní telemetrická data mohou být přidány do `TelemetryConfiguration` pomocí metody rozšíření `AddApplicationInsightsTelemetryProcessor` na `IServiceCollection`. Pomocí následujícího příkladu.

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

Následující moduly automaticky kolekce jsou ve výchozím nastavení povolené a odpovídáte za automaticky shromažďuje telemetrická data. Může být zakázán a nakonfigurovat tak, aby změnit výchozí chování.

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

Ke konfiguraci žádné výchozí `TelemetryModule`, použijte metodu rozšíření `ConfigureTelemetryModule<T>` na `IServiceCollection` jak je znázorněno v následujícím příkladu.

```csharp
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
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

*1. Chci sledovat některé další telemetrické údaje kromě automaticky shromažďovat telemetrická data. Jak to udělám?*

* Získání instance `TelemetryClient` tím, že pomocí konstruktoru vkládání a volat požadované `TrackXXX()` metoda na něj. Není doporučeno vytvořit novou `TelemetryClient` instancí aplikace ASP.NET Core jako instanci typu singleton `TelemetryClient` je už zaregistrovaný do kontejnerů DI, který sdílí `TelemetryConfiguration` se zbytkem telemetrická data. Vytvoření nového `TelemetryClient` instance se doporučuje jenom v případě, že má samostatné konfigurace od zbytku telemetrická data. Následující příklad ukazuje, jak sledovat další telemetrické údaje z kontroleru.

```csharp
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

*2. Některé šablony sady Visual Studio využít UseApplicationInsights() rozšiřující metoda na IWebHostBuilder Application Insights. Toto použití je stále platný?*

* Povolením Application Insights s touto metodou je platný a se používá v sadě Visual Studio v rámci zprovozňování a také rozšíření webové aplikace Azure. Doporučujeme však použít `services.AddApplicationInsightsTelemery()` poskytuje přetížení k řízení určitou konfiguraci. Jak metoda interně provede stejnou akci, pokud není žádná vlastní konfigurace použije, volání buď je v pořádku.

*3. Můžu jsem nasazení Moje aplikace ASP.NET Core do Azure Web Apps. Měli stále povolit rozšíření Application Insights z webových aplikací?*

* Pokud sada SDK je nainstalovaná v okamžiku sestavení, jak je znázorněno v tomto článku, není nutné k povolení rozšíření Application Insights na portálu webových aplikací. I v případě, že je rozšíření nainstalované, ho bude regrese, když zjistí, že sada SDK je už přidané do aplikace. Povolením Application Insights z rozšíření není instalaci a aktualizaci sady SDK do vaší aplikace. Povolením Application Insights podle tohoto článku je ale flexibilnější z důvodů níže.
    1. Telemetrie Application Insights bude pokračovat v práci
        1. Všechny operační systémy – Windows, Linuxu a macu.
        1. Všechny publikovat režim – samostatná nebo závisí na architektuře.
        1. Všechny cílové architektury, včetně úplné rozhraní .NET Framework.
        1. Všechny možnosti hostování – webové aplikace Azure, virtuálních počítačů, Linux, kontejnery, AKS, mimo Azure.
    1. Telemetrii můžete zobrazit místně, při ladění ze sady Visual Studio.
    1. Umožňuje používat další vlastní telemetrická data sledování `TrackXXX()` rozhraní API.
    1. Má plnou kontrolu nad konfigurace.

*4. Můžete povolit monitorování pomocí nástrojů, jako je monitorování stavu Application Insights?*

* Ne. [Monitorování stavu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) a jejím nahrazení nadcházející [IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21) aktuálně podporuje pouze technologie ASP.NET. Dokument bude aktualizován po podporu pro aplikace ASP.NET Core je k dispozici.

*5. Mám aplikaci ASP.NET Core 2.0? Application Insights nepovoluje automaticky je bez mi teď zrovna nic nedělá?*

* `Microsoft.AspNetCore.All` 2.0 Microsoft.aspnetcore.all zahrnuté sadu SDK Application Insights (verze 2.1.0), a pokud spustíte aplikaci v ladicím programu sady Visual Studio, Visual Studio umožňuje služby application insights a zobrazuje telemetrická data místně v samotném integrovaném vývojovém prostředí. Telemetrie nebyla proto odeslána do služby Application Insights, výslovně uvedeno Instrumentační klíč. Doporučujeme postupujte podle pokynů v tomto článku a povolte Application Insights, i pro 2.0 aplikace.

*6. Spustit aplikaci v Linuxu. Všechny funkce podporuje Linux i?*

* Ano. Podpora funkce sady SDK je stejná ve všech platformách, s následujícími výjimkami:
    1. Čítače výkonu se zatím nepodporují v Non-Windows. Když se přidá podpora Linuxu, bude tento dokument aktualizován.
    1. I když `ServerTelemetryChannel` je povolená ve výchozím nastavení, pokud je aplikace spuštěna v jiný systém než windows, kanál neprovádí automatické vytváření složky místní úložiště dočasně zachovat telemetrická data, pokud existují problémy se sítí. Toto omezení způsobí, že telemetrická data ztratí, pokud jsou dočasné síť nebo server problémy. Alternativní řešení tohoto problému je pro uživatele nakonfigurovat místní složku pro kanál, jak je znázorněno níže.

```csharp
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
