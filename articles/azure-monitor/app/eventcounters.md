---
title: Čítače událostí v Application Insights | Microsoft Docs
description: Monitorujte systém a vlastní .NET/.NET Core EventCounters v Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 000486ecd4fddd5749e4c7cc9f9210a1f0f8666c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272361"
---
# <a name="eventcounters-introduction"></a>Úvod do EventCounters

`EventCounter` je základní mechanismus .NET/.NET pro publikování a používání čítačů nebo statistik. [Tento](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) dokument obsahuje přehled `EventCounters` a příklady, jak je publikovat a využívat. EventCounters jsou podporované na všech platformách OS – Windows, Linux a macOS. Můžeme si představit jako ekvivalent pro [čítače výkonu](/dotnet/api/system.diagnostics.performancecounter) pro různé platformy, který je podporován pouze v systémech Windows.

I když uživatelé mohou publikovat jakékoli vlastní `EventCounters` , aby splnili požadavky, modul runtime .NET Core 3,0 ve výchozím nastavení publikuje sadu těchto čítačů. Dokument vás provede kroky potřebnými ke shromáždění a zobrazení `EventCounters` (definované systémem nebo uživatelem) v Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Shromažďování EventCounters pomocí Application Insights

Application Insights podporuje shromažďování `EventCounters` s jeho `EventCounterCollectionModule` součástí nově vydaných balíčků NuGet [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule` se automaticky povolí při použití [AspNetCore](asp-net-core.md) nebo [WorkerService](worker-service.md). `EventCounterCollectionModule` shromažďuje čítače s nekonfigurovatelnou frekvencí kolekce 60 sekund. Ke shromáždění EventCounters nejsou nutná žádná zvláštní oprávnění.

## <a name="default-counters-collected"></a>Shromážděny výchozí čítače

Pro aplikace běžící v .NET Core 3,0 se sada SDK automaticky shromáždí následující čítače. Název čítačů bude ve formátu "kategorie | Čítač ".

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

> [!NOTE]
> Čítače kategorie Microsoft. AspNetCore. Hosting se přidávají jenom v aplikacích ASP.NET Core.

## <a name="customizing-counters-to-be-collected"></a>Přizpůsobení čítačů, které se mají shromažďovat

Následující příklad ukazuje, jak přidat nebo odebrat čítače. Toto přizpůsobení se provede v `ConfigureServices` metodě vaší aplikace po povolení Application Insights kolekce telemetrie pomocí `AddApplicationInsightsTelemetry()` nebo `AddApplicationInsightsWorkerService()` . Následuje příklad kódu z aplikace ASP.NET Core. Pro jiný typ aplikace, přečtěte si [Tento](worker-service.md#configuring-or-removing-default-telemetrymodules) dokument.

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

## <a name="event-counters-in-metric-explorer"></a>Čítače událostí v Průzkumníkovi metrik

Pokud chcete v [Průzkumníkovi metriky](../platform/metrics-charts.md)zobrazit EventCounter metriky, vyberte Application Insights prostředek a zvolte metriky založené na protokolu jako obor názvů metriky. Pak se metriky EventCounter zobrazí v kategorii vlastní.

> [!div class="mx-imgBorder"]
> ![Čítače událostí hlášené v Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Čítače událostí v analýzách

V tabulce **customMetrics** můžete také vyhledávat a zobrazovat sestavy čítače událostí v části [analýzy](../log-query/log-query-overview.md).

Například spuštěním následujícího dotazu zjistíte, jaké čítače jsou shromažďovány a k dispozici pro dotaz:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Čítače událostí hlášené v Application Insights](./media/event-counters/analytics-event-counters.png)

Chcete-li získat graf určitého čítače (například: `ThreadPool Completed Work Item Count` ) za poslední období, spusťte následující dotaz.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat o jednom čítači v Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Podobně jako u jiné telemetrie má **customMetrics** také sloupec `cloud_RoleInstance` , který označuje identitu instance hostitelského serveru, na které je vaše aplikace spuštěná. Výše uvedený dotaz ukazuje hodnotu čítače na instanci a lze ji použít k porovnání výkonu různých instancí serveru.

## <a name="alerts"></a>Výstrahy
Podobně jako u jiných metrik můžete [nastavit výstrahu](../platform/alerts-log.md) , která vás upozorní, pokud počítadlo události překročí zadaný limit. Otevřete podokno výstrahy a klikněte na Přidat výstrahu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Můžu v živých metrikách zobrazit EventCounters?

Živé metriky nezobrazují EventCounters k dnešnímu dni. K zobrazení telemetrie použijte Průzkumníka metrik nebo analýzu.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Jaké platformy se můžu podívat na výchozí seznam čítačů .NET Core 3,0?

EventCounter nevyžaduje žádná zvláštní oprávnění a podporuje se na všech platformách .NET Core 3,0. Sem patří:

* **Operační systém**: Windows, Linux nebo MacOS.
* **Metoda hostování**: v procesu nebo mimo proces.
* **Metoda nasazení**: závislá na architektuře nebo samostatně obsažená.
* **Webový server**: IIS (Internet Information Server) nebo Kestrel.
* **Hostující platforma**: funkce Web Apps Azure App Service, virtuální počítač Azure, Docker, Azure Kubernetes Service (AKS) a tak dále.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Na portálu webové aplikace Azure jsem Application Insights povolit. Ale nevidím EventCounters.?

 [Application Insights rozšíření](./azure-web-apps.md) pro ASP.NET Core tuto funkci ještě nepodporuje. Tento dokument bude aktualizován, pokud je tato funkce podporována.

## <a name="next-steps"></a><a name="next"></a>Další kroky

* [Sledování závislostí](./asp-net-dependencies.md)

