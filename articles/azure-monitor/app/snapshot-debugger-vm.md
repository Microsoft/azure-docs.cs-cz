---
title: Povolení Snapshot Debugger pro aplikace .NET v Azure Service Fabric, cloudové službě a Virtual Machines | Microsoft Docs
description: Povolení Snapshot Debugger pro aplikace .NET v Azure Service Fabric, cloudové službě a Virtual Machines
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 5a6cf763ae16b55806df2acaf2e03fd8c13d1e76
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359287"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Povolení Snapshot Debugger pro aplikace .NET v Azure Service Fabric, cloudové službě a Virtual Machines

Pokud aplikace ASP.NET nebo ASP.NET Core běží v Azure App Service, důrazně doporučujeme [povolit Snapshot Debugger prostřednictvím stránky Application Insightsového portálu](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Pokud však vaše aplikace vyžaduje upravenou konfiguraci Snapshot Debugger nebo verzi Preview rozhraní .NET Core, pak by tato instrukce měla následovat ***kromě*** pokynů pro [povolení prostřednictvím portálu Application Insights stránky](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Pokud vaše aplikace běží v Azure Service Fabric, cloudové službě, Virtual Machines nebo na místních počítačích, měli byste použít následující pokyny. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurace shromažďování snímků pro aplikace ASP.NET

1. [Povolit Application Insights ve webové aplikaci](../../azure-monitor/app/asp-net.md), pokud jste to ještě neudělali.

2. Zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíčku NuGet ve vaší aplikaci.

3. V případě potřeby přizpůsobené Snapshot Debugger konfiguraci přidanou do [souboru ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Výchozí konfigurace Snapshot Debugger je většinou prázdná a všechna nastavení jsou volitelná. Tady je příklad, který ukazuje konfiguraci odpovídající výchozí konfiguraci:

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

4. Snímky se shromažďují pouze na výjimky, které se hlásí do Application Insights. V některých případech (například starší verze platformy .NET), možná budete muset [Konfigurovat shromažďování výjimek](../../azure-monitor/app/asp-net-exceptions.md#exceptions) pro zobrazení výjimek pomocí snímků na portálu.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Konfigurace kolekce snímků pro aplikace s využitím ASP.NET Core 2,0 nebo vyšší

1. [Povolit Application Insights ve webové aplikaci ASP.NET Core](../../azure-monitor/app/asp-net-core.md), pokud jste to ještě neudělali.

    > [!NOTE]
    > Být jisti, že vaše aplikace odkazuje na verzi 2.1.1 nebo novější, Microsoft.ApplicationInsights.AspNetCore balíčku.

2. Zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíčku NuGet ve vaší aplikaci.

3. Upravit svou aplikaci `Startup` třídy přidání a konfigurace procesoru telemetrie Snapshot Collector.
    1. Pokud se používá balíček NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) verze 1.3.5 nebo vyšší, přidejte následující příkazy using do `Startup.cs`.

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Přidejte následující na konec metody ConfigureServices ve `Startup` třídě v. `Startup.cs`

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Pokud se používá balíček NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) verze 1.3.4 nebo nižší, přidejte následující příkazy using do `Startup.cs`.

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Přidejte následující `SnapshotCollectorTelemetryProcessorFactory` třídu `Startup` třídy.
    
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
        Přidat `SnapshotCollectorConfiguration` a `SnapshotCollectorTelemetryProcessorFactory` služby pro spuštění kanálu:
    
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

4. V případě potřeby přizpůsobené konfiguraci Snapshot Debugger přidáním oddílu SnapshotCollectorConfiguration do souboru appSettings. JSON. Všechna nastavení v konfiguraci Snapshot Debugger jsou volitelná. Tady je příklad, který ukazuje konfiguraci odpovídající výchozí konfiguraci:

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

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurace shromažďování snímků pro jiné aplikace .NET

1. Pokud vaše aplikace není již instrumentována pomocí nástroje Application Insights, začněte tím, že [povolením Application Insights a že Instrumentační klíč nastavíte](../../azure-monitor/app/windows-desktop.md).

2. Přidat [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíčku NuGet ve vaší aplikaci.

3. Snímky se shromažďují pouze na výjimky, které se hlásí do Application Insights. Budete muset změnit váš kód pro nahlášení je. Kód zpracování výjimek závisí na struktuře aplikace, ale příkladem je nižší než:
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

## <a name="next-steps"></a>Další postup

- Vygenerujte provoz do vaší aplikace, který může aktivovat výjimku. Potom počkejte 10 až 15 minut, než se snímky odešlou do instance Application Insights.
- Podívejte se na [snímky](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) v Azure Portal.
- Nápovědu k řešení potíží s Snapshot Debugger najdete v tématu [řešení potíží s Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
