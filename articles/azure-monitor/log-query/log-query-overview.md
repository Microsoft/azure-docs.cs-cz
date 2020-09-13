---
title: Přehled dotazů protokolu v Azure Monitor | Microsoft Docs
description: Odpoví na běžné otázky související s dotazy protokolu a pomůže vám s jejich pomocí začít.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 5a47de51a90d926b47f6b465d6c10111407da9c6
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033162"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Přehled dotazů protokolu v Azure Monitor
Dotazy protokolu umožňují plně využít hodnoty dat shromažďovaných v [protokolech Azure monitor](../platform/data-platform-logs.md). Výkonný dotazovací jazyk umožňuje spojit data z více tabulek, agregovat velké sady dat a provádět komplexní operace s minimálním kódem. Prakticky všechny dotazy mohou být zodpovězeny a analýzou provedeny, pokud jsou shromážděna podpůrná data a rozumíte tomu, jak vytvořit správný dotaz.

Některé funkce v Azure Monitor, jako jsou [přehledy](../insights/insights-overview.md) a [řešení](../monitor-reference.md) , zpracovávají data protokolu bez odhalení základních dotazů. Abyste mohli plně využít jiné funkce Azure Monitor, měli byste pochopit, jak se vytvářejí dotazy a jak je můžete používat k interaktivní analýze dat v protokolech Azure Monitor.

V tomto článku se dozvíte, jak se naučíte používat dotazy protokolu v Azure Monitor. Odpovídá na časté otázky a poskytuje odkazy na další dokumentaci, která poskytuje další podrobnosti a lekce.

## <a name="how-can-i-learn-how-to-write-queries"></a>Jak se můžu dozvědět, jak zapisovat dotazy?
Pokud chcete přejít přímo na nějaké věci, můžete začít s následujícími kurzy:

- Začněte [s Log Analytics v Azure monitor](get-started-portal.md).
- [Začínáme s dotazy protokolu v Azure monitor](get-started-queries.md).

Jakmile vyberete základní informace, Projděte si více lekcí s využitím vlastních dat nebo dat z našeho ukázkového prostředí od: 

- [Práce s řetězci v Azure Monitorch dotazech protokolu](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Jaký jazyk používají dotazy protokolu?
Protokoly Azure Monitor jsou založené na [Azure Průzkumník dat](/azure/data-explorer)a dotazy protokolu se zapisují pomocí stejného dotazovacího jazyka KQL (Kusto Query Language). Jedná se o bohatou jazyk navržený tak, aby bylo snadné ho číst a vytvářet a abyste ho mohli začít používat s minimálními pokyny.

Úplnou dokumentaci k KQL najdete v [dokumentaci k Azure Průzkumník dat KQL](/azure/kusto/query) a referenční informace k různým dostupným funkcím.<br>
Rychlý návod k jazyku s použitím dat z protokolů Azure Monitor najdete [v tématu Začínáme s dotazy protokolu v Azure monitor](get-started-queries.md) .
V tématu [Azure monitor rozdíly v jazyce dotazů protokolu](data-explorer-difference.md) pro drobné rozdíly ve verzi KQL používané v Azure monitor.

## <a name="what-data-is-available-to-log-queries"></a>Jaká data jsou k dispozici pro dotazy protokolů?
Všechna data shromážděná v protokolech Azure Monitor jsou k dispozici pro načtení a analýzu v protokolových dotazech. Různé zdroje dat zapisují svá data do různých tabulek, ale k analýze dat napříč více zdroji můžete použít více tabulek v jednom dotazu. Když vytváříte dotaz, začnete tím, že určíte, které tabulky mají data, která hledáte. Vysvětlení způsobu strukturování dat najdete v tématu [Struktura protokolů Azure monitor](logs-structure.md) .

## <a name="what-does-a-log-query-look-like"></a>Jak vypadá dotaz protokolu?
Dotaz může být jednoduchý jako název jedné tabulky pro načtení všech záznamů z této tabulky:

```Kusto
Syslog
```

Nebo může filtrovat konkrétní záznamy, shrnout je a vizualizovat výsledky v grafu:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Pro složitější analýzu můžete načíst data z více tabulek pomocí spojení k analýze výsledků společně.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
I když nejste obeznámeni s KQL, měli byste být schopni aspoň zjistit základní logiku, kterou tyto dotazy používají. Začínají názvem tabulky a potom přidávají více příkazů pro filtrování a zpracování těchto dat. Dotaz může používat libovolný počet příkazů a můžete psát složitější dotazy, jak se seznámit s různými KQL příkazy k dispozici.

V tématu Začínáme [s dotazy protokolu v Azure monitor](get-started-queries.md) najdete kurz o dotazech protokolů, které zavádí jazyk a běžné funkce.<br>


## <a name="what-is-log-analytics"></a>Co je služba Log Analytics?
Log Analytics je primárním nástrojem na webu Azure Portal pro zápis dotazů na protokoly a interaktivní analýzu jejich výsledků. I v případě, že se dotaz protokolu používá jinde v Azure Monitor, obvykle nejprve zapíšete a otestujete dotaz pomocí Log Analytics.

Můžete začít Log Analytics z několika míst v Azure Portal. Rozsah dat, která jsou k dispozici pro Log Analytics, je určen podle toho, jak je spustíte. Další podrobnosti najdete v [oboru dotazů](scope.md) .

- Z nabídky **Azure monitor** nebo z nabídky **pracovní prostory Log Analytics** vyberte **protokoly** .
- Na stránce **Přehled** aplikace Application Insights vyberte **protokoly** .
- V nabídce prostředku Azure vyberte **protokoly** .

![Log Analytics](media/log-query-overview/log-analytics.png)

V článku [Začínáme s Log Analytics v Azure monitor](get-started-portal.md) najdete návod k Log Analytics, který přináší několik jeho funkcí.

## <a name="where-else-are-log-queries-used"></a>Kde jinde se používají dotazy protokolu?
Kromě interaktivní práce s dotazy protokolů a jejich výsledky v Log Analytics oblasti v Azure Monitor, kde budete používat dotazy, patří následující:

- **Pravidla výstrah.** [Pravidla výstrah](../platform/alerts-overview.md) proaktivně identifikují problémy z dat ve vašem pracovním prostoru.  Každé pravidlo výstrahy je založené na prohledávání protokolu, které se automaticky spouští v pravidelných intervalech.  Výsledky jsou zkontrolovány, aby bylo možné určit, zda má být vytvořena výstraha.
- **Řídicí panely.** Výsledky jakéhokoli dotazu můžete připnout na [řídicí panel Azure](../learn/tutorial-logs-dashboards.md) , který vám umožní vizualizovat data protokolů a metrik společně a volitelně sdílet s ostatními uživateli Azure.
- **Náhled.**  Vizualizace dat, která se mají zahrnout do uživatelských řídicích panelů, můžete vytvořit pomocí [návrháře zobrazení](../platform/view-designer.md).  Dotazy protokolu poskytují data používaná [dlaždicemi](../platform/view-designer-tiles.md) a [částmi vizualizace](../platform/view-designer-parts.md) v každém zobrazení.  
- **Export.**  Když importujete data protokolu z Azure Monitor do aplikace Excel nebo [Power BI](../platform/powerbi.md), vytvoříte dotaz protokolu pro definování dat k exportu.
- **Prostředí.** PowerShellový skript můžete spustit z příkazového řádku nebo Azure Automation Runbooku, který pomocí rutiny [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) načte data protokolu z Azure monitor.  Tato rutina vyžaduje dotaz k určení dat, která se mají načíst.
- **Rozhraní API protokolů Azure Monitor.**  [Rozhraní API protokolů Azure monitor](https://dev.loganalytics.io) umožňuje každému klientovi REST API načíst data protokolu z pracovního prostoru.  Požadavek rozhraní API obsahuje dotaz, který se spouští proti Azure Monitor k určení dat, která se mají načíst.


## <a name="next-steps"></a>Další kroky
- Projděte si [kurz použití Log Analytics v Azure Portal](get-started-portal.md).
- Projděte si [kurz týkající se psaní dotazů](get-started-queries.md).
