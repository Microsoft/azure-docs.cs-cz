---
title: Čítače výkonu v Application Insights | Microsoft Docs
description: Monitorovat systémové a vlastní čítače výkonu rozhraní .NET v Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 423e17ef2b44286c28b464836075284929d8644c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031357"
---
# <a name="system-performance-counters-in-application-insights"></a>Čítače výkonu systému v Application Insights

Windows nabízí širokou škálu [čítačů výkonu](/windows/desktop/perfctrs/about-performance-counters), například pro vytížení procesoru nebo využití paměti, disku a sítě. Můžete také definovat vlastní čítače výkonu. Shromažďování čítačů výkonu je podporováno, pokud je aplikace spuštěna v rámci služby IIS na místním hostiteli nebo virtuální počítač, ke kterému máte přístup pro správu. I když aplikace spuštěné jako Azure Web Apps nemají přímý přístup k čítačům výkonu, v Application Insights shromáždí podmnožinu dostupných čítačů.

## <a name="view-counters"></a>Zobrazit čítače

V podokně metriky se zobrazuje výchozí sada čítačů výkonu.

![Čítače výkonu hlášené v Application Insights](./media/performance-counters/performance-counters.png)

Aktuální výchozí čítače, které mají být shromažďovány pro webové aplikace v ASP.NET, jsou:
- % \\ Času procesoru procesu
- % \\ Času procesoru procesu – normalizace
- \\Bajty dostupné paměti
- ASP.NET požadavky za sekundu
- Vyvolané výjimky .NET CLR za sekundu
- Doba spuštění ASP.NET ApplicationsRequest
- \\Nesdílené bajty procesu
- \\Vstupně-výstupní operace procesu – bajty/s
- Žádosti aplikací ASP.NET \\ ve frontě aplikací
- Procesor (_Total) \\ % času procesoru

Aktuální výchozí čítače, které jsou nakonfigurované pro ASP.NET Core webových aplikací, jsou:
- % \\ Času procesoru procesu
- % \\ Času procesoru procesu – normalizace
- \\Bajty dostupné paměti
- \\Nesdílené bajty procesu
- \\Vstupně-výstupní operace procesu – bajty/s
- Procesor (_Total) \\ % času procesoru

## <a name="add-counters"></a>Přidání čítačů

Pokud požadovaný čítač výkonu není zahrnutý v seznamu metrik, můžete ho přidat.

1. Zjistěte, jaké čítače jsou k dispozici na serveru pomocí tohoto příkazu PowerShellu na místním serveru:

    `Get-Counter -ListSet *`

    (Viz [`Get-Counter`](/powershell/module/microsoft.powershell.diagnostics/get-counter) .)
2. Otevřete ApplicationInsights.config.

   * Pokud jste během vývoje přidali Application Insights do vaší aplikace, upravte ApplicationInsights.config v projektu a pak ji znovu nasaďte na své servery.
3. Úprava direktivy sběrače výkonu:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> Aplikace ASP.NET Core neobsahují `ApplicationInsights.config` , takže výše uvedená metoda není platná pro ASP.NET Core aplikace.

Můžete zachytit standardní počítadla i ty, které jste implementovali sami. `\Objects\Processes` je příkladem standardního čítače, který je k dispozici ve všech systémech Windows. `\Sales(photo)\# Items Sold` je příkladem vlastního čítače, který může být implementován ve webové službě.

Formát je `\Category(instance)\Counter"` nebo pro kategorie, které nemají instance, stačí `\Category\Counter` .

`ReportAs` je vyžadován u názvů čítačů, které se neshodují `[a-zA-Z()/-_ \.]+` – to znamená, že obsahují znaky, které nejsou v následujících sadách: písmena, kulaté závorky, lomítko, pomlčka, podtržítko, mezera, tečka.

Pokud zadáte instanci, bude shromažďována jako dimenze "CounterInstanceName" hlášené metriky.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Shromažďování čítačů výkonu v kódu pro webové aplikace ASP.NET nebo konzolové aplikace .NET/.NET Core
Chcete-li shromáždit čítače výkonu systému a odeslat je do Application Insights, můžete následující fragment kódu upravit:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Můžete také provést stejnou věc s vlastními metrikami, které jste vytvořili:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Shromažďování čítačů výkonu v kódu pro ASP.NET Core webové aplikace

