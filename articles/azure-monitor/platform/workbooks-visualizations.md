---
title: Vizualizace sešitu Azure Monitoru
description: Seznamte se se všemi součástmi vizualizací sešitu Azure Monitor, včetně textu, grafů, mřížek, stromů a grafů.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658026"
---
# <a name="azure-monitor-workbook-visualizations"></a>Vizualizace sešitu Azure Monitoru

Sešity Azure Monitoru podporují řadu různých stylů vizualizací, aby vyhovovaly vašim potřebám vytváření sestav. Tento článek obsahuje příklady jednotlivých typů vizualizací.

## <a name="text"></a>Text

Sešity umožňují autorům zahrnout textové bloky do sešitů. Text může být lidská analýza telemetrie, informace, které pomáhají uživatelům interpretovat vaše data, nadpisy oddílů atd.

![Snímek obrazovky s textovou tabulkou Apdex](./media/workbooks-visualizations/apdex.png)

Text je přidán pomocí ovládacího prvku Markdown, který poskytuje úplné ovládání formátování.

![Snímek obrazovky s nezpracovanými markdowny, který vytváří vykreslenou tabulku](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Přidání textového ovládacího prvku

1. Přepnutí sešitu do režimu úprav kliknutím na položku panelu nástrojů **úpravy**
2. Pomocí textového odkazu **Přidat** přidejte do sešitu textový ovládací prvek.
3. Přidejte Markdown do ovládacího prvku.
4. Kliknutím na tlačítko **Hotovo úpravy** zobrazíte formátovaný text.

> [!TIP]
> Pomocí tohoto [informačního listu o značkách](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) se dozvíte o různých možnostech formátování.

## <a name="charts"></a>Grafy

Sešity umožňují zobrazení dat monitorování jako grafů. Mezi podporované typy grafů patří spojnicový, pruhový, pruhový kategorický, plošný graf, výsečový graf a čas. Autoři si mohou vybrat přizpůsobit výšku, šířku, paletu barev, legendu, názvy, bezdatovou zprávu atd.

Sešity podporují grafy pro protokoly i zdroje dat metriky. 

### <a name="adding-a-log-chart"></a>Přidání protokolu

1. Přepnutí sešitu do režimu úprav kliknutím na položku panelu nástrojů **úpravy**
2. Pomocí odkazu **Přidat dotaz** přidejte do sešitu ovládací prvek dotazu protokolu.
3. Vyberte typ dotazu jako **Protokol**, typ prostředku (například Application Insights) a prostředky, na které chcete cílit.
4. Pomocí editoru dotazů zadejte [kql](https://docs.microsoft.com/azure/kusto/query/) pro analýzu (například trend požadavků).
5. Nastavte vizualizaci na jednu z: **Oblast**, **Pruh**, **Pruh (kategorický)**, **Čára**, **Výsečový**, **Bodový**nebo **Čas**.
6. V případě potřeby nastavte další parametry - například časový rozsah, vizualizaci, velikost, paletu barev a legendu.

![Snímek obrazovky s protokolovým grafem v režimu úprav](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parametry protokolu grafu

| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ dotazu, který se má použít | Protokol, Azure Resource Graph, atd. |
| `Resource Type` | Typ prostředku, na který se má cílit | Přehledy aplikací, analýza protokolů nebo Azure |
| `Resources` | Sada zdrojů, ze kterých můžete získat hodnotu metrik | Aplikace MyApp1 |
| `Time Range` | Časové okno pro zobrazení protokolu grafu | Poslední hodina, Posledních 24 hodin, atd. |
| `Visualization` | Vizualizace, která se má použít | Plocha, Pruh, Čára, Výseč, Bodový, Čas, pruh kategorický |
| `Size` | Svislá velikost ovládacího prvku | Malé, střední, velké nebo plné |
| `Color palette` | Paleta barev, která se má použít v grafu. Ignorováno ve vícemetrovém nebo segmentovaném režimu. | Modrá, zelená, červená atd. |
| `Legend` | Funkce agregace, která se má použít pro legendu | Součet nebo Průměr hodnot nebo Max, Min, První, Poslední hodnota |
| `Query` | Libovolný dotaz KQL, který vrací data ve formátu očekávaném vizualizací grafu | _požadavky \| make-series Požadavky = count() výchozí = 0 na časové razítko od ago(1d) do now() krok 1h_ |

### <a name="adding-a-metric-chart"></a>Přidání metrického grafu

1. Přepnutí sešitu do režimu úprav kliknutím na položku panelu nástrojů **úpravy**
2. Pomocí odkazu **Přidat metriku** přidejte do sešitu ovládací prvek metriky.
3. Vyberte typ prostředku (například účet úložiště), prostředky, na které se má cílit, obor a název metriky a agregaci, která se má použít.
4. V případě potřeby nastavte další parametry – například časový rozsah, rozdělení, vizualizace, velikost a paletu barev.

![Snímek obrazovky s metrickým grafem v režimu úprav](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parametry grafu metriky

| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `Resource Type` | Typ prostředku, na který se má cílit | Úložiště nebo virtuální počítač. |
| `Resources` | Sada zdrojů, ze kterých můžete získat hodnotu metrik | Úložiště My1 |
| `Namespace` | Obor názvů s metrikou | Objekt blob > úložiště |
| `Metric` | Metrika pro vizualizaci | > Transakce > objektů blob pro ukládání do úložiště |
| `Aggregation` | Funkce agregace, která se má použít pro metriku | Součet, počet, průměr atd. |
| `Time Range` | Časové okno pro zobrazení metriky v | Poslední hodina, Posledních 24 hodin, atd. |
| `Visualization` | Vizualizace, která se má použít | Plocha, Bar, Čára, Bodový, Mřížka |
| `Split By` | Volitelně rozdělit metriku na dimenzi | Transakce podle typu Geo |
| `Size` | Svislá velikost ovládacího prvku | Malé, střední nebo velké |
| `Color palette` | Paleta barev, která se má použít v grafu. Ignorováno, `Split by` pokud je použit parametr | Modrá, zelená, červená atd. |

## <a name="grids"></a>Mřížky

Mřížky nebo tabulky představují běžný způsob prezentace dat uživatelům. Sešity umožňují uživatelům jednotlivě stylizovat sloupce mřížky a poskytovat tak bohaté uživatelské uživatelské místo pro své sestavy.

Následující příklad ukazuje mřížku, která kombinuje ikony, heatmapy a zapalovací panely a prezentuje složité informace. Sešit také poskytuje řazení, vyhledávací pole a tlačítko pro analýzu.

![Snímek obrazovky mřížky založené na protokolu](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Přidání mřížky založené na protokolu

1. Přepnutí sešitu do režimu úprav kliknutím na položku panelu nástrojů **úpravy**
2. Pomocí odkazu **Přidat dotaz** přidejte do sešitu ovládací prvek dotazu protokolu.
3. Vyberte typ dotazu jako **Protokol**, typ prostředku (například Application Insights) a prostředky, na které chcete cílit.
4. Pomocí editoru dotazů zadejte KQL pro analýzu (například virtuální počítače s pamětí pod prahovou hodnotou)
5. Nastavení vizualizace na **mřížku**
6. V případě potřeby nastavte další parametry - například časový rozsah, velikost, paletu barev a legendu.

![Snímek obrazovky s mřížkou založeným na protokolu](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Dlaždice

Dlaždice představují velmi užitečný způsob prezentace souhrnných dat v sešitech. Na obrázku níže je zobrazen běžný případ použití dlaždic – souhrn na úrovni aplikace nad podrobnou mřížkou.

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/tiles-summary.png)

Dlaždice sešitu podporují zobrazení nadpisu, titulků, velkého textu, ikon, přechodů založených na metrikách, minigrafů/pruhů, zápatí atd.

### <a name="adding-a-tile"></a>Přidání dlaždice

1. Přepnutí sešitu do režimu úprav kliknutím na položku panelu nástrojů _úpravy_
2. Pomocí odkazu **Přidat dotaz** přidejte do sešitu ovládací prvek dotazu protokolu. 
3. Vyberte typ dotazu jako **Protokol**, typ prostředku (například Application Insights) a prostředky, na které chcete cílit.
4. Zadání kql pro analýzu pomocí editoru dotazů
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Nastavit velikost na **plnou**
6. Nastavení vizualizace na **dlaždice**
7. Kliknutím na tlačítko **Nastavení dlaždice** otevřete podokno nastavení.
8. V **dlaždicových polích**nastavte:
    * Název:`name`
    * Vlevo: `Requests`, Renderer: `Big Number`, `Green to Red`Paleta barev: , Min Hodnota:`0`
    * Dolní části:`appName`
9. Klepněte na tlačítko **Uložit a zavřít** v dolní části podokna.

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/tile-settings.png)

Takto budou dlaždice vypadat v režimu čtení:

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Stromy

Sešity podporují hierarchická zobrazení prostřednictvím stromových mřížek. Stromy umožňují některé řádky, které mají být rozbalitelné do další úrovně pro přechod k podrobnostem.

Následující příklad ukazuje metriky stavu kontejneru (velikost pracovní sady) vizualizované jako mřížka stromu. Uzly nejvyšší úrovně jsou uzly Služby Azure Kubernetes (AKS), další úroveň jsou pody a konečná úroveň jsou kontejnery. Všimněte si, že stále můžete formátovat sloupce jako v mřížce (heatmap, ikony, odkaz). Podkladovým zdrojem dat v tomto případě je pracovní prostor Analýzy protokolů s protokoly AKS.

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Přidání stromové mřížky
1. Přepnutí sešitu do režimu úprav kliknutím na položku panelu nástrojů _úpravy_
2. Pomocí odkazu **Přidat dotaz** přidejte do sešitu ovládací prvek dotazu protokolu. 
3. Vyberte typ dotazu jako **Protokol**, typ prostředku (například Application Insights) a prostředky, na které chcete cílit.
4. Zadání kql pro analýzu pomocí editoru dotazů
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Nastavení vizualizace na **mřížku**
6. Klepnutím na tlačítko **Nastavení sloupců** otevřete podokno nastavení.
7. V části **Strom/Seskupit podle nastavení** v dolní části nastavte:
    * Typ stromu:`Parent/Child`
    * Id Pole:`Id`
    * Pole ID rodiče:`ParentId`
    * Zobrazit rozbalovací tlačítko zapnuto:`Name`
    * Rozbalte nejvyšší úroveň stromu:`checked`
8. V části _Sloupce_ nahoře nastavte:
    * _ID_ - Vykreslovač sloupců:`Hidden`
    * _Id nadřazeného objektu_ - vykreslovač sloupců:`Hidden`
    * Požadavky - _Vykreslovač_ sloupců: `Bar`, Barva: `Blue`, Minimální hodnota:`0`
9. Klepněte na tlačítko _Uložit a zavřít_ v dolní části podokna.    

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Nastavení stromu

| Nastavení | Vysvětlení |
|:------------- |:-------------|
| `Id Field` | Jedinečné ID každého řádku v mřížce |
| `Parent Id Field` | Id nadřazeného řádku |
| `Show the expander on` | Sloupec, na kterém chcete zobrazit rozšíření stromu. Je běžné, že mřížky stromu skrýt jejich id a nadřazené id pole, protože nejsou příliš čitelné. Místo toho se rozbalovací jednotka zobrazí v poli s čitelnější hodnotou – například na název entity |
| `Expand the top level of the tree` | Pokud je zaškrtnuto, mřížka stromu bude rozšířena na nejvyšší úrovni. Užitečné, pokud chcete zobrazit více informací ve výchozím nastavení |

## <a name="graphs"></a>Grafy

Sešity podporují vizualizaci libovolných grafů založených na datech z protokolů, aby se zobrazily vztahy mezi monitorovacími entitami.

Níže uvedený graf ukazuje data proudící dovnitř / ven z počítače přes různé porty do / z externích počítačů. Je zbarven podle typu (počítač vs. port vs. externí IP) a velikosti okrajů odpovídají množství dat, která proudí mezi nimi. Podkladová data pocházejí z dotazu KQL zaměřeného na připojení virtuálních můek.

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Přidání grafu
1. Přepnutí sešitu do režimu úprav kliknutím na položku panelu nástrojů _úpravy_
2. Pomocí odkazu **Přidat dotaz** přidejte do sešitu ovládací prvek dotazu protokolu. 
3. Vyberte typ dotazu jako **Protokol**, typ prostředku (například Application Insights) a prostředky, na které chcete cílit.
4. Zadání kql pro analýzu pomocí editoru dotazů
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
7. Nastavení vizualizace na **graf**
8. Klepnutím na tlačítko **Nastavení grafu** otevřete podokno nastavení.
9. V _poli rozložení_ dole nastavte:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. V _nastavení formátu uzlu_ v horní části nastavte:
    * _Nejvyšší obsah_- `Name`Použít sloupec: , Vykreslovač sloupců:`Text`
    * _Středový obsah_- `Calls`Použít sloupec: `Big Number`, Vykreslovač sloupců: , Paleta barev:`None`
    * _Dolní obsah_- `Kind`Použít sloupec: , Vykreslovač sloupců:`Text`
10. Klepněte na tlačítko _Uložit a zavřít_ v dolní části podokna.

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Další kroky

* [Nasazujte](workbooks-automate.md) sešity pomocí Správce prostředků Azure.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu
