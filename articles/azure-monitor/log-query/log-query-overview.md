---
title: Analýza dat Log Analytics ve službě Azure Monitor | Dokumentace Microsoftu
description: Vyžadujete, aby hledání v protokolu se načíst žádná data ze služby Log Analytics.  Tento článek popisuje, jak nový protokol hledání se používají v Log Analytics a poskytuje koncepty, které je třeba porozumět před vytvořením jedné.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 4a2ea188a35b5157950f0d61f35a5f2a4a0e83d3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855695"
---
# <a name="analyze-log-analytics-data-in-azure-monitor"></a>Analýza dat Log Analytics ve službě Azure Monitor

Protokol data shromážděná službou Azure Monitor se ukládají v pracovním prostoru Log Analytics, která je založena na [Průzkumník dat Azure](/azure/data-explorer). Shromažďuje telemetrická data z různých zdrojů a používá [dotazovací jazyk v Průzkumníku dat](/azure/kusto/query) načíst a analyzovat data.

> [!NOTE]
> Log Analytics se dřív považovány za vlastní službu v Azure. Je nyní považovaných za součást služby Azure Monitor a se zaměřuje na úložiště a analýzu dat protokolu pomocí jeho dotazovací jazyk. Funkce, které bylo považováno za součást Log Analytics, jako je například Windows a Linux agentů pro shromažďování dat, zobrazení vizualizovat existující data, výstrahy a proaktivně vás upozorňují na problémy, nedošlo ke změně, ale jsou nyní považovány za součást služby Azure Monitor.



## <a name="log-queries"></a>Protokol dotazů

Budete potřebovat protokolu dotaz pro načtení všech dat ze služby Log Analytics.  Ať už se [analýza dat na portálu](../../log-analytics/log-analytics-log-search-portals.md), [konfigurace pravidla upozornění](../../monitoring-and-diagnostics/alert-metric.md) upozornit určitou podmínku, nebo při načítání dat pomocí [rozhraní API služby Log Analytics](https://dev.loganalytics.io/), můžete pomocí dotazu určit data, která chcete.  Tento článek popisuje, jak se používají dotazy protokolu v Log Analytics a poskytuje koncepty, které je třeba porozumět před vytvořením jedné.



## <a name="where-log-queries-are-used"></a>Použití dotazů na protokoly

Různé způsoby, že budete používat dotazy v Log Analytics patří následující:

- **Portály.** Můžete provádět interaktivní analýzu dat protokolu v [webu Azure portal](../../log-analytics/log-analytics-log-search-portals.md).  To umožňuje upravit dotaz a analyzujte výsledky v různých formátech a vizualizace.  
- **Pravidla upozornění.** [Pravidla výstrah](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktivně identifikovat problémy z dat ve vašem pracovním prostoru.  Každé pravidlo výstrahy je založen na prohledávání protokolu, který se spouští automaticky v pravidelných intervalech.  Výsledky jsou kontrolovány k určení Pokud by měla být vytvořena výstraha.
- **Řídicí panely.** Můžete připnout výsledky jakéhokoli dotazu do [řídicí panel Azure](../../azure-monitor/platform/dashboards.md) který vám umožní vizualizovat data protokolů a metrik společně a volitelně sdílet s ostatními uživateli Azure. 
- **Zobrazení.**  Možnost tvorby vizualizací dat mají být zahrnuty v řídicích panelech uživatele s [Návrhář zobrazení](../../azure-monitor/platform/view-designer.md).  Protokol dotazů, které poskytují data používá [dlaždice](../../azure-monitor/platform/view-designer-tiles.md) a [části vizualizace](../../azure-monitor/platform/view-designer-parts.md) v každém zobrazení.  
- **Export.**  Při importu dat z pracovního prostoru Log Analytics do aplikace Excel nebo [Power BI](../../log-analytics/log-analytics-powerbi.md), vytvořte dotaz protokolu pro definování dat k exportu.
- **PowerShell.** Skript prostředí PowerShell můžete spustit z příkazového řádku nebo runbooku Azure Automation, který používá [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) k načtení dat ze služby Log Analytics.  Tato rutina vyžaduje dotazu určit data, která mají načíst.
- **Rozhraní API služby log Analytics.**  [Protokolu v log Analytics rozhraní API pro vyhledávání](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) umožňuje jakéhokoli klienta REST API k načtení dat protokolu z pracovního prostoru.  Žádosti rozhraní API obsahuje dotaz, který je spuštěn proti Log Analytics k určení dat k načtení.

![Prohledávání protokolů](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>Napsat dotaz
Protokolovat Analytics používá [verze dotazovacího jazyka Průzkumník dat](../../log-analytics/query-language/get-started-queries.md) načíst a analyzovat data protokolů v mnoha různými způsoby.  Budete obvykle začínat základní dotazy a potom pokroku používat pokročilejší funkce rostoucí vašim požadavkům.

Základní struktura dotazu je zdrojová tabulka, za nímž následuje řadu operátory, které jsou oddělené znakem přesměrování `|`.  Můžete zřetězit dohromady víc operátorů Upřesnit tak data a provádět pokročilé funkce.

Předpokládejme například, že byste chtěli najít hlavní deset počítačů se většina chybové události za poslední den.

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

Nebo možná budete chtít najít počítače, u kterých nedošlo prezenční signál za poslední den.

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

A co spojnicový graf s využití procesoru v aplikaci pro každý počítač od posledního týdne?

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

Je vidět z ukázek rychlá, bez ohledu na to, jaká data, která pracujete s strukturu dotazu je podobné.  Je možné rozdělit do samostatné kroky, kde jedním příkazem Výsledná data se odesílají prostřednictvím kanálu k dalšímu příkazu.

Data můžete také dotazování napříč pracovních prostorů Log Analytics v rámci vašeho předplatného.

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-log-analytics-data-is-organized"></a>Uspořádání dat Log Analytics
Když vytvoříte dotaz, spustíte tak, že určíte, které tabulky obsahují data, která hledáte. Různé druhy dat jsou rozdělené do vyhrazené tabulek v každé [pracovní prostor Log Analytics](../../log-analytics/log-analytics-quick-create-workspace.md).  Dokumentaci k různým zdrojům dat obsahuje název datového typu, který vytvoří a popis každého z její vlastnosti.  Mnoho dotazů budete potřebovat jenom data z jedné tabulky, ale ostatní může používat širokou škálu možností zahrnout data z více tabulek.

Zatímco [Application Insights](../../application-insights/app-insights-overview.md) ukládá data aplikací, například požadavky, výjimky, trasování a využití v Log Analytics, tato data uložená v jiného oddílu než ostatní data protokolu. Přístup k těmto datům pomocí stejné dotazovací jazyk, ale musí používat [konzoly Application Insights](../../application-insights/app-insights-analytics.md) nebo [REST API pro Application Insights](https://dev.applicationinsights.io/) k němu přistupovat. Můžete použít [dotazy napříč prostředky](../../log-analytics/log-analytics-cross-workspace-search.md) kombinovat data Application Insights s ostatními daty v Log Analytics.


![Tabulky](media/log-query-overview/queries-tables.png)







## <a name="next-steps"></a>Další postup

- Další informace o [prohledávání protokolů portály, které použijete k vytvoření a úprava](../../log-analytics/log-analytics-log-search-portals.md).
- Podívejte se [kurz o psaní dotazů](../../log-analytics/query-language/get-started-queries.md) pomocí nového dotazovacího jazyka.
