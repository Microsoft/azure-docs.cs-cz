---
title: Převod návrháře zobrazení na dlaždice v sešitech Azure Monitor
description: Podrobnosti pro převod dlaždic na sešity při přechodu ze zobrazení v Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 9b8d1840c0b1cea4c03312d38ed892fde94faa4c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043333"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Převody dlaždic návrháře zobrazení Azure Monitor
[Návrhář zobrazení](view-designer.md) je funkce Azure monitor, která umožňuje vytvářet vlastní zobrazení, která vám pomohou vizualizovat data v pracovním prostoru Log Analytics s grafy, seznamy a časovými osami. Budou postupně vyladěny a nahrazeny sešity, které poskytují další funkce. Tento článek poskytuje podrobné informace pro převod různých dlaždic na sešity.

## <a name="donut--list-tile"></a>Dlaždice seznamu & prstence

![Prstencový seznam](media/view-designer-conversion-tiles/donut-list.png)

Opětovné vytvoření dlaždice seznamu & prstence v sešitech zahrnuje dvě samostatné vizualizace. Pro část prstenec jsou k dispozici dvě možnosti.
V nabídce Start vyberte možnost **Přidat dotaz** a vložte původní dotaz z návrháře zobrazení do buňky.

**Možnost 1:** Výběr **výsečového grafu** z rozevíracího seznamu **vizualizace** : ![ Nabídka vizualizace výsečového grafu](media/view-designer-conversion-tiles/pie-chart.png)

**Možnost 2:** V rozevíracím seznamu **vizualizace** vyberte možnost **nastavit podle dotazu** a přidejte `| render piechart` do něj dotaz:

 ![Nabídka vizualizace](media/view-designer-conversion-tiles/set-by-query.png)

**Příklad**

Původní dotaz
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Aktualizovaný dotaz
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Pokud chcete vytvořit seznam a povolit minigrafy, přečtěte si článek o [běžných úlohách](view-designer-conversion-tasks.md).

Následuje příklad, jak může být dlaždice seznamu & prstence překládána v sešitech:

![Prstencový seznam sešitů](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Dlaždice seznamu & spojnicového grafu
![Seznam spojnicových grafů](media/view-designer-conversion-tiles/line-list.png) 

Chcete-li znovu vytvořit část spojnicového grafu, aktualizujte dotaz následujícím způsobem:

Původní dotaz
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Aktualizovaný dotaz
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Existují dvě možnosti, jak vizualizovat spojnicový graf.

**Možnost 1:** V rozevíracím seznamu **vizualizace** vyberte **Spojnicový graf** :
 
 ![Nabídka spojnicového grafu](media/view-designer-conversion-tiles/line-visualization.png)

**Možnost 2:** V rozevíracím seznamu **vizualizace** vyberte možnost **nastavit podle dotazu** a přidejte `| render linechart` do něj dotaz:

 ![Nabídka vizualizace](media/view-designer-conversion-tiles/set-by-query.png)

**Příklad**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Pokud chcete vytvořit seznam a povolit minigrafy, přečtěte si článek o [běžných úlohách](view-designer-conversion-tasks.md).

Následuje příklad, jak může být překládána dlaždice spojnicového grafu & seznamu v sešitech:

![Spojnicový graf – seznam sešitů](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Dlaždice seznamu & číslo

 ![Seznam dlaždic](media/view-designer-conversion-tiles/tile-list-example.png)

Pro dlaždici číslo aktualizujte dotaz následujícím způsobem:

Původní dotaz
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Aktualizovaný dotaz
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Změňte rozevírací seznam vizualizace na **dlaždice** a pak vyberte **nastavení dlaždice**.
 ![Vizualizace dlaždice](media/view-designer-conversion-tiles/tile-visualization.png)

**Název** oddílu Nechejte prázdný a vyberte **vlevo**. Změňte hodnotu vlastnosti **použít sloupec:** na **Count** a vyhodnotit **vykreslování sloupce** na **velké číslo**:

![Nastavení dlaždic](media/view-designer-conversion-tiles/tile-settings.png)

 
Pokud chcete vytvořit seznam a povolit minigrafy, přečtěte si článek o [běžných úlohách](view-designer-conversion-tasks.md).

Následuje příklad, jak je možné přeinterpretovat číslo & dlaždici seznamu v sešitech:

![Seznam čísel v sešitech](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Časová osa a seznam

 ![Seznam časových os](media/view-designer-conversion-tiles/time-list.png)

Pro časovou osu aktualizujte svůj dotaz následujícím způsobem:

Původní dotaz
```KQL
search * 
| sort by TimeGenerated desc
```

Aktualizovaný dotaz
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Existují dvě možnosti, jak vizualizovat dotaz jako pruhový graf:

**Možnost 1:** Výběr **pruhového grafu** z rozevíracího seznamu **vizualizace** : ![ vizualizace BarChart](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Možnost 2:** V rozevíracím seznamu **vizualizace** vyberte možnost **nastavit podle dotazu** a přidejte `| render barchart` do něj dotaz:

 ![Nabídka vizualizace](media/view-designer-conversion-tiles/set-by-query.png)

 
Pokud chcete vytvořit seznam a povolit minigrafy, přečtěte si článek o [běžných úlohách](view-designer-conversion-tasks.md).

Následuje příklad, jak může být v sešitech překládána časová osa & dlaždice:

![Pracovní osa – seznam sešitů](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Další kroky

- [Přehled přechodu návrháře zobrazení na sešity](view-designer-conversion-overview.md)
