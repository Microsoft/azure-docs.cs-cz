---
title: Čítače výkonu v přehledech aplikací | Dokumenty společnosti Microsoft
description: Monitorujte čítače výkonu systému a vlastních rozhraní .NET v application insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: 94d2520c17867f6d70caffd002a76365a425986f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669875"
---
# <a name="system-performance-counters-in-application-insights"></a>Čítače výkonu systému v application insights

Windows nabízí širokou škálu [čítačů výkonu](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), například pro vytížení procesoru nebo využití paměti, disku a sítě. Můžete také definovat vlastní čítače výkonu. Kolekce čítačů výkonu je podporována tak dlouho, dokud je vaše aplikace spuštěna ve službě IIS na místním hostiteli nebo virtuálním počítači, ke kterému máte přístup pro správu. Přestože aplikace spuštěné jako Azure Web Apps nemají přímý přístup k čítačům výkonu, podmnožinu dostupných čítačů jsou shromažďovány Application Insights.

## <a name="view-counters"></a>Zobrazit čítače

Podokno Metriky zobrazuje výchozí sadu čítačů výkonu.

![Čítače výkonu hlášené v přehledech aplikací](./media/performance-counters/performance-counters.png)

Aktuální výchozí čítače, které jsou nakonfigurovány tak, aby byly shromažďovány pro ASP.NET/ASP.NET základní webové aplikace jsou:
- %\\času zpracovatele procesu
- %\\normalizovaného času zpracovatele procesu
- Dostupné\\bajty v paměti
- ASP.NET požadavky/s
- Vyvolány výjimky CLR rozhraní .NET / s
- ASP.NET aplikaceŽádost o spuštění
- Zpracovat\\soukromé bajty
- Počet\\bajtů dat vstupně-upu vstupně-patrech/s
- ASP.NET\\požadavky aplikací ve frontě aplikací
- Procesor (_Total)\\% času procesoru

## <a name="add-counters"></a>Přidání čítačů

Pokud požadovaný čítač výkonu není zahrnut v seznamu metrik, můžete ho přidat.

