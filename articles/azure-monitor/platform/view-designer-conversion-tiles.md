---
title: Návrhář zobrazení Azure Monitor u převodů dlaždic sešitů
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658622"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Převody dlaždic návrháře zobrazení Azure Monitor
[Návrhář zobrazení](view-designer.md) je funkce Azure Monitoru, která umožňuje vytvářet vlastní zobrazení, která vám pomohou vizualizovat data v pracovním prostoru Log Analytics pomocí grafů, seznamů a časových os. Jsou postupně ukončovány a nahrazovány sešity, které poskytují další funkce. Tento článek obsahuje podrobnosti pro převod různých dlaždic do sešitů.

## <a name="donut--list-tile"></a>Dlaždice seznamu &

![Seznam koblih](media/view-designer-conversion-tiles/donut-list.png)

Opětovné vytvoření dlaždice seznamu donut& v sešitech zahrnuje dvě samostatné vizualizace. Pro koblihovou část existují dvě možnosti.
Pro oba začít výběrem **Přidat dotaz** a vložte původní dotaz z návrháře zobrazení do buňky.

**Možnost 1:** V rozevíracím lístku **Vizualizace** vyberte ![ **výsečový graf:** Vizualizace výsečového grafu](media/view-designer-conversion-tiles/pie-chart.png)

**Možnost č. 2:** V rozevíracím souboru **Vizualizace** `| render piechart` vyberte **Nastavit podle dotazu** a přidejte do něj:

 ![Vizualizační nabídka](media/view-designer-conversion-tiles/set-by-query.png)

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

Informace o vytvoření seznamu a povolení minigrafů naleznete v článku o [běžných úkolech](view-designer-conversion-tasks.md).

Následuje příklad, jak může být dlaždice seznamu & koblihy v sešitech přeinterpretována:

![Sešity seznamu koblih](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Dlaždice & seznamu spojnicového grafu
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

Existují dvě možnosti vizualizace spojnicového grafu.

**Možnost 1:** V rozevíracím rozevíracím řádku **Vizualizace** vyberte **Spojnicový graf:**
 
 ![Nabídka spojnicového grafu](media/view-designer-conversion-tiles/line-visualization.png)

**Možnost č. 2:** V rozevíracím souboru **Vizualizace** `| render linechart` vyberte **Nastavit podle dotazu** a přidejte do něj:

 ![Vizualizační nabídka](media/view-designer-conversion-tiles/set-by-query.png)

**Příklad**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Informace o vytvoření seznamu a povolení minigrafů naleznete v článku o [běžných úkolech](view-designer-conversion-tasks.md).

Následuje příklad, jak může být spojnicový graf & seznam v sešitech přeinterpretován:

![Sešity se seznamem spojnicových grafů](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Dlaždice seznamu číslo &

 ![Seznam dlaždic](media/view-designer-conversion-tiles/tile-list-example.png)

Pro dlaždici číslo aktualizujte dotaz takto:

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

Změňte rozevírací nabídka Vizualizace na **Dlaždice** a pak vyberte **Nastavení dlaždic**.
 ![Vizualizace dlaždic](media/view-designer-conversion-tiles/tile-visualization.png)

Ponechte oddíl **Název** prázdný a vyberte **Vlevo**. Změňte hodnotu **pro použít sloupec:** **na Count**a **Column Renderer** to **Big Number**:

![Nastavení dlaždic](media/view-designer-conversion-tiles/tile-settings.png)

 
Informace o vytvoření seznamu a povolení minigrafů naleznete v článku o [běžných úkolech](view-designer-conversion-tasks.md).

Následuje příklad, jak může být číslo & seznam v sešitech přeinterpretováno:

![Sešity seznamu čísel](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Časová osa a seznam

 ![Seznam časové osy](media/view-designer-conversion-tiles/time-list.png)

Pro časovou osu aktualizujte dotaz takto:

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

Existují dvě možnosti vizualizace dotazu jako pruhového grafu:

**Možnost 1:** V rozevíracím rozevíracím ![panelu Vizualizace vyberte **pruhový** **graf:** Vizualizace barchartu](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Možnost č. 2:** V rozevíracím souboru **Vizualizace** `| render barchart` vyberte **Nastavit podle dotazu** a přidejte do něj:

 ![Vizualizační nabídka](media/view-designer-conversion-tiles/set-by-query.png)

 
Informace o vytvoření seznamu a povolení minigrafů naleznete v článku o [běžných úkolech](view-designer-conversion-tasks.md).

Následuje příklad, jak může být v sešitech znovu interpretována dlaždice & seznamu časové osy:

![Sešity se seznamem časových os](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Další kroky

- [Přehled přechodu návrháře zobrazení do sešitů](view-designer-conversion-overview.md)
