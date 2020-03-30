---
title: Vytváření grafů a diagramů z dotazů protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Popisuje různé vizualizace v Azure Monitoru pro zobrazení dat protokolu různými způsoby.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670317"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Vytváření grafů a diagramů z dotazů protokolu Azure Monitor

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [rozšířené agregace v dotazech protokolu Azure Monitor.](advanced-aggregations.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje různé vizualizace v Azure Monitoru pro zobrazení dat protokolu různými způsoby.

## <a name="charting-the-results"></a>Zmapování výsledků
Začněte kontrolou, kolik počítačů je v operačním systému za poslední hodinu:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Ve výchozím nastavení se výsledky zobrazují jako tabulka:

![Table](media/charts/table-display.png)

Chcete-li získat lepší zobrazení, vyberte **Graf**a zvolte možnost **Výsečový graf,** abyste si výsledky představili:

![Výsečový graf](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Časové grafy
V přihrádkách po 1 hodině zobrazíte průměr 50. Dotaz generuje více řad a pak můžete vybrat, které řady se mají zobrazit v časovém grafu:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Vyberte možnost zobrazení **spojnicového** grafu:

![Spojnicový graf](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Vztažná čára

Referenční čára vám může pomoci snadno identifikovat, zda metrika překročila určitou prahovou hodnotu. Chcete-li do grafu přidat čáru, rozšiřte datovou sadu o konstantní sloupec:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Vztažná čára](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Více dimenzí
Více výrazů `by` v `summarize` klauzuli vytvořit více řádků ve výsledcích, jeden pro každou kombinaci hodnot.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Při zobrazení výsledků jako grafu použije první sloupec `by` z klauzule. Následující příklad ukazuje skládaný sloupcový graf pomocí _ID události._ Dimenze musí být `string` typu, takže v tomto příkladu _eventid_ je přetypován na řetězec. 

![Pruhový graf EventID](media/charts/charts-and-diagrams-multiDimension1.png)

Mezi nimi můžete přepínat výběrem rozevíracího souboru s názvem sloupce. 

![Typ účtu pruhového grafu](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Další kroky
Podívejte se na další lekce pro používání [dotazovacího jazyka Kusto](/azure/kusto/query/) s daty protokolu Azure Monitor:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Psaní rozšířených dotazů](advanced-query-writing.md)
- [Spojení](joins.md)
