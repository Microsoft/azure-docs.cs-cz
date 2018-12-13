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
ms.date: 10/11/2016
ms.author: mbullwin
ms.openlocfilehash: bcfb3a52793ba0daca980564d5d2248629b5caf4
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323008"
---
# <a name="system-performance-counters-in-application-insights"></a>Čítače výkonu systému ve službě Application Insights
Windows poskytuje širokou škálu [čítače výkonu](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters) například vytížení procesoru, paměti, disku a využití sítě. Můžete také definovat své vlastní. [Application Insights](app-insights-overview.md) můžete zobrazit tyto čítače výkonu, pokud je aplikace spuštěna v rámci služby IIS na místního hostitele nebo virtuální počítač, ke kterému máte přístup pro správu. Grafy označují prostředky dostupné pro vaše živé aplikace a může pomoct identifikovat nevyváženého zatížení mezi instancemi serveru.

Čítače výkonu se zobrazí v okně servery, který obsahuje tabulku tohoto segmentů podle instance serveru.

![Čítače výkonu v Application Insights](./media/app-insights-performance-counters/counters-by-server-instance.png)

(Čítače výkonu nejsou k dispozici pro Azure Web Apps. Ale můžete [odesílání Azure Diagnostics do Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).)

## <a name="view-counters"></a>Zobrazení čítačů
Ukazuje blade servery výchozí sadu čítačů výkonu. 

Pokud chcete zobrazit další čítače, úpravy grafů v okně servery nebo otevřete nové [Průzkumníka metrik](app-insights-metrics-explorer.md) okno a přidejte nový graf. 

Dostupné čítače jsou uvedené jako metriky, když upravujete grafu.

![Čítače výkonu v Application Insights](./media/app-insights-performance-counters/choose-performance-counters.png)

Pokud chcete zobrazit všechny nejužitečnější grafy na jednom místě, vytvořit [řídicí panel](app-insights-dashboards.md) a připínat je na to.

## <a name="add-counters"></a>Přidání čítačů
Pokud čítač výkonu, které chcete nezobrazuje v seznamu metrik, je to, protože Application Insights SDK se shromažďují ve vašem webovém serveru. Můžete nakonfigurovat ji tak.

1. Zjistěte, jaké čítače jsou k dispozici na serveru pomocí tohoto příkazu prostředí PowerShell na serveru:
   
    `Get-Counter -ListSet *`
   
    (Viz [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Otevřete soubor ApplicationInsights.config.
   
   * Pokud jste přidali Application Insights do vaší aplikace během vývoje, upravit soubor ApplicationInsights.config v projektu a pak znovu nasadit na servery.
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

Můžete zaznamenat standardní čítače i ty že sami sebe implementovali. `\Objects\Processes` Příkladem standardní čítač je k dispozici na všech systémech Windows. `\Sales(photo)\# Items Sold` je příkladem vlastní čítače, která může být implementována ve webové službě. 

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
Můžete vyhledat a zobrazit sestavy čítačů výkonu v [Analytics](app-insights-analytics.md).

**Čítače výkonu** zpřístupňuje schématu `category`, `counter` název, a `instance` název jednotlivých čítačů výkonu.  V telemetrii pro každou aplikaci zobrazí se vám jenom čítače pro tuto aplikaci. Například pokud chcete zobrazit jsou k dispozici co čítače: 

![Čítače výkonu ve službě Application Insights analytics](./media/app-insights-performance-counters/analytics-performance-counters.png)

('Instance' zde vztahuje na instance čítače výkonu není instance počítače roli nebo server. Název instance čítače výkonu obvykle segmenty čítače, jako například čas procesoru podle názvu procesu nebo aplikace.)

Pokud chcete získat grafu dostupné paměti za poslední období: 

![Paměť promítnu ve službě Application Insights analytics](./media/app-insights-performance-counters/analytics-available-memory.png)

Další telemetrická data, jako jsou **čítače výkonu** má také sloupec `cloud_RoleInstance` , která určuje identitu instance hostitelského serveru, na kterém je spuštěná aplikace. Chcete-li například porovnat výkon vaší aplikace na různých počítačích: 

![Výkon segmentované podle instance role ve službě Application Insights analytics](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET a počty Application Insights
*Jaký je rozdíl mezi frekvence výjimek a metriky výjimky?*

* *Frekvence výjimek* je čítač výkonu systému. CLR vrátí počet všech zpracovaných a nezpracovaných výjimek, které jsou vyvolány a vydělí celkový počet v intervalu vzorkování délku intervalu. Sadu SDK Application Insights shromažďuje tento výsledek a odešle ji na portál.
* *Výjimky* je počet sestav TrackException přijatých na portálu v intervalu vzorkování grafu. Zahrnuje pouze zpracované výjimky, ve kterém jste napsali TrackException volání v kódu a nezahrnuje všechny [neošetřené výjimky](app-insights-asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Čítače výkonu aplikace Asp.Net Core
Čítače výkonu jsou podporovány pouze v případě, že aplikace cílí na úplné rozhraní .NET Framework. Neexistuje žádná možnost získat čítače výkonu pro.Net Core aplikací.

## <a name="alerts"></a>Výstrahy
Stejně jako jiné metriky můžete [nastavit výstrahu](app-insights-alerts.md) upozornit, pokud čítač výkonu přejde mimo omezení zadáte. Otevře se okno oznámení a klikněte na tlačítko Přidat oznámení.

## <a name="next"></a>Další kroky
* [Sledování závislostí](app-insights-asp-net-dependencies.md)
* [Sledování výjimek](app-insights-asp-net-exceptions.md)

