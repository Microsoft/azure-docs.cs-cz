---
title: Vytváření grafů a diagramů z dotazů na protokoly Azure Monitor | Dokumentace Microsoftu
description: Popisuje různé vizualizace ve službě Azure Monitor k zobrazení dat protokolů různými způsoby.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 07d0866bd697587da170a00e8077a57035989d32
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269412"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Vytváření grafů a diagramů z dotazů na protokoly Azure monitoru

> [!NOTE]
> By se měla Dokončit [Advanced agregace v dotazů na protokoly Azure monitoru](advanced-aggregations.md) před dokončením v této lekci.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje různé vizualizace ve službě Azure Monitor k zobrazení dat protokolů různými způsoby.

## <a name="charting-the-results"></a>Vytvoření grafu výsledků
Nejdřív si prostudujte kolika počítačích během poslední hodiny na operační systém, jsou:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Ve výchozím nastavení zobrazí výsledky jako tabulka:

![Table](media/charts/table-display.png)

Chcete-li získat lepší zobrazení, vyberte **grafu**a zvolte **výsečový** možnost vizualizovat výsledky:

![Výsečový graf](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
Zobrazit průměr, 50. a 95. percentily čas procesoru v přihrádky 1 hodina. Vygeneruje více řad dotaz a pak vyberete, které řady znázornit v grafu čas:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Vyberte **řádku** graf možnosti zobrazení:

![Spojnicový graf](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Referenční čáry

Referenční čáry můžete snáze identifikovat metriku překročení konkrétních mezních hodnot. Přidání řádku do grafu, rozšiřte datovou sadu s konstantní sloupce:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Referenční čáry](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Více dimenzí
Více výrazů v `by` klauzuli `summarize` vytvořit více řádků ve výsledcích, jeden pro každou kombinaci hodnot.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Při zobrazení výsledků jako graf, použije první sloupec z `by` klauzuli. Následující příklad ukazuje použití skládaného sloupcového grafu _EventID._ Dimenze musí být `string` typ, tak v tomto příkladu _EventID_ byl přetypován na řetězec. 

![Identifikátor EventID pruhový graf](media/charts/charts-and-diagrams-multiDimension1.png)

Můžete přepínat mezi tak, že vyberete rozevírací seznam s názvem sloupce. 

![AccountType pruhový graf](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Další postup
Zobrazit další lekce pro použití [Kusto dotazovací jazyk](/azure/kusto/query/) službou Azure Monitor můžete vytvářet protokoly dat:

- [Operace s řetězci](string-operations.md)
- [Datum a čas operace](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datových struktur](json-data-structures.md)
- [Zápis rozšířeného dotazu](advanced-query-writing.md)
- [Spojení](joins.md)