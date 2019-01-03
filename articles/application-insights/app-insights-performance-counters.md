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
ms.openlocfilehash: 3131afb31fd08903bb349f86634d2b9e6449c59e
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752652"
---
# <a name="system-performance-counters-in-application-insights"></a>Čítače výkonu systému ve službě Application Insights

Windows poskytuje širokou škálu [čítače výkonu](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) například vytížení procesoru, paměti, disku a využití sítě. Můžete také definovat vlastní čítače výkonu. Tak dlouho, dokud vaše aplikace běží v rámci služby IIS na místního hostitele nebo virtuální počítač, ke kterému máte přístup pro správu.

## <a name="view-counters"></a>Zobrazení čítačů

V podokně metriky se zobrazí výchozí sadu čítačů výkonu.

![Čítače výkonu v Application Insights](./media/app-insights-performance-counters/performance-counters.png)

Aktuální výchozí čítače, které se shromažďují pro webové aplikace .NET jsou:

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

Pokud chcete zobrazit všechny nejužitečnější grafy na jednom místě, vytvořit [řídicí panel](../azure-monitor/app/app-insights-dashboards.md) a připínat je na to.

## <a name="add-counters"></a>Přidání čítačů

Pokud čítač výkonu, které chcete nejsou zahrnuty v seznamu metrik, můžete ho přidat.

1. Zjistěte, jaké čítače jsou k dispozici na serveru pomocí tohoto příkazu prostředí PowerShell na místním serveru:
   
    `Get-Counter -ListSet *`
   
    (Viz [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Otevřete soubor ApplicationInsights.config.
   
   * Pokud jste přidali Application Insights do vaší aplikace během vývoje, upravit soubor ApplicationInsights.config v projektu a pak ho znovu nasadíte na serverech.
   * Pokud monitorování stavu jste použili k instrumentaci webové aplikace za běhu, vyhledejte soubor ApplicationInsights.config v kořenovém adresáři aplikace ve službě IIS. Aktualizujte ji, existuje v každé instanci serveru.
3. Upravte směrnice kolekce výkonu:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Můžete zaznamenat standardní čítače i ty že jsme implementovali sami. `\Objects\Processes` je příkladem standardní čítač, který je k dispozici na všech systémech Windows. `\Sales(photo)\# Items Sold` je příkladem vlastní čítače, která může být implementována ve webové službě. 

Formát je `\Category(instance)\Counter"`, nebo kategorií, které nemají instancí, právě `\Category\Counter`.

`ReportAs` je vyžadován pro názvy čítačů, které se neshodují s `[a-zA-Z()/-_ \.]+` – to znamená, které obsahují znaky, které nejsou v následujících sadách: písmena, kulaté závorky, lomítkem, spojovník, podtržítko, mezery, tečky.

Pokud chcete zadat instanci, nebudou se shromažďovat jako dimenze "CounterInstanceName" ohlášené metriky.

### <a name="collecting-performance-counters-in-code"></a>Shromažďování čítačů výkonu v kódu
Pokud chcete shromáždit čítače výkonu systému a posílat do Application Insights, můžete přizpůsobit následující fragment:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
Nebo můžete provést totéž s vlastní metriky, které jste vytvořili:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Čítače výkonu v Analytics
Můžete vyhledat a zobrazit sestavy čítačů výkonu v [Analytics](../azure-monitor/app/analytics.md).

**Čítače výkonu** zpřístupňuje schématu `category`, `counter` název, a `instance` název jednotlivých čítačů výkonu.  V telemetrii pro každou aplikaci zobrazí se vám jenom čítače pro tuto aplikaci. Například pokud chcete zobrazit jsou k dispozici co čítače: 

![Čítače výkonu ve službě Application Insights analytics](./media/app-insights-performance-counters/analytics-performance-counters.png)

('Instance' tady odkazuje na instanci čítače výkonu, není role nebo instance počítače serveru. Název instance čítače výkonu obvykle segmenty čítače, jako například čas procesoru podle názvu procesu nebo aplikace.)

Pokud chcete získat grafu dostupné paměti za poslední období: 

![Paměť promítnu ve službě Application Insights analytics](./media/app-insights-performance-counters/analytics-available-memory.png)

Další telemetrická data, jako jsou **čítače výkonu** má také sloupec `cloud_RoleInstance` , která určuje identitu instance hostitelského serveru, na kterém je spuštěná aplikace. Chcete-li například porovnat výkon vaší aplikace na různých počítačích: 

![Výkon segmentované podle instance role ve službě Application Insights analytics](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET a počty Application Insights
*Jaký je rozdíl mezi frekvence výjimek a metriky výjimky?*

* *Frekvence výjimek* je čítač výkonu systému. CLR vrátí počet všech zpracovaných a nezpracovaných výjimek, které jsou vyvolány a vydělí celkový počet v intervalu vzorkování délku intervalu. Sadu SDK Application Insights shromažďuje tento výsledek a odešle ji na portál.

* *Výjimky* je počet sestav TrackException přijatých na portálu v intervalu vzorkování grafu. Zahrnuje pouze zpracované výjimky, ve kterém jste napsali TrackException volání v kódu a nezahrnuje všechny [neošetřené výjimky](../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Čítače výkonu aplikace ASP.Net Core
Čítače výkonu jsou podporovány pouze v případě, že aplikace cílí na úplné rozhraní .NET Framework. Neexistuje žádná možnost získat čítače výkonu pro.Net Core aplikací.

## <a name="alerts"></a>Výstrahy
Stejně jako jiné metriky můžete [nastavit výstrahu](../azure-monitor/app/alerts.md) upozornit, pokud čítač výkonu přejde mimo omezení zadáte. Otevřete podokno oznámení a klikněte na tlačítko Přidat oznámení.

## <a name="next"></a>Další kroky
* [Sledování závislostí](../azure-monitor/app/asp-net-dependencies.md)
* [Sledování výjimek](../azure-monitor/app/asp-net-exceptions.md)

