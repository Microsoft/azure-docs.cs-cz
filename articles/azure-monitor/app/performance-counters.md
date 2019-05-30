---
title: Čítače výkonu ve službě Application Insights | Dokumentace Microsoftu
description: Monitorování systému a vlastní čítače výkonu .NET ve službě Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: 0ec64a5ae412fb4a1900021fefcb7d9112b1b019
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255326"
---
# <a name="system-performance-counters-in-application-insights"></a>Čítače výkonu systému ve službě Application Insights

Windows nabízí širokou škálu [čítačů výkonu](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), například pro vytížení procesoru nebo využití paměti, disku a sítě. Můžete také definovat vlastní čítače výkonu. Shromažďování čítačů výkonu je podporován tak dlouho, dokud je aplikace spuštěna v rámci služby IIS na místního hostitele nebo virtuální počítač, ke kterému máte přístup pro správu. I když aplikace běžící jako Azure Web Apps nemají přímý přístup k čítačům výkonu, jsou shromažďovány podmnožinu dostupné čítače službou Application Insights.

## <a name="view-counters"></a>Zobrazení čítačů

V podokně metriky se zobrazí výchozí sadu čítačů výkonu.

![Čítače výkonu v Application Insights](./media/performance-counters/performance-counters.png)

Aktuální výchozí čítače, které jsou nakonfigurované jako shromážděných pro ASP.NET/ASP.NET základní webové aplikace jsou:

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

## <a name="add-counters"></a>Přidání čítačů

Pokud čítač výkonu, které chcete nejsou zahrnuty v seznamu metrik, můžete ho přidat.

1. Zjistěte, jaké čítače jsou k dispozici na serveru pomocí tohoto příkazu prostředí PowerShell na místním serveru:

    `Get-Counter -ListSet *`

    (Viz [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Open ApplicationInsights.config.

   * Pokud jste přidali Application Insights do vaší aplikace během vývoje, upravit soubor ApplicationInsights.config v projektu a pak ho znovu nasadíte na serverech.
3. Upravte směrnice kolekce výkonu:

```XML

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

> [!NOTE]
> Aplikace ASP.NET Core nemají `ApplicationInsights.config`, a proto výše uvedené metody není platný pro aplikace ASP.NET Core.

Můžete zaznamenat standardní čítače i ty že jsme implementovali sami. `\Objects\Processes` je příkladem standardní čítač, který je k dispozici na všech systémech Windows. `\Sales(photo)\# Items Sold` je příkladem vlastní čítače, která může být implementována ve webové službě.

Formát je `\Category(instance)\Counter"`, nebo kategorií, které nemají instancí, právě `\Category\Counter`.

`ReportAs` je vyžadován pro názvy čítačů, které se neshodují s `[a-zA-Z()/-_ \.]+` – to znamená, které obsahují znaky, které nejsou v následujících sadách: písmena, kulaté závorky, lomítkem, spojovník, podtržítko, mezery, tečky.

Pokud chcete zadat instanci, nebudou se shromažďovat jako dimenze "CounterInstanceName" ohlášené metriky.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Shromažďování čítačů výkonu v kódu pro webové aplikace ASP.NET nebo.NET/.NET základní konzolové aplikace
Pokud chcete shromáždit čítače výkonu systému a posílat do Application Insights, můžete přizpůsobit následující fragment:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Nebo můžete provést totéž s vlastní metriky, které jste vytvořili:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Shromažďování čítačů výkonu v kódu pro webové aplikace ASP.NET Core

Upravit `ConfigureServices` metoda ve vaší `Startup.cs` třídy jak je uvedeno níže.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Čítače výkonu v Analytics
Můžete vyhledat a zobrazit sestavy čítačů výkonu v [Analytics](../../azure-monitor/app/analytics.md).

**Čítače výkonu** zpřístupňuje schématu `category`, `counter` název, a `instance` název jednotlivých čítačů výkonu.  V telemetrii pro každou aplikaci zobrazí se vám jenom čítače pro tuto aplikaci. Například pokud chcete zobrazit jsou k dispozici co čítače: 

![Čítače výkonu ve službě Application Insights analytics](./media/performance-counters/analytics-performance-counters.png)

('Instance' tady odkazuje na instanci čítače výkonu, není role nebo instance počítače serveru. Název instance čítače výkonu obvykle segmenty čítače, jako například čas procesoru podle názvu procesu nebo aplikace.)

Pokud chcete získat grafu dostupné paměti za poslední období: 

![Paměť promítnu ve službě Application Insights analytics](./media/performance-counters/analytics-available-memory.png)

Další telemetrická data, jako jsou **čítače výkonu** má také sloupec `cloud_RoleInstance` , která určuje identitu instance hostitelského serveru, na kterém je spuštěná aplikace. Chcete-li například porovnat výkon vaší aplikace na různých počítačích: 

![Výkon segmentované podle instance role ve službě Application Insights analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET a počty Application Insights

*Jaký je rozdíl mezi frekvence výjimek a metriky výjimky?*

* *Frekvence výjimek* je čítač výkonu systému. CLR vrátí počet všech zpracovaných a nezpracovaných výjimek, které jsou vyvolány a vydělí celkový počet v intervalu vzorkování délku intervalu. Sadu SDK Application Insights shromažďuje tento výsledek a odešle ji na portál.

* *Výjimky* je počet sestav TrackException přijatých na portálu v intervalu vzorkování grafu. Zahrnuje pouze zpracované výjimky, ve kterém jste napsali TrackException volání v kódu a nezahrnuje všechny [neošetřené výjimky](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Čítače výkonu pro aplikace běžící v Azure Web Apps

Aplikace ASP.NET a ASP.NET Core nasadit do Azure Web Apps běží v prostředí izolovaného prostoru speciální. Toto prostředí neumožňuje přímý přístup k čítačům výkonu systému. Ale jsou omezené dílčí sady čítačů zveřejní jako proměnné prostředí, jak je popsáno [tady](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK pro ASP.NET a ASP.NET Core z těchto proměnných prostředí speciální shromažďuje čítače výkonu z Azure Web Apps. Pouze podmnožinu čítače jsou k dispozici v tomto prostředí, a najdete úplný seznam [tady.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Čítače výkonu aplikace ASP.NET Core

* [ASP.NET Core SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verzi 2.4.1 a nad shromažďuje čítače výkonu, pokud je aplikace spuštěná ve službě Azure Web App (Windows)

* Sada SDK verze 2.7.0-beta3 a nad shromažďuje čítače výkonu, pokud aplikace běží ve Windows a cílení `NETSTANDARD2.0` nebo vyšší.
* Pro aplikace cílené na rozhraní .NET Framework čítače výkonu jsou podporovány ve všech verzích SDK.
* Tento článek bude aktualizován, když se přidá podpora čítače výkonu v jiných Windows.

## <a name="alerts"></a>Výstrahy
Stejně jako jiné metriky můžete [nastavit výstrahu](../../azure-monitor/app/alerts.md) upozornit, pokud čítač výkonu přejde mimo omezení zadáte. Otevřete podokno oznámení a klikněte na tlačítko Přidat oznámení.

## <a name="next"></a>Další kroky

* [Sledování závislostí](../../azure-monitor/app/asp-net-dependencies.md)
* [Sledování výjimek](../../azure-monitor/app/asp-net-exceptions.md)

