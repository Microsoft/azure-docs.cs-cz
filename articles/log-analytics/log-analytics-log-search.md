---
title: "Hledání přihlásit k Azure Log Analytics | Microsoft Docs"
description: "Vyžadujete, aby hledání protokolů načíst všechna data z analýzy protokolů.  Tento článek popisuje, jak nový protokol hledání se používají v analýzy protokolů a poskytuje koncepty, které je třeba porozumět před vytvořením jeden."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 5f040d1480433ccf4c0b2b22c0cf1e25a7151d74
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2017
---
# <a name="understanding-log-searches-in-log-analytics"></a>Principy protokolu hledání v analýzy protokolů

Vyžadujete, aby hledání protokolů načíst všechna data z analýzy protokolů.  Jestli při analýze dat na portálu, konfigurace pravidlo výstrahy pro informováni o určitá podmínka nebo načítání dat pomocí rozhraní API Log Analytics, budete pomocí vyhledávání protokolu zadat data, která chcete.  Tento článek popisuje, jak se používají protokol hledání v analýzy protokolů a poskytuje koncepty, které je třeba porozumět před vytvořením jeden. Najdete v článku [další kroky](#next-steps) části Podrobné informace o vytváření a úpravy protokolu hledání a odkazy na dotazovací jazyk.

## <a name="where-log-searches-are-used"></a>Použití protokolu hledání

Různé způsoby, že použijete protokolu hledání v analýzy protokolů zahrnují následující:

- **Portálů.** Interaktivní analýzu dat můžete provádět v úložišti na portálu Azure nebo [Advanced Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587).  To umožňuje upravit dotaz a analyzujte výsledky v různých formátech a vizualizací.  Většinu dotazů, které vytvoříte, se spustí v jednom z portálů a pak zkopíruje Jakmile ověříte, že funguje podle očekávání.
- **Pravidla výstrah.** [Pravidla výstrah](log-analytics-alerts.md) aktivně identifikovat problémy z dat v pracovním prostoru.  Každé pravidlo výstrahy je založen na protokolu vyhledávání, který se automaticky spouští v pravidelných intervalech.  Výsledky jsou prozkoumá a určí, jestliže se vytvoří výstraha.
- **Zobrazení.**  Můžete vytvořit vizualizaci dat, které mají být zahrnuty v řídicích panelech uživatele s [Návrhář zobrazení](log-analytics-view-designer.md).  Protokol hledání poskytují data používá [dlaždice](log-analytics-view-designer-tiles.md) a [vizualizace částí](log-analytics-view-designer-parts.md) v každém zobrazení.  Podrobnostem z vizualizace části na stránku hledání protokolů k další analýze na data.
- **Export.**  Při exportu dat z pracovního prostoru analýzy protokolů do aplikace Excel nebo [Power BI](log-analytics-powerbi.md), vytvořte protokol hledání, zadejte data pro export.
- **Prostředí PowerShell.** Skript prostředí PowerShell můžete spustit z příkazového řádku nebo runbooku automatizace Azure, který používá [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) k načtení dat z analýzy protokolů.  Tento příkaz cmdlet vyžaduje dotazu určit data pro získání.
- **Log Analytics API.**  [Analýzy protokolů protokolu rozhraní API služby search](log-analytics-log-search-api.md) umožňuje libovolného klienta REST API k načtení dat z pracovního prostoru.  Rozhraní API požadavek obsahuje dotaz, který spustí pro analýzy protokolů k určení dat pro načtení.

![Protokol hledání](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Uspořádání dat analýzy protokolů
Když vytvoříte dotaz, je třeba spustit tak, že určíte, které tabulky mají data, která hledáte. Každý [zdroj dat](log-analytics-data-sources.md) a [řešení](../operations-management-suite/operations-management-suite-solutions.md) ukládá data do vyhrazené tabulky v pracovním prostoru analýzy protokolů.  Dokumentace pro každý zdroj dat a řešení obsahuje název datového typu, který vytvoří a popis každého z jeho vlastnosti.  Mnoho dotazů bude potřebovat pouze data z jedné tabulky, ale jiné může pomocí různých možností zahrnout data z více tabulek.

![Tabulky](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>Zápis dotazu
Základem protokolu hledání v analýzy protokolů je [rozsáhlé dotazovací jazyk](https://docs.loganalytics.io/) které vám umožní načíst a analyzovat data z úložiště v mnoha různými způsoby.  Tento stejný jazyk dotazu se používá pro [Application Insights](../application-insights/app-insights-analytics.md).  Naučit, jak napsat dotaz je velmi důležité vytváření protokolu hledání v analýzy protokolů.  Budete obvykle začínat základní dotazy a pak průběhu použít pokročilejší funkce jsou složitější vašim požadavkům.

Základní struktura dotazu je zdrojová tabulka, za nímž následuje řadu operátory, které jsou odděleny svislou čarou `|`.  Můžete společně zřetězit více operátorů Upřesnit data a provádět pokročilé funkce.

Předpokládejme například, že chcete najít top deset počítače s většina chybové události přes poslední den.

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

Nebo možná chcete najít počítače, které nebyly měl prezenční signál v poslední den.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

Co spojnicový graf s využití procesoru pro každý počítač, z poslední týden?

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

Je vidět na tyto ukázky rychlý, bez ohledu na druh data, která pracujete s strukturu dotazu je podobný.  Lze ho rozdělit do různých kroků, kdy se odesílají Výsledná data z jednoho příkazu prostřednictvím kanálu na další příkaz.

Můžete také dotazování dat mezi pracovních prostorů analýzy protokolů v rámci vašeho předplatného.

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 


Úplnou dokumentaci k Azure Log Analytics dotazovací jazyk, včetně kurzy a referenční dokumentace jazyka najdete v článku [Azure Log Analytics dotazu jazyka dokumentaci](https://docs.loganalytics.io/).

## <a name="next-steps"></a>Další kroky

- Další informace o [portály, které použijete k vytvoření a úprava protokolu hledání](log-analytics-log-search-portals.md).
- Podívejte se [kurz na zápis dotazů](log-analytics-tutorial-viewdata.md) pomocí nového dotazu jazyka.