Upravte `ConfigureServices` metodu ve `Startup.cs` třídě tak, jak je uvedeno níže.

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

## <a name="performance-counters-in-analytics"></a>Čítače výkonu v analýzách
Sestavy čítače výkonu můžete vyhledávat a zobrazovat v [analýzách](../logs/log-query-overview.md).

Schéma **čítače výkonu** zpřístupňuje `category` , `counter` název a `instance` název každého čítače výkonu.  V telemetrii pro každou aplikaci uvidíte pouze čítače této aplikace. Například, chcete-li zjistit, jaké čítače jsou k dispozici: 

![Čítače výkonu v Application Insights Analytics](./media/performance-counters/analytics-performance-counters.png)

(' Instance ' zde odkazuje na instanci čítače výkonu, nikoli na roli nebo instanci počítače serveru. Název instance čítače výkonu obvykle segmentuje čítače, jako je například čas procesoru, podle názvu procesu nebo aplikace.)

Získání grafu dostupné paměti za poslední období: 

![Timechart paměti v analýzách Application Insights](./media/performance-counters/analytics-available-memory.png)

Podobně jako u jiné telemetrie má **čítače výkonu** také sloupec `cloud_RoleInstance` , který označuje identitu instance hostitelského serveru, na které je vaše aplikace spuštěná. Pokud například chcete porovnat výkon aplikace v různých počítačích: 

![Výkon segmentované instance role v Application Insights Analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Počty ASP.NET a Application Insights

*Jaký je rozdíl mezi metrikou četnosti výjimek a výjimkami?*

* *Míra výjimek* je čítač výkonu systému. CLR počítá všechny zpracovávané a neošetřené výjimky, které jsou vyvolány, a vydělí celkový součet v intervalu vzorkování o délku intervalu. Sada SDK Application Insights shromažďuje tento výsledek a odesílá je na portál.

* *Výjimky* jsou počtem sestav TrackException přijatých portálem v intervalu vzorkování grafu. Obsahuje pouze ošetřené výjimky, u kterých jste napsali volání TrackException v kódu a nezahrnuje všechny [neošetřené výjimky](./asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Čítače výkonu pro aplikace běžící v Azure Web Apps

Aplikace ASP.NET i ASP.NET Core nasazené do Azure Web Apps běžet ve speciálním prostředí izolovaného prostoru (sandbox). Toto prostředí neumožňuje přímý přístup k čítačům výkonu systému. Omezená podmnožina čítačů je však k dispozici jako proměnné prostředí, jak je popsáno [zde](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Sada Application Insights SDK pro ASP.NET a ASP.NET Core shromažďuje čítače výkonu z Azure Web Apps z těchto speciálních proměnných prostředí. V tomto prostředí je dostupná jenom podmnožina čítačů. úplný seznam najdete [tady.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Čítače výkonu v aplikacích ASP.NET Core

Podpora čítačů výkonu v ASP.NET Core je omezená:

* [Sady SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verze 2.4.1 a novější shromažďují čítače výkonu, pokud aplikace běží v Azure Web Apps (Windows).
* Verze sady SDK 2.7.1 a novější shromažďují čítače výkonu, pokud je aplikace spuštěná ve Windows a cílících `NETSTANDARD2.0` nebo novějších.
* Pro aplikace cílené na .NET Framework všechny verze sady SDK podporují čítače výkonu.
* Verze sady SDK 2.8.0 a novější podporují čítač procesorů a paměti v systému Linux. V systému Linux není podporován žádný jiný čítač. Doporučený způsob, jak získat systémové čítače v systémech Linux (a dalších prostředích mimo Windows), je použití [EventCounters](eventcounters.md)

## <a name="alerts"></a>Výstrahy
Podobně jako u jiných metrik můžete [nastavit výstrahu](../alerts/alerts-log.md) , která vás upozorní, pokud počítadlo výkonu dosáhne mimo zadaný limit. Otevřete podokno výstrahy a klikněte na Přidat výstrahu.

## <a name="next-steps"></a><a name="next"></a>Další kroky

* [Sledování závislostí](./asp-net-dependencies.md)
* [Sledování výjimek](./asp-net-exceptions.md)

