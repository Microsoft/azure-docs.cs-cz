---
title: Analyzuje data protokolů ve službě Azure Monitor | Dokumentace Microsoftu
description: Vyžadujete protokol dotaz pro načtení data protokolů ze služby Azure Monitor.  Tento článek popisuje, jak nový protokol dotazů se používají ve službě Azure Monitor a poskytuje koncepty, které je třeba porozumět před vytvořením jedné.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: bwren
ms.openlocfilehash: 9aff955a2ae0f40785036c2fee22804785e6526a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002265"
---
# <a name="analyze-log-data-in-azure-monitor"></a>Analyzuje data protokolů ve službě Azure Monitor

Protokol data shromážděná službou Azure Monitor se ukládají v pracovním prostoru Log Analytics, která je založena na [Průzkumník dat Azure](/azure/data-explorer). Shromažďuje telemetrická data z různých zdrojů a používá [dotazovací jazyk v Průzkumníku dat](/azure/kusto/query) načíst a analyzovat data.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="log-queries"></a>Protokol dotazů

Vyžadujete protokol dotaz pro načtení všechna data protokolů ze služby Azure Monitor.  Ať už se [analýza dat na portálu](portals.md), [konfigurace pravidla upozornění](../platform/alerts-metric.md) upozornit určitou podmínku, nebo při načítání dat pomocí [rozhraní API služby Azure Monitor protokoly](https://dev.loganalytics.io/) , se pomocí dotazu určit data, která chcete.  Tento článek popisuje použití dotazů na protokoly ve službě Azure Monitor a poskytuje koncepty, které je třeba porozumět před vytvořením jedné.



## <a name="where-log-queries-are-used"></a>Použití dotazů na protokoly

Různé způsoby, že budete používat protokol dotazů ve službě Azure Monitor, patří:

- **Portál.** Můžete provádět interaktivní analýzu dat protokolu v [webu Azure portal](portals.md).  To umožňuje upravit dotaz a analyzujte výsledky v různých formátech a vizualizace.  
- **Pravidla upozornění.** [Pravidla výstrah](../platform/alerts-overview.md) proaktivně identifikovat problémy z dat ve vašem pracovním prostoru.  Každé pravidlo výstrahy je založen na prohledávání protokolu, který se spouští automaticky v pravidelných intervalech.  Výsledky jsou kontrolovány k určení Pokud by měla být vytvořena výstraha.
- **Řídicí panely.** Můžete připnout výsledky jakéhokoli dotazu do [řídicí panel Azure](../learn/tutorial-logs-dashboards.md) který vám umožní vizualizovat data protokolů a metrik společně a volitelně sdílet s ostatními uživateli Azure. 
- **Zobrazení.**  Možnost tvorby vizualizací dat mají být zahrnuty v řídicích panelech uživatele s [Návrhář zobrazení](../platform/view-designer.md).  Protokol dotazů, které poskytují data používá [dlaždice](../platform/view-designer-tiles.md) a [části vizualizace](../platform/view-designer-parts.md) v každém zobrazení.  
- **Export.**  Když importujete data protokolů ze služby Azure Monitor do aplikace Excel nebo [Power BI](../platform/powerbi.md), vytvořte dotaz protokolu pro definování dat k exportu.
- **PowerShell.** Skript prostředí PowerShell můžete spustit z příkazového řádku nebo runbooku Azure Automation, který používá [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) načíst data protokolů ze služby Azure Monitor.  Tato rutina vyžaduje dotazu určit data, která mají načíst.
- **Rozhraní API protokoly Azure monitoru.**  [Rozhraní API služby Azure Monitor protokoly](../platform/alerts-overview.md) umožňuje jakéhokoli klienta REST API k načtení dat protokolu z pracovního prostoru.  Žádosti rozhraní API obsahuje dotaz, který je spuštěn proti Azure Monitor k určení dat k načtení.

![Prohledávání protokolů](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>Napsat dotaz
Azure Monitor používá [verze dotazovacího jazyka Průzkumník dat](get-started-queries.md) načíst a analyzovat data protokolů v mnoha různými způsoby.  Budete obvykle začínat základní dotazy a potom pokroku používat pokročilejší funkce rostoucí vašim požadavkům.

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

## <a name="how-azure-monitor-log-data-is-organized"></a>Jakým způsobem je organizována data protokolů Azure Monitor
Když vytvoříte dotaz, spustíte tak, že určíte, které tabulky obsahují data, která hledáte. Různé druhy dat jsou rozdělené do vyhrazené tabulek v každé [pracovní prostor Log Analytics](../learn/quick-create-workspace.md).  Dokumentaci k různým zdrojům dat obsahuje název datového typu, který vytvoří a popis každého z její vlastnosti.  Mnoho dotazů budete potřebovat jenom data z jedné tabulky, ale ostatní může používat širokou škálu možností zahrnout data z více tabulek.

Zatímco [Application Insights](../app/app-insights-overview.md) ukládá data aplikací, například požadavky, výjimky, trasování a využití v protokolech Azure Monitor, tato data uložená v jiného oddílu než ostatní data protokolu. Přístup k těmto datům pomocí stejné dotazovací jazyk, ale musí používat [konzoly Application Insights](../app/analytics.md) nebo [REST API pro Application Insights](https://dev.applicationinsights.io/) k němu přistupovat. Můžete použít [dotazy napříč prostředky](../log-query/cross-workspace-query.md) kombinovat data Application Insights s ostatními daty protokolu ve službě Azure Monitor.


![Tabulky](media/log-query-overview/queries-tables.png)




## <a name="next-steps"></a>Další postup
- Další informace o použití [protokolu analytics můžete vytvářet a upravovat prohledávání protokolů](../log-query/portals.md).
- Podívejte se [kurz o psaní dotazů](../log-query/get-started-queries.md) pomocí nového dotazovacího jazyka.
