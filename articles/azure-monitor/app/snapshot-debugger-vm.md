---
title: Povolení ladicího programu snímků pro aplikace .NET v Azure Service Fabric, Cloud Service a Virtual Machines | Dokumenty společnosti Microsoft
description: Povolení ladicího programu snímků pro aplikace .NET v Azure Service Fabric, Cloud Service a Virtual Machines
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 194a2da23c8fb405c492df8f6ee173cc97fde4ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671334"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Povolení ladicího programu snímků pro aplikace .NET v Azure Service Fabric, Cloud Service a Virtual Machines

Pokud vaše ASP.NET nebo ASP.NET základní aplikace běží ve službě Azure App Service, důrazně doporučujeme [povolit debugger snímků prostřednictvím portálu Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Pokud však vaše aplikace vyžaduje přizpůsobenou konfiguraci ladicího programu snímků nebo verzi preview jádra .NET, měla by být tato instrukce následována ***kromě*** pokynů pro [povolení prostřednictvím stránky portálu Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Pokud vaše aplikace běží v Azure Service Fabric, cloudové služby, virtuální počítače nebo místní počítače, by měly být použity následující pokyny. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurace kolekce snímků pro ASP.NET aplikace

1. Pokud jste to ještě neudělali, [povolte ve webové aplikaci Přehledy aplikací.](../../azure-monitor/app/asp-net.md)

2. Zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet balíček ve vaší aplikaci.

3. V případě potřeby přizpůsobte konfiguraci ladicího programu snímek přidanou do [souboru ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Výchozí konfigurace ladicího programu snímků je většinou prázdná a všechna nastavení jsou volitelná. Zde je příklad, který ukazuje konfiguraci ekvivalentní výchozí konfiguraci:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Snímky jsou shromažďovány pouze na výjimky, které jsou hlášeny application insights. V některých případech (například starší verze platformy .NET) může být nutné [nakonfigurovat kolekci výjimek,](../../azure-monitor/app/asp-net-exceptions.md#exceptions) aby se zobcely výjimky se snímky na portálu.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Konfigurace kolekce snímků pro aplikace používající ASP.NET Core 2.0 nebo vyšší

1. Pokud jste to ještě neudělali, [povolte ve své webové aplikaci ASP.NET Core přehledy](../../azure-monitor/app/asp-net-core.md)aplikací.

    > [!NOTE]
    > Ujistěte se, že vaše aplikace odkazuje na verzi 2.1.1 nebo novější balíčku Microsoft.ApplicationInsights.AspNetCore.

2. Zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet balíček ve vaší aplikaci.

3. Upravte třídu `Startup` aplikace a přidejte a nakonfigurujte telemetrický procesor sběratele snímků.
    1. Pokud [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet balíček verze 1.3.5 nebo vyšší se `Startup.cs`používá, přidejte následující using příkazy .

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Přidejte následující na konci ConfigureServices metoda `Startup` ve `Startup.cs`třídě v .

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Pokud [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet balíček verze 1.3.4 nebo nižší je `Startup.cs`použit, přidejte následující using příkazy .

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Přidejte `SnapshotCollectorTelemetryProcessorFactory` do `Startup` třídy následující třídu.
    
       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;
    
               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;
    
               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Přidejte `SnapshotCollectorConfiguration` `SnapshotCollectorTelemetryProcessorFactory` služby a do spouštěcího kanálu:
    
        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));
    
               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));
    
               // TODO: Add other services your application needs here.
           }
       }
       ```

4. V případě potřeby přizpůsobte konfiguraci ladicího programu snímek přidáním oddílu SnapshotCollectorConfiguration do souboru appsettings.json. Všechna nastavení v konfiguraci ladicího programu snímek jsou volitelná. Zde je příklad, který ukazuje konfiguraci ekvivalentní výchozí konfiguraci:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurace kolekce snímků pro jiné aplikace .NET

1. Pokud vaše aplikace ještě není instrumentovaná s Application Insights, můžete začít [povolením Application Insights a nastavením klíče instrumentace](../../azure-monitor/app/windows-desktop.md).

2. Přidejte balíček [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet do aplikace.

3. Snímky jsou shromažďovány pouze na výjimky, které jsou hlášeny application insights. Možná budete muset upravit kód, abyste je nahlásili. Kód zpracování výjimek závisí na struktuře aplikace, ale příklad je následující:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Další kroky

- Generovat provoz do aplikace, která může vyvolat výjimku. Potom počkejte 10 až 15 minut pro snímky, které mají být odeslány do instance Application Insights.
- Podívejte se na [snímky](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) na webu Azure Portal.
- Nápovědu k řešení problémů s ladicím programem snímků naleznete v [tématu Odstraňování ladicích snímků snímek](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
