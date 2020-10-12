---
title: Vytváření grafů a diagramů z Azure Monitorch dotazů protokolu | Microsoft Docs
description: Popisuje různé vizualizace v Azure Monitor k zobrazení dat protokolu různými způsoby.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670317"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Vytváření grafů a diagramů z Azure Monitorch dotazů protokolu

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [Pokročilé agregace v Azure Monitorch dotazech protokolu](advanced-aggregations.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje různé vizualizace v Azure Monitor k zobrazení dat protokolu různými způsoby.

## <a name="charting-the-results"></a>Segrafování výsledků
Začněte tím, že zkontrolujete, kolik počítačů v jednom operačním systému je během poslední hodiny:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Ve výchozím nastavení se výsledky zobrazují jako tabulka:

![Tabulka](media/charts/table-display.png)

Chcete-li získat lepší zobrazení, vyberte **graf**a zvolte možnost **výseč** k vizualizaci výsledků:

![Výsečový graf](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
Zobrazí průměrné, 50 a 95. percentily času procesoru v přihrádkách po 1 hodinách. Dotaz vygeneruje více řad a Vy pak můžete vybrat, které řady se mají zobrazit v časovém grafu:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Vyberte možnost zobrazení **spojnicového** grafu:

![Spojnicový graf](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Referenční čára

Referenční čára vám umožní snadno identifikovat, jestli metrika překročila konkrétní prahovou hodnotu. Chcete-li přidat čáru do grafu, zvětšete datovou sadu pomocí konstantního sloupce:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Referenční čára](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Více dimenzí
Více výrazů v `by` klauzuli `summarize` pro vytvoření více řádků ve výsledcích, jeden pro každou kombinaci hodnot.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Při zobrazení výsledků jako grafu použije první sloupec z `by` klauzule. Následující příklad znázorňuje skládaný sloupcový graf s použitím _ID události._ Dimenze musí být `string` typu, takže v tomto příkladu je _ID události_ přetypování do řetězce. 

![ID události pruhového grafu](media/charts/charts-and-diagrams-multiDimension1.png)

Můžete přepínat mezi výběrem rozevírací nabídky s názvem sloupce. 

![AccountType pruhového grafu](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Další kroky
Podívejte se na další lekce týkající se používání [dotazovacího jazyka Kusto](/azure/kusto/query/) s využitím dat protokolu Azure monitor:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Psaní rozšířených dotazů](advanced-query-writing.md)
- [Spojení](joins.md)