1. Pomocí tohoto příkazu prostředí PowerShell na místním serveru zjistěte, jaké čítače jsou na serveru k dispozici:

    `Get-Counter -ListSet *`

    (Viz [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Otevřete soubor ApplicationInsights.config.

   * Pokud jste během vývoje do aplikace přidali Application Insights, upravte soubor ApplicationInsights.config v projektu a pak jej znovu nasaďte na servery.
3. Upravit direktivu kolekcí výkonu:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Základní aplikace `ApplicationInsights.config`nemají , a proto výše uvedená metoda není platná pro ASP.NET základní aplikace.

Můžete zachytit jak standardní čítače, tak ty, které jste sami implementovali. `\Objects\Processes`je příkladem standardního čítače, který je k dispozici ve všech systémech Windows. `\Sales(photo)\# Items Sold`je příkladem vlastního čítače, který může být implementován ve webové službě.

Formát je `\Category(instance)\Counter"`, nebo pro kategorie, které nemají `\Category\Counter`instance, pouze .

`ReportAs`je vyžadováno pro názvy `[a-zA-Z()/-_ \.]+` čítačů, které se neshodují - to znamená, že obsahují znaky, které nejsou v následujících sadách: písmena, kulaté závorky, lomítko, pomlčka, podtržítko, mezera, tečka.

Pokud zadáte instanci, bude shromažďována jako dimenze "CounterInstanceName" hlášené metriky.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Shromažďování čítačů výkonu v kódu pro ASP.NET webových aplikací nebo aplikací základní konzoly .NET/.NET
Chcete-li shromažďovat čítače výkonu systému a odesílat je do application insights, můžete upravit fragment níže:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Nebo můžete udělat totéž s vlastnímetriky, které jste vytvořili:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Shromažďování čítačů výkonu v kódu pro ASP.NET základní webové aplikace

Upravte `ConfigureServices` metodu ve třídě, `Startup.cs` jak je uvedeno níže.

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
Přehledy čítačů výkonu můžete vyhledávat a zobrazovat v [Analytics](../../azure-monitor/app/analytics.md).

**Schéma performanceCounters** zveřejňuje `category`, `counter` název a `instance` název každého čítače výkonu.  V telemetrii pro každou aplikaci uvidíte pouze čítače pro tuto aplikaci. Chcete-li například zjistit, jaké čítače jsou k dispozici: 

![Čítače výkonu v analytice Application Insights](./media/performance-counters/analytics-performance-counters.png)

('Instance' zde odkazuje na instanci čítače výkonu, nikoli na roli nebo instanci počítače serveru. Název instance čítače čítače čítače čítače výkonu obvykle segmenty čítače, jako je například čas procesoru podle názvu procesu nebo aplikace.)

Chcete-li získat graf dostupné paměti za poslední období: 

![Časový graf paměti v analytice Application Insights](./media/performance-counters/analytics-available-memory.png)

Stejně jako ostatní telemetrie **performanceCounters** má také sloupec, `cloud_RoleInstance` který označuje identitu instance hostitelského serveru, na kterém je spuštěna vaše aplikace. Chcete-li například porovnat výkon aplikace na různých počítačích: 

![Výkon segmentovaný podle instance role v analytice Application Insights](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>počet ASP.NET a přehledů aplikací

*Jaký je rozdíl mezi metrikami Míra výjimek a Výjimky?*

* *Míra výjimek* je čítač výkonu systému. CLR spočítá všechny zpracované a neošetřené výjimky, které jsou vyvolány a vydělí součet v intervalu vzorkování délkou intervalu. Sada Application Insights SDK shromažďuje tento výsledek a odesílá jej na portál.

* *Výjimky* je počet sestav TrackException přijatých portálem v intervalu vzorkování grafu. Obsahuje pouze zpracované výjimky, kde jste ve svém kódu napsali volání TrackException, a nezahrnuje všechny [neošetřené výjimky](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Čítače výkonu pro aplikace spuštěné v Azure Web Apps

aplikace ASP.NET i ASP.NET Core nasazené do Azure Web Apps běží ve speciálním prostředí izolovaného prostoru. Toto prostředí neumožňuje přímý přístup k čítačům výkonu systému. Omezená podmnožina čítačů je však vystavena jako proměnné prostředí, jak je popsáno [zde](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK pro ASP.NET a ASP.NET Core shromažďuje čítače výkonu z Azure Web Apps z těchto speciálních proměnných prostředí. V tomto prostředí je k dispozici pouze podmnožina čítačů a úplný seznam naleznete [zde.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Čítače výkonu v aplikacích ASP.NET Core

Podpora čítačů výkonu v ASP.NET Core je omezená:

* [Sada SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.4.1 a novější shromažďovat čítače výkonu, pokud je aplikace spuštěna v Azure Web Apps (Windows).
* Sada SDK verze 2.7.1 a novější shromažďovat čítače `NETSTANDARD2.0` výkonu, pokud je aplikace spuštěna v systému Windows a cíle nebo novější.
* Pro aplikace zaměřené na rozhraní .NET Framework podporují všechny verze sady SDK čítače výkonu.
* Sada SDK Verze 2.8.0 a novější podporují čítač cpu/paměti v Linuxu. V Linuxu není podporován žádný jiný čítač. Doporučený způsob, jak získat čítače systému v Linuxu (a jiných prostředích mimo Windows) je pomocí [EventCounters](eventcounters.md)

## <a name="alerts"></a>Výstrahy
Stejně jako ostatní metriky můžete [nastavit výstrahu,](../../azure-monitor/app/alerts.md) která vás upozorní, pokud čítač výkonu nespadá mimo zadaný limit. Otevřete podokno Výstrahy a klikněte na Přidat výstrahu.

## <a name="next-steps"></a><a name="next"></a>Další kroky

* [Sledování závislostí](../../azure-monitor/app/asp-net-dependencies.md)
* [Sledování výjimek](../../azure-monitor/app/asp-net-exceptions.md)

