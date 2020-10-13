---
title: Čítače událostí v Application Insights | Microsoft Docs
description: Monitorujte systém a vlastní .NET/.NET Core EventCounters v Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a9af36f3c81ee52b41a8eed875c1a286b95bf838
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803639"
---
# <a name="eventcounters-introduction"></a>Úvod do EventCounters

[`EventCounter`](/dotnet/core/diagnostics/event-counters) je základní mechanismus .NET/.NET pro publikování a používání čítačů nebo statistik. EventCounters jsou podporované na všech platformách OS – Windows, Linux a macOS. Můžeme si představit jako ekvivalent pro [čítače výkonu](/dotnet/api/system.diagnostics.performancecounter) pro různé platformy, který je podporován pouze v systémech Windows.

I když uživatelé mohou publikovat jakékoli vlastní `EventCounters` , aby splnili požadavky, .NET Core 3,0 a vyšší běhový modul zveřejňuje ve výchozím nastavení sadu těchto čítačů. Tento dokument vás provede kroky potřebnými ke shromáždění a zobrazení `EventCounters` (definované systémem nebo uživatelem definovanému) v Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Shromažďování EventCounters pomocí Application Insights

Application Insights podporuje shromažďování `EventCounters` s jeho `EventCounterCollectionModule` součástí nově vydaných balíčků NuGet [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule` se automaticky povolí při použití [AspNetCore](asp-net-core.md) nebo [WorkerService](worker-service.md). `EventCounterCollectionModule` shromažďuje čítače s nekonfigurovatelnou frekvencí kolekce 60 sekund. Ke shromáždění EventCounters nejsou nutná žádná zvláštní oprávnění.

## <a name="default-counters-collected"></a>Shromážděny výchozí čítače

Počínaje verzí 2.15.0 sady [ASPNETCORE SDK](asp-net-core.md) nebo sady [WorkerService SDK](worker-service.md)nejsou ve výchozím nastavení shromažďovány žádné čítače. Samotný modul je povolený, takže uživatelé mohou jednoduše přidat požadované čítače a shromáždit je.

Seznam známých čítačů publikovaných modulem runtime .NET najdete v dokumentu [Dostupné čítače](/dotnet/core/diagnostics/event-counters#available-counters) .

## <a name="customizing-counters-to-be-collected"></a>Přizpůsobení čítačů, které se mají shromažďovat

Následující příklad ukazuje, jak přidat nebo odebrat čítače. Toto přizpůsobení se provede v `ConfigureServices` metodě vaší aplikace po povolení Application Insights kolekce telemetrie pomocí `AddApplicationInsightsTelemetry()` nebo `AddApplicationInsightsWorkerService()` . Následuje příklad kódu z aplikace ASP.NET Core. Pro jiný typ aplikace, přečtěte si [Tento](worker-service.md#configuring-or-removing-default-telemetrymodules) dokument.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters, if any.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>Zakazuje se modul EventCounter Collection.

`EventCounterCollectionModule` lze zakázat pomocí `ApplicationInsightsServiceOptions` . Příklad použití ASP.NET Core SDK je uveden níže.

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

Podobný přístup lze také použít pro WorkerService SDK, ale obor názvů musí být změněn, jak je znázorněno v následujícím příkladu.

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>Čítače událostí v Průzkumníkovi metrik

Pokud chcete v [Průzkumníkovi metriky](../platform/metrics-charts.md)zobrazit EventCounter metriky, vyberte Application Insights prostředek a zvolte metriky založené na protokolu jako obor názvů metriky. Pak se metriky EventCounter zobrazí v kategorii vlastní.

> [!div class="mx-imgBorder"]
> ![Čítače událostí hlášené v Průzkumníkovi metrik Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Čítače událostí v analýzách

V tabulce **customMetrics** můžete také vyhledávat a zobrazovat sestavy čítače událostí v části [analýzy](../log-query/log-query-overview.md).

Například spuštěním následujícího dotazu zjistíte, jaké čítače jsou shromažďovány a k dispozici pro dotaz:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Čítače událostí hlášené v Application Insights Analytics](./media/event-counters/analytics-event-counters.png)

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

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Na portálu webové aplikace Azure jsem Application Insights povolit. Ale nevidím EventCounters.?

 [Application Insights rozšíření](./azure-web-apps.md) pro ASP.NET Core tuto funkci ještě nepodporuje. Tento dokument bude aktualizován, pokud je tato funkce podporována.

## <a name="next-steps"></a><a name="next"></a>Další kroky

* [Sledování závislostí](./asp-net-dependencies.md)

