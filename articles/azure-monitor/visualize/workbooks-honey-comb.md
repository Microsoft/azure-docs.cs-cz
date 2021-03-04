---
title: Vizualizace v hřebenu v Azure Monitor sešitu
description: Přečtěte si o Azure Monitor vizualizace v hřebenu v sešitu.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: c91912f711b912c54c1673f2f92e998b4d9ea9db
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736929"
---
# <a name="honey-comb-visualizations"></a>Provizualizace v hřebenu

Med – hřebeny umožňují zobrazení s vysokou hustotou metrik nebo kategorií, které mohou být volitelně seskupeny jako clustery. Jsou užitečné při vizuální identifikaci hotspotů a dalším procházení.

Následující obrázek ukazuje využití procesoru u virtuálních počítačů v rámci dvou předplatných. Každá buňka představuje virtuální počítač a barva nebo popisek představuje průměrné využití procesoru (Reds jsou horké počítače). Virtuální počítače jsou seskupené podle předplatného.

[![Snímek obrazovky znázorňující využití CPU pro virtuální počítače ve dvou předplatných](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>Přidání hřebenu medu

1. Kliknutím na položku panelu nástrojů pro úpravy přepněte sešit do režimu úprav.
2. Pomocí příkazu **Přidat**  v dolní části klikněte na Přidat *dotaz* a přidejte do sešitu ovládací prvek pro dotaz na protokol.
3. Jako *zdroj dat* jako *typ prostředku* vyberte "Logs", "Log Analytics" a jako *prostředek* přejděte do pracovního prostoru s protokolem výkonu virtuálního počítače.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. Spustit dotaz.
6. Nastavte *vizualizaci* na Graph.
7. Vyberte **nastavení grafu**.
    1. V dolní části *pole rozložení* nastavte:
        1. Typ grafu: **clustery podregistru**.
        2. ID uzlu: `Id` .
        3. Seskupit `None` podle:
        4. Velikost uzlu: 100.
        5. Okraj mezi šestiúhelníky: `5` .
        6. Typ barevného zbarvení: **heatmapu**.
        7. Pole barvy uzlu: `CouterValue` .
        8. Barevná paleta: `Red to Green` .
        9. Minimální hodnota: `100` .
        10. Maximální hodnota: `2000` .
    2. V *nastavení formát uzlu* v horní části nastavte:
        1. Horní obsah:
            1. Použít sloupec: `Computer` .
            2. Vykreslovací modul sloupců `Text`
        9. Obsah centra:
            1. Použít sloupec: `CounterValue` .
            2. Zobrazovací jednotka sloupce: `Big Number` .
            3. Barevná paleta: `None` .
            4. Zaškrtněte políčko *vlastní formátování čísel* .
            5. Jednotky: `Megabytes` .
            6. Maximální počet zlomkových číslic: `1` .
8. V dolní části podokna vyberte tlačítko **Uložit a zavřít** .

[![Snímek obrazovky ovládacího prvku dotaz, nastavení grafu a hřebene s výše uvedeným dotazem a nastavením](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>Nastavení rozložení hřebene v-v

| Nastavení | Vysvětlení |
|:------------- |:-------------|
| `Node Id` | Vybere sloupec, který poskytuje jedinečné ID uzlů. Hodnotou sloupce může být řetězec nebo číslo. |
| `Group By Field` | Vyberte sloupec, na kterém chcete uzly clusterovat. |
| `Node Size` | Nastaví velikost šestiúhelníkových buněk. Pomocí vlastnosti s `Margin between hexagons` vlastností můžete přizpůsobit vzhled grafu v rámci medu. |
| `Margin between hexagons` | Nastaví mezeru mezi šestiúhelníky buněk. Pomocí vlastnosti s `Node size` vlastností můžete přizpůsobit vzhled grafu v rámci medu. |
| `Coloring type` | Vybere schéma, které se má použít k vybarvení uzlu. |
| `Node Color Field` | Vybere sloupec, který poskytuje metriku, na které budou oblasti uzlu založeny. |

## <a name="node-coloring-types"></a>Typy obarvení uzlů

| Typ barevného zbarvení | Vysvětlení |
|:------------- |:-------------|
| `None` | Všechny uzly mají stejnou barvu. |
| `Categorical` | K uzlům jsou přiřazeny barvy na základě hodnoty nebo kategorie ze sloupce v sadě výsledků. V předchozím příkladu jsou barvy vycházející z _typu_ sloupce sady výsledků dotazu. Podporované palety jsou `Default` , `Pastel` a `Cool tone` .  |
| `Heatmap` | V tomto typu jsou buňky barevné na základě sloupce metriky a palety barev. To poskytuje jednoduchý způsob, jak zvýraznit metriky napříč buňkami. |
| `Thresholds` | V tomto typu jsou barvy buňky nastavené podle mezních pravidel (například _CPU > 90% => červená, 60% > CPU > 90% => Yellow, CPU < 60% => zelená_). |
| `Field Based` | V tomto typu sloupec poskytuje konkrétní hodnoty RGB, které se mají použít pro uzel. Poskytuje největší flexibilitu, ale obvykle vyžaduje více práce, které je potřeba povolit.  |
      
## <a name="node-format-settings"></a>Nastavení formátu uzlu

Tvůrci v hřebenu mohou určit, jaký obsah přejde do různých částí uzlu: nahoře, vlevo, na střed, vpravo a dole. Autoři si mohou použít kterýkoli z sešitů pro vykreslování (text, velké číslo, sparkové čáry, ikona atd.).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [v sešitech vytvořit modul pro vykreslování kompozitních pruhů](workbooks-composite-bar.md).
- Naučte se [importovat data protokolu Azure monitor do Power BI](./powerbi.md).