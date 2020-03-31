---
title: Přehled dotazů protokolů ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Odpovědi na běžné otázky týkající se dotazů protokolu a nastartuje je.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670113"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Přehled dotazů protokolů v Azure Monitoru
Protokolové dotazy vám pomohou plně využít hodnotu dat shromážděných v [protokolech monitorování Azure](../platform/data-platform-logs.md). Výkonný dotazovací jazyk umožňuje spojit data z více tabulek, agregovat velké sady dat a provádět složité operace s minimálním kódem. Prakticky všechny otázky mohou být zodpovězeny a analýzy provedeny tak dlouho, dokud byla shromážděna podpůrná data a chápete, jak vytvořit správný dotaz.

Některé funkce v Azure Monitoru, jako jsou [přehledy](../insights/insights-overview.md) a [řešení](../insights/solutions-inventory.md) zpracování dat protokolu bez vystavení podkladových dotazů. Chcete-li plně využít další funkce Azure Monitoru, měli byste pochopit, jak se výpočty dotazů a jak je můžete použít k interaktivní analýze dat v protokolech azure monitoru.

Tento článek použijte jako výchozí bod pro učení o dotazech protokolu v Azure Monitoru. Odpovídá na běžné otázky a obsahuje odkazy na další dokumentaci, která poskytuje další podrobnosti a lekce.

## <a name="how-can-i-learn-how-to-write-queries"></a>Jak se mohu dozvědět, jak psát dotazy?
Pokud chcete skočit přímo do věcí, můžete začít s následujícími kurzy:

- [Začínáme s Analýzou protokolů ve službě Azure Monitor](get-started-portal.md).
- [Začínáme s dotazy protokolu v Azure Monitoru](get-started-queries.md).

Jakmile budete mít základy dolů, projděte si několik lekcí pomocí vlastních dat nebo dat z našeho demo prostředí, počínaje: 

- [Práce s řetězci v dotazech protokolu Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Jaký jazyk používají dotazy protokolu?
Protokoly monitorování Azure je založen na [Azure Data Explorer](/azure/data-explorer)a dotazy protokolu se zapisují pomocí stejného dotazovacího jazyka Kusto (KQL). Jedná se o bohatý jazyk navržený tak, aby byl snadno čitelný a autorský, a měli byste být schopni začít používat s minimálním vedením.

Přečtěte si [dokumentaci k KQL průzkumníka dat Azure](/azure/kusto/query) pro úplnou dokumentaci k KQL a odkaz na různé funkce, které jsou k dispozici.<br>
Rychlý návod k jazyku pomocí dat z protokolů Azure Monitor utématite v tématu [Začínáme s dotazy protokolů v Azure Monitoru.](get-started-queries.md)
Viz [Azure Monitor dotaz dotazovat se na rozdíly](data-explorer-difference.md) v dotazu na drobné rozdíly ve verzi KQL používané službou Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Jaká data jsou k dispozici pro protokolování dotazů?
Všechna data shromážděná v protokolech monitorování Azure jsou k dispozici k načtení a analýze v dotazech protokolu. Různé zdroje dat budou zapisovat svá data do různých tabulek, ale do jednoho dotazu můžete zahrnout více tabulek a analyzovat data z více zdrojů. Při vytváření dotazu začnete určením, které tabulky obsahují data, která hledáte, takže byste měli mít alespoň základní znalosti o tom, jak jsou data v protokolech monitorování Azure strukturována.

V části [Zdroje protokolů monitorování Azure](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)najdete seznam různých zdrojů dat, které naplňují protokoly monitorování Azure.<br>
Vysvětlení, jak jsou data strukturována, najdete v tématu [Struktura protokolů monitorování Azure.](logs-structure.md)

## <a name="what-does-a-log-query-look-like"></a>Jak vypadá dotaz protokolu?
Dotaz může být stejně jednoduchý jako název jedné tabulky pro načítání všech záznamů z této tabulky:

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
I v případě, že nejste obeznámeni s KQL, měli byste být schopni alespoň zjistit základní logiku, kterou tyto dotazy používají. Začínají názvem tabulky a pak přidají více příkazů pro filtrování a zpracování těchto dat. Dotaz může používat libovolný počet příkazů a můžete psát složitější dotazy, jakmile se seznámíte s různými příkazy KQL, které jsou k dispozici.

Viz [Začínáme s dotazy protokolu v Azure Monitoru](get-started-queries.md) pro kurz o dotazech protokolu, který zavádí jazyk a běžné funkce .<br>


## <a name="what-is-log-analytics"></a>Co je služba Log Analytics?
Log Analytics je primární nástroj na portálu Azure pro psaní dotazů protokolu a interaktivní analýzu jejich výsledků. I v případě, že dotaz protokolu se používá jinde ve službě Azure Monitor, obvykle zapíšete a otestujete dotaz nejprve pomocí Log Analytics.

Analýzu protokolů můžete spustit z několika míst na webu Azure Portal. Rozsah dat, která jsou k dispozici pro analýzu protokolů, je určen způsobem, jakým je spustíte. Další podrobnosti najdete [v tématu Obor dotazu.](scope.md)

- V nabídce **Azure Monitor** nebo **v pracovních prostorech Log Analytics vyberte** **Protokoly.**
- Na stránce **Přehled** aplikace Application Insights vyberte **Analytics.**
- V nabídce prostředku Azure vyberte **Protokoly.**

![Log Analytics](media/log-query-overview/log-analytics.png)

Podívejte [se na začínáme s Log Analytics v Azure Monitor](get-started-portal.md) pro výukový návod log analytics, který zavádí několik jeho funkcí.

## <a name="where-else-are-log-queries-used"></a>Kde jinde se používají dotazy protokolu?
Kromě interaktivní práce s dotazy protokolu a jejich výsledky v Log Analytics, oblasti ve službě Azure Monitor, kde budete používat dotazy patří následující:

- **Pravidla upozornění.** [Pravidla výstrah](../platform/alerts-overview.md) proaktivně identifikují problémy z dat ve vašem pracovním prostoru.  Každé pravidlo výstrahy je založeno na hledání protokolu, které je automaticky spuštěno v pravidelných intervalech.  Výsledky jsou kontrolovány k určení, pokud by měla být vytvořena výstraha.
- **Řídicích panelů.** Výsledky libovolného dotazu můžete připnout do [řídicího panelu Azure,](../learn/tutorial-logs-dashboards.md) který vám umožní společně vizualizovat data protokolu a metrik y a volitelně je sdílet s ostatními uživateli Azure.
- **Zobrazení.**  Pomocí [aplikace View Designer](../platform/view-designer.md)můžete vytvářet vizualizace dat, která budou zahrnuta do řídicích panelů uživatelů .  Dotazy protokolu poskytují data používaná [dlaždicemi](../platform/view-designer-tiles.md) a [vizualizačními částmi](../platform/view-designer-parts.md) v každém zobrazení.  
- **Vývozní.**  Při importu dat protokolu z Azure Monitoru do Excelu nebo [Power BI](../platform/powerbi.md)vytvoříte dotaz protokolu, který definuje data, která se mají exportovat.
- **Powershell.** Skript PowerShellu můžete spustit z příkazového řádku nebo runbooku Azure Automation, který používá [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) k načtení dat protokolu z Azure Monitoru.  Tato rutina vyžaduje dotaz k určení dat k načtení.
- **Rozhraní API protokolů azure monitoru.**  Rozhraní [API protokolů monitorování Azure](https://dev.loganalytics.io) umožňuje libovolnému klientovi rozhraní REST API načíst data protokolu z pracovního prostoru.  Požadavek rozhraní API obsahuje dotaz, který je spuštěn proti Azure Monitor k určení dat k načtení.


## <a name="next-steps"></a>Další kroky
- Projděte si [kurz o používání Log Analytics na webu Azure Portal](get-started-portal.md).
- Projděte si [návod na psaní dotazů](get-started-queries.md).
