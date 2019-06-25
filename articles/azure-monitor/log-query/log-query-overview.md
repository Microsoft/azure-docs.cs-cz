---
title: Přehled protokolu dotazů ve službě Azure Monitor | Dokumentace Microsoftu
description: Odpovědi běžné dotazy související s dotazy protokolů a vám pomůže začít v jejich používání.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: b395b7bccbf93b56e84d5e7b5a4ed7355eaca335
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296347"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Přehled protokolu dotazů ve službě Azure Monitor
Protokol dotazů můžete plně využít výhody na data shromážděná v [protokoly Azure monitoru](../platform/data-platform-logs.md). Výkonný dotazovací jazyk umožňuje připojit data z více tabulek, agregovat rozsáhlých datových sad a provádět složité operace s minimem kódu. Prakticky libovolný dotaz může zodpovědět a provádět analýzy byla shromážděna podpůrné data, a pokud víte, jak vytvořit dotaz správný.

Některé funkce ve službě Azure Monitor, jako [insights](../insights/insights-overview.md) a [řešení](../insights/solutions-inventory.md) zpracovat data protokolu bez vystavení je základní dotazy. Plně využívat další funkce služby Azure Monitor, měli byste porozumět, jak jsou dotazy konstruovány a jak je můžete využít k interaktivní analýze dat protokolů Azure Monitor.

V tomto článku použijte jako výchozí bod pro získání informací o dotazech protokolu ve službě Azure Monitor. Odpovědi na běžné dotazy a obsahuje odkazy na další dokumentaci, která poskytuje další podrobnosti a lekcemi.

## <a name="how-can-i-learn-how-to-write-queries"></a>Jak lze zjistit, jak psát dotazy?
Pokud chcete přejít přímo do věcí, může začínat v následujících kurzech:

- [Začínáme se službou Log Analytics ve službě Azure Monitor](get-started-portal.md).
- [Začínáme s dotazy protokolu ve službě Azure Monitor](get-started-queries.md).

Jakmile máte základy dolů, provede několik lekce s použitím vlastních dat nebo dat v našem ukázkovém prostředí od: 

- [Práce s řetězci v dotazech protokolu Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Jaký jazyk protokolovat použijte dotazy?
Protokoly služby Azure Monitor je založen na [Průzkumník dat Azure](/azure/data-explorer), a dotazů na protokoly se zapíšou ve stejném jazyce dotaz Kusto (KQL). Toto je bohatou jazykovou navržené tak, aby se snadno čte a Autor a které by měl být schopni spustit pomocí s minimálními.

Zobrazit [dokumentaci KQL Průzkumník dat Azure](/azure/kusto/query) úplnou dokumentaci k KQL a odkaz na různé funkce, které jsou k dispozici.<br>
Zobrazit [Začínáme s dotazy protokolu ve službě Azure Monitor](get-started-queries.md) rychlý návod jazyka pomocí dat z monitorování protokolů Azure.
Zobrazit [Azure Monitor protokolu dotazu jazyka rozdíly](data-explorer-difference.md) pro drobné rozdíly ve verzi KQL používat Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Jaká data jsou k dispozici pro přihlášení dotazy?
Všechna data shromážděná v protokolech sledování Azure je možné načíst a analyzovat v dotazech protokolu. Různých zdrojů dat bude zapisovat svá data do různých tabulek, ale může obsahovat více tabulek v jediném dotazu pro analýzu dat napříč více zdrojů. Když vytvoříte dotaz, spustíte tak, že určíte, které tabulky obsahují data, která hledáte, proto byste měli mít alespoň základní znalosti o jak strukturovaná data v protokolech monitorování Azure.

V tématu [zdroje protokolů monitorování Azure](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)pro seznam různých datových zdrojů, který naplnit protokoly Azure monitoru.<br>
Zobrazit [struktura protokoly sledování Azure](logs-structure.md) pro vysvětlení, jak strukturovaná data.

## <a name="what-does-a-log-query-look-like"></a>Jak vypadá dotaz protokolu?
Dotaz může být stejně snadné jako název jedné tabulky pro načtení všech záznamů z dané tabulky:

```Kusto
Syslog
```

Nebo může filtrovat konkrétní záznamy, jejich souhrn a vizualizovat výsledky v grafu:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Pro složitější analýzu může načtení dat z více tabulek pomocí spojení analyzovat výsledky dohromady.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
I v případě, že nejste obeznámeni s KQL, byste měli mít alespoň zjistit základní logiku, která je používána ve tyto dotazy. Spuštění s názvem tabulky a pak přidejte více příkazů filtrovat a zpracovat data. Dotaz může používat libovolný počet příkazů, a můžete psát složitější dotazy, jak jste se seznámili s různé příkazy KQL, která je k dispozici.

Zobrazit [Začínáme s dotazy protokolu ve službě Azure Monitor](get-started-queries.md) kurz týkající se dotazů na protokoly, které zavádí jazyk a běžné funkce.<br>


## <a name="what-is-log-analytics"></a>Co je služba Log Analytics?
Log Analytics je primárním nástrojem na webu Azure Portal pro psaní dotazů na protokoly a interaktivní analýza získaných výsledků. I v případě protokolu dotazu se používá jinde ve službě Azure Monitor, budete obvykle zápisu a otestujte dotaz nejdřív pomocí Log Analytics.

Log Analytics můžete začít z několika míst na portálu Azure portal. Rozsah dat k dispozici ke službě Log Analytics se určuje podle způsob spuštění. Zobrazit [oboru dotazu](scope.md) další podrobnosti.

- Vyberte **protokoly** z **Azure Monitor** nabídky nebo **pracovních prostorů Log Analytics** nabídky.
- Vyberte **Analytics** z **přehled** stránky aplikace služby Application Insights.
- Vyberte **protokoly** v nabídce prostředku Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Zobrazit [Začínáme se službou Log Analytics ve službě Azure Monitor](get-started-portal.md) podrobný kurz služby Log Analytics, která představuje řadu jejích funkcí.

## <a name="where-else-are-log-queries-used"></a>Kde jinde ještě se používají protokol dotazy?
Kromě interaktivní práce s dotazy log a jejich výsledky ve službě Log Analytics, oblastí ve službě Azure Monitor, kde budete používat dotazy patří:

- **Pravidla upozornění.** [Pravidla výstrah](../platform/alerts-overview.md) proaktivně identifikovat problémy z dat ve vašem pracovním prostoru.  Každé pravidlo výstrahy je založen na prohledávání protokolu, který se spouští automaticky v pravidelných intervalech.  Výsledky jsou kontrolovány k určení Pokud by měla být vytvořena výstraha.
- **Řídicí panely.** Můžete připnout výsledky jakéhokoli dotazu do [řídicí panel Azure](../learn/tutorial-logs-dashboards.md) který vám umožní vizualizovat data protokolů a metrik společně a volitelně sdílet s ostatními uživateli Azure.
- **Zobrazení.**  Možnost tvorby vizualizací dat mají být zahrnuty v řídicích panelech uživatele s [Návrhář zobrazení](../platform/view-designer.md).  Protokol dotazů, které poskytují data používá [dlaždice](../platform/view-designer-tiles.md) a [části vizualizace](../platform/view-designer-parts.md) v každém zobrazení.  
- **Export.**  Když importujete data protokolů ze služby Azure Monitor do aplikace Excel nebo [Power BI](../platform/powerbi.md), vytvořte dotaz protokolu pro definování dat k exportu.
- **PowerShell.** Skript prostředí PowerShell můžete spustit z příkazového řádku nebo runbooku Azure Automation, který používá [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) načíst data protokolů ze služby Azure Monitor.  Tato rutina vyžaduje dotazu určit data, která mají načíst.
- **Rozhraní API protokoly Azure monitoru.**  [Rozhraní API služby Azure Monitor protokoly](../platform/alerts-overview.md) umožňuje jakéhokoli klienta REST API k načtení dat protokolu z pracovního prostoru.  Žádosti rozhraní API obsahuje dotaz, který je spuštěn proti Azure Monitor k určení dat k načtení.


## <a name="next-steps"></a>Další postup
- Projít [kurz o používání Log Analytics na portálu Azure portal](get-started-portal.md).
- Projít [kurz o psaní dotazů](get-started-queries.md).
