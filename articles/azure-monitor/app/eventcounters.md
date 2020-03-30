---
title: Čítače událostí v přehledech aplikací | Dokumenty společnosti Microsoft
description: Monitorujte systém a vlastní čítače .NET/.NET Core EventCounters v Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663585"
---
# <a name="eventcounters-introduction"></a>EventCounters úvod

`EventCounter`je .NET/.NET Core mechanismus pro publikování a využití čítačů nebo statistiky. [Tento](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) dokument poskytuje `EventCounters` přehled a příklady, jak je publikovat a využívat. EventCounters jsou podporovány ve všech platformách operačního systému – Windows, Linux a macOS. To si lze myslet jako ekvivalent pro různé platformy pro [PerformanceCounters,](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) který je podporován pouze v systémech Windows.

Zatímco uživatelé mohou `EventCounters` publikovat libovolný vlastní, aby vyhovoval jejich potřebám, zaběhu .NET Core 3.0 publikuje sadu těchto čítačů ve výchozím nastavení. Dokument provede kroky potřebné ke shromažďování `EventCounters` a zobrazení (definované systémem nebo uživatelem) v Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Shromažďování čítačů Událostí pomocí přehledů aplikací

Application Insights `EventCounters` podporuje `EventCounterCollectionModule`shromažďování s jeho , který je součástí nově vydanénuget balíček [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`je automaticky povolena při použití [aspNetCore](asp-net-core.md) nebo [WorkerService](worker-service.md). `EventCounterCollectionModule`shromažďuje čítače s nekonfigurovatelnou frekvencí sběru 60 sekund. Neexistují žádná zvláštní oprávnění potřebná ke shromažďování čítačů EventCounters.

## <a name="default-counters-collected"></a>Výchozí čítače shromážděné

Pro aplikace spuštěné v rozhraní .NET Core 3.0 jsou sada SDK automaticky shromažďovány následující čítače. Název čítačů bude ve tvaru Kategorie| Počítadlo".

|Kategorie | Čítač|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Čítače kategorie Microsoft.AspNetCore.Hosting jsou přidány pouze v ASP.NET základní aplikace.

## <a name="customizing-counters-to-be-collected"></a>Přizpůsobení čítačů, které mají být shromažďovány

Následující příklad ukazuje, jak přidat nebo odebrat čítače. Toto přizpůsobení by `ConfigureServices` bylo provedeno v metodě aplikace po Application `AddApplicationInsightsTelemetry()` `AddApplicationInsightsWorkerService()`Insights telemetrie kolekce je povolena pomocí buď nebo . Následuje ukázkový kód z aplikace ASP.NET Core. Další typy aplikací naleznete v [tomto](worker-service.md#configuring-or-removing-default-telemetrymodules) dokumentu.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Čítače událostí v Průzkumníku metrik

Chcete-li zobrazit metriky EventCounter v [Průzkumníku metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), vyberte prostředek Application Insights a zvolte metriky založené na protokolu jako obor názvů metriky. Pak EventCounter metriky získat zobrazí v rámci vlastní kategorie.

> [!div class="mx-imgBorder"]
> ![Čítače událostí hlášené v přehledech aplikací](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Čítače událostí v Analytics

Přehledy čítačů událostí můžete také vyhledávat a zobrazovat v [Analytics](../../azure-monitor/app/analytics.md)v tabulce **vlastní metriky.**

Spusťte například následující dotaz, abyste zjistili, jaké čítače jsou shromažďovány a k dispozici pro dotaz:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Čítače událostí hlášené v přehledech aplikací](./media/event-counters/analytics-event-counters.png)

Chcete-li získat graf konkrétního čítače (například: `ThreadPool Completed Work Item Count`) za poslední období, spusťte následující dotaz.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat s jedním čítačem v Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Stejně jako ostatní telemetrie, `cloud_RoleInstance` **customMetrics** má také sloupec, který označuje identitu instance hostitelského serveru, na kterém je spuštěna vaše aplikace. Výše uvedený dotaz zobrazuje hodnotu čítače na instanci a lze jej použít k porovnání výkonu různých instancí serveru.

## <a name="alerts"></a>Výstrahy
Stejně jako ostatní metriky můžete [nastavit výstrahu,](../../azure-monitor/app/alerts.md) která vás upozorní, pokud počítadlo událostí nespadá mimo zadaný limit. Otevřete podokno Výstrahy a klikněte na Přidat výstrahu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Mohu vidět EventCounters v Živé metriky?

Živé metriky nezobrazují EventCounters od dnešního dne. Telemetrický program zobrazíte pomocí Průzkumníka metrik nebo analýzy.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Které platformy mohou zobrazit výchozí seznam čítačů .NET Core 3.0?

EventCounter nevyžaduje žádná zvláštní oprávnění a je podporován ve všech platformách .NET Core 3.0 je podporován. To zahrnuje:

* **Operační systém**: Windows, Linux nebo macOS.
* **Hosting metoda**: V procesu nebo mimo proces.
* **Metoda nasazení**: Framework závislé nebo samostatné.
* **Webový server**: IIS (Internet Information Server) nebo Kestrel.
* **Hostitelská platforma:** Funkce Webové aplikace služby Azure App Service, Virtuálního počítače Azure, Dockeru, služby Azure Kubernetes Service (AKS) a tak dále.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Povolil jsem přehledy aplikací z portálu Azure Web App Portal. Ale nevidím EventCounters.?

 [Rozšíření Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) pro ASP.NET Core tuto funkci ještě nepodporuje. Tento dokument bude aktualizován, pokud je tato funkce podporována.

## <a name="next-steps"></a><a name="next"></a>Další kroky

* [Sledování závislostí](../../azure-monitor/app/asp-net-dependencies.md)
