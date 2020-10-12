---
title: Vizualizace grafu Azure Monitorového sešitu
description: Přečtěte si o všech vizualizacích grafu Azure Monitorového sešitu.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89664030"
---
# <a name="graph-visualizations"></a>Vizualizace grafů

Pracovní sešity podporují vizualizaci libovolného grafu na základě dat z protokolů, aby se zobrazily vztahy mezi entitami monitorování.

Následující graf ukazuje tok dat do počítače nebo z nich prostřednictvím různých portů do/z externích počítačů. Je barevný podle typu (počítač vs. port vs. externí IP adresa) a velikosti hran odpovídají množství dat toků mezi. Podkladová data pocházejí z KQL dotazů cílících na připojení virtuálních počítačů.

[![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Přidání grafu
1. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku dotaz na protokol do sešitu použijte odkaz **Přidat dotaz** .
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. Nastavení vizualizace na **graf**
6. Kliknutím na tlačítko **nastavení grafu** otevřete podokno nastavení.
7. V dolní části _pole rozložení_ nastavte:
    * ID uzlu: `Id`
    * ID zdroje: `SourceId`
    * ID cíle: `TargetId`
    * Popisek okraje: `None`
    * Velikost okraje: `Calls`
    * Velikost uzlu: `None`
    * Typ vybarvení: `Categorical`
    * Pole barvy uzlu: `Kind`
    * Barevná paleta: `Pastel`
8. V _nastavení formát uzlu_ v horní části nastavte:
    * _Horní sloupec obsahu_– použití: `Name` , Renderer sloupce: `Text`
    * _Center Content_-use Column: `Calls` , Renderer sloupců: `Big Number` , barevná paleta: `None`
    * _Dolní obsah_– sloupec použití: `Kind` , zobrazovací sloupec: `Text`
9. V dolní části podokna vyberte tlačítko _Uložit a zavřít_ .

[![Snímek obrazovky se souhrnným zobrazením dlaždice s výše uvedeným dotazem a nastavením](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Nastavení grafu

| Nastavení         | Vysvětlení                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Vybere sloupec, který poskytuje jedinečné ID uzlů v grafu. Hodnotou sloupce může být řetězec nebo číslo. |
| `Source Id`     | Vybere sloupec, který poskytuje ID zdrojových uzlů pro okraje grafu. Hodnoty musí být namapovány na hodnotu ve sloupci _ID uzlu_ . |
| `Target Id`     | Vybere sloupec, který poskytuje ID cílových uzlů pro okraje grafu. Hodnoty musí být namapovány na hodnotu ve sloupci _ID uzlu_ . |
| `Edge Label`    | Vybere sloupec, který poskytuje popisky okrajů v grafu.                                                            |
| `Edge Size`     | Vybere sloupec, který poskytuje metriku, na které budou na šířku okraje založeny.                                |
| `Node Size`     | Vybere sloupec, který poskytuje metriku, na které budou oblasti uzlu založeny.                                 |
| `Coloring Type` | Slouží k výběru schématu barevného zbarvení uzlu.                                                                            |

## <a name="node-coloring-types"></a>Typy obarvení uzlů

| Typ barevného zbarvení | Vysvětlení |
|:------------- |:------------|
| `None`        | Všechny uzly mají stejnou barvu. |
| `Categorical` | K uzlům jsou přiřazeny barvy na základě hodnoty nebo kategorie ze sloupce v sadě výsledků. V předchozím příkladu jsou barvy vycházející z _typu_ sloupce sady výsledků dotazu. Podporované palety jsou `Default` , `Pastel` a `Cool tone` .  |
| `Field Based` | V tomto typu sloupec poskytuje konkrétní hodnoty RGB, které se mají použít pro uzel. Poskytuje největší flexibilitu, ale obvykle vyžaduje více práce, které je potřeba povolit.  |

## <a name="node-format-settings"></a>Nastavení formátu uzlu

Autoři grafů mohou určit, jaký obsah přejde do různých částí uzlu: nahoře, vlevo, uprostřed, vpravo a dole. Grafy můžou použít kterýkoli z sešitů pro vykreslování (text, velké číslo, sparkové čáry, ikona atd.).

## <a name="field-based-node-coloring"></a>Barvy uzlu založené na poli

1. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku dotaz na protokol do sešitu použijte odkaz **Přidat dotaz** .
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu.

     ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. Nastavení vizualizace na *graf*
6. Kliknutím na tlačítko **nastavení grafu**  otevřete podokno nastavení.
7. V dolní části *pole rozložení* nastavte:
    * ID uzlu:`Id`
    * ID zdroje: `SourceId`
    * ID cíle: `TargetId`
    * Popisek okraje: `None`
    * Velikost okraje: `Calls`
    * Velikost uzlu: `Node`
    * Typ vybarvení: `Field Based`
    * Pole barvy uzlu: `Color`
8. V části *Nastavení formátu uzlu* nahoře zadejte následující.
    * V *horní části obsahu*nastavte:
        * Použít sloupec: `Name` .
        * Zobrazovací jednotka sloupce: `Text` .
    * V *centrovém obsahu*nastavte:
        * Použít sloupec: `Calls`
        * Zobrazovací jednotka sloupce: `Big Number`
        * Barevná paleta: `None`
    * V *dolním obsahu*nastavte:
        * Použít sloupec: `Kind`
        * Zobrazovací jednotka sloupce: `Text` .
9. V dolní části podokna vyberte **tlačítko Uložit a zavřít** .

[![Snímek obrazovky znázorňující vytvoření vizualizace grafu pomocí vybarvení základních uzlů pole](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Grafy také podporují vykreslování kompozitních pruhů. Další informace najdete v [dokumentaci ke kompozitním panelům](workbooks-composite-bar.md).
* Přečtěte si další informace o [zdrojích dat](workbooks-data-sources.md) , které můžete použít v sešitech.
