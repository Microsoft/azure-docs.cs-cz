---
title: Vizualizace sešitu Azure Monitor
description: Seznamte se se všemi komponentami v Azure Monitorch vizualizacích sešitu, včetně textových, grafů, mřížek, stromů a grafů.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658026"
---
# <a name="azure-monitor-workbook-visualizations"></a>Vizualizace sešitu Azure Monitor

Azure Monitor sešity podporují různé styly vizualizací, které se vejdou na požadavky vytváření sestav. Tento článek popisuje příklady jednotlivých typů vizualizací.

## <a name="text"></a>Text

Sešity umožňují autorům zahrnout do svých sešitů textové bloky. Text může být lidská analýza telemetrie, informace, které uživatelům pomůžou interpretovat vaše data, nadpisy oddílů atd.

![Snímek obrazovky s Apdexu tabulkou textu](./media/workbooks-visualizations/apdex.png)

Text je přidán prostřednictvím ovládacího prvku Markdownu, který poskytuje úplné formátování ovládacího prvku.

![Snímek obrazovky nezpracovaného markdownuu, který vytvoří vykreslenou tabulku](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Přidání ovládacího prvku text

1. Kliknutím na položku panelu nástrojů pro **Úpravy** přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku text do sešitu použijte odkaz **Přidat text** .
3. Přidejte Markdownu do ovládacího prvku.
4. Formátovaný text zobrazíte kliknutím na tlačítko **hotové úpravy** .

> [!TIP]
> Pomocí tohoto [listu Markdownu tahák](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) se dozvíte o různých možnostech formátování.

## <a name="charts"></a>Grafy

Pracovní sešity umožňují, aby se data monitorování mohla prezentovat jako grafy. Mezi podporované typy grafů patří spojnicový, pruhový, pruhový kategorií, plošný, bodový graf, výsečový a čas. Autoři si můžou zvolit, že se má přizpůsobit výška, Šířka, paleta barev, legenda, názvy, zpráva bez dat atd. v grafu.

Pracovní sešity podporují grafy pro protokoly i zdroje dat metriky. 

### <a name="adding-a-log-chart"></a>Přidání grafu protokolu

1. Kliknutím na položku panelu nástrojů pro **Úpravy** přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku dotaz na protokol do sešitu použijte odkaz **Přidat dotaz** .
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte [KQL](https://docs.microsoft.com/azure/kusto/query/) pro analýzu (například trend požadavků).
5. Nastavte vizualizaci na jednu z těchto: **oblast**, **pruhový**, **pruhový (kategorií)**, **spojnicový**, **výsečový**, **bodový**nebo **čas**.
6. V případě potřeby nastavte další parametry jako časový rozsah, vizualizaci, velikost, paletu barev a legendu.

![Snímek obrazovky s grafem protokolu v režimu úprav](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parametry grafu protokolu

| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ dotazu, který se má použít | Log, Azure Resource Graph atd. |
| `Resource Type` | Typ prostředku, který se má cílit | Nejdřív Application Insights, Log Analytics nebo Azure – |
| `Resources` | Sada prostředků, ze kterých se má získat hodnota metrik | MyApp1 |
| `Time Range` | Časový interval pro zobrazení grafu protokolu | Poslední hodina, posledních 24 hodin atd. |
| `Visualization` | Vizualizace, která se má použít | Oblast, pruhový, spojnicový, výsečový, bodový, čas, pruhový kategorií |
| `Size` | Svislá velikost ovládacího prvku | Malá, střední, Velká nebo plná |
| `Color palette` | Paleta barev, která se má použít v grafu Ignorováno v režimu s více metrikami nebo v režimu segmentace. | Modrá, zelená, červená atd. |
| `Legend` | Agregační funkce, která se má použít pro legendu | Součet nebo průměr hodnot nebo maximum, minimum, první, poslední hodnota |
| `Query` | Libovolný dotaz KQL, který vrací data ve formátu očekávaném vizualizací grafu | _vyžádá požadavky \| -Series = Count () výchozí nastavení = 0 v časovém razítku před (1d) do Now () krok 1H_ |

### <a name="adding-a-metric-chart"></a>Přidání grafu metriky

1. Kliknutím na položku panelu nástrojů pro **Úpravy** přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku metriky do sešitu použijte odkaz **Přidat metriku** .
3. Vyberte typ prostředku (například účet úložiště), prostředky k cíli, obor názvů metriky a název a agregaci, která se má použít.
4. Nastavte další parametry, pokud je to potřeba, jako časový rozsah, rozdělené, vizualizace, velikost a paleta barev.

![Snímek grafu metriky v režimu úprav](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parametry grafu metriky

| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `Resource Type` | Typ prostředku, který se má cílit | Úložiště nebo virtuální počítač. |
| `Resources` | Sada prostředků, ze kterých se má získat hodnota metrik | MyStorage1 |
| `Namespace` | Obor názvů s metrikou | Objekt BLOB > úložiště |
| `Metric` | Metrika, která se má vizualizovat | Transakce > BLOB > úložiště |
| `Aggregation` | Agregační funkce, která se má použít pro metriku | Sum, Count, Average atd. |
| `Time Range` | Časový interval pro zobrazení metriky | Poslední hodina, posledních 24 hodin atd. |
| `Visualization` | Vizualizace, která se má použít | Plošný, pruhový, spojnicový, bodový, mřížka |
| `Split By` | Volitelně rozdělit metriku na dimenzi | Transakce podle geografického typu |
| `Size` | Svislá velikost ovládacího prvku | Malá, střední nebo Velká |
| `Color palette` | Paleta barev, která se má použít v grafu Ignoruje se, pokud se `Split by` použije parametr. | Modrá, zelená, červená atd. |

## <a name="grids"></a>Mřížky

Mřížky nebo tabulky představují běžný způsob, jak prezentovat data uživatelům. Pracovní sešity umožňují uživatelům individuálně naformátovat sloupce mřížky, aby poskytovaly bohatou uživatelské rozhraní pro své sestavy.

Níže uvedený příklad ukazuje mřížku, která kombinuje ikony, Heat mapy a Spark-bar a prezentuje komplexní informace. Sešit také nabízí řazení, vyhledávací pole a tlačítko Přejít na analýzu.

![Snímek obrazovky s tabulkou založenou na protokolu](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Přidání mřížky založené na protokolu

1. Kliknutím na položku panelu nástrojů pro **Úpravy** přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku dotaz na protokol do sešitu použijte odkaz **Přidat dotaz** .
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu (například virtuální počítače s pamětí pod prahovou hodnotou).
5. Nastavení vizualizace na **mřížku**
6. V případě potřeby nastavte další parametry, jako je třeba časový rozsah, velikost, paleta barev a legenda.

![Snímek obrazovky s dotazem na mřížku založenou na protokolu](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Dlaždice

Dlaždice představují velmi užitečný způsob, jak prezentovat souhrnná data v sešitech. Následující obrázek ukazuje běžný případ použití dlaždic – souhrn na úrovni aplikace nad detailní mřížkou.

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/tiles-summary.png)

Podpora dlaždic sešitu zobrazuje název, Podnadpis, velký text, ikony, přechody na bázi metrik, sparkovou čáru/pruhy, zápatí atd.

### <a name="adding-a-tile"></a>Přidání dlaždice

1. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku dotaz na protokol do sešitu použijte odkaz **Přidat dotaz** . 
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu.
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Nastavit velikost na **Full**
6. Nastavení vizualizace na **dlaždice**
7. Kliknutím na tlačítko **nastavení dlaždice** otevřete podokno nastavení.
8. V **poli dlaždice**nastavte:
    * Hlava`name`
    * Left: `Requests` , rendererer: `Big Number` , barevná paleta: `Green to Red` , minimální hodnota:`0`
    * Dolů`appName`
9. Klikněte na tlačítko **Uložit a zavřít** v dolní části podokna.

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/tile-settings.png)

Tímto způsobem budou dlaždice vypadat v režimu čtení:

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Předmětů

Pracovní sešity podporují hierarchická zobrazení prostřednictvím stromové mřížky. Stromy umožňují rozšířit některé řádky na další úroveň pro prostředí pro přechod k podrobnostem.

Následující příklad ukazuje metriky stavu kontejneru (velikost pracovní sady), která je znázorněna jako mřížka stromu. Uzly nejvyšší úrovně jsou uzly Azure Kubernetes Service (AKS), další úroveň je lusky a konečná úroveň jsou kontejnery. Všimněte si, že stále můžete formátovat sloupce jako v mřížce (heatmapu, ikony, odkaz). Podkladový zdroj dat v tomto případě je Log Analytics pracovní prostor s protokoly AKS.

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Přidání mřížky stromu
1. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku dotaz na protokol do sešitu použijte odkaz **Přidat dotaz** . 
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu.
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
6. Kliknutím na tlačítko **Nastavení sloupce** otevřete podokno nastavení.
7. V části **strom/seskupení podle nastavení** v dolní části nastavte:
    * Typ stromu:`Parent/Child`
    * Pole ID:`Id`
    * Nadřazené pole ID:`ParentId`
    * Zobrazit rozšíření na:`Name`
    * Rozbalte nejvyšší úroveň stromu:`checked`
8. V části _sloupce_ nahoře nastavte:
    * _ID_ – vykreslovací modul sloupců:`Hidden`
    * _Nadřazené ID_ – vykreslovací modul sloupců:`Hidden`
    * _Požadavky_ – vykreslovací modul sloupců: `Bar` , barva: `Blue` , minimální hodnota:`0`
9. Klikněte na tlačítko _Uložit a zavřít_ v dolní části podokna.    

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Nastavení stromu

| Nastavení | Vysvětlení |
|:------------- |:-------------|
| `Id Field` | Jedinečné ID každého řádku v mřížce |
| `Parent Id Field` | ID nadřazeného prvku aktuálního řádku |
| `Show the expander on` | Sloupec, ve kterém se má zobrazit rozšíření stromu Je běžné, že stromové mřížky skryjí své ID a nadřazené pole ID, protože nejsou velmi čitelné. Místo toho se rozšíření zobrazuje na poli s čitelnější hodnotou, jako je název entity. |
| `Expand the top level of the tree` | Pokud je zaškrtnuto, bude se stromová mřížka rozbalí na nejvyšší úrovni. Užitečné, pokud chcete ve výchozím nastavení zobrazit další informace |

## <a name="graphs"></a>Diagram

Pracovní sešity podporují vizualizaci libovolného grafu na základě dat z protokolů, aby se zobrazily vztahy mezi entitami monitorování.

Následující graf ukazuje tok dat v/v počítači prostřednictvím různých portů do/z externích počítačů. Je barevný podle typu (počítač vs. port vs. externí IP adresa) a velikosti hran odpovídají množství dat toků mezi. Podkladová data pocházejí z KQL dotazů cílících na připojení virtuálních počítačů.

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Přidání grafu
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
7. Nastavení vizualizace na **graf**
8. Kliknutím na tlačítko **nastavení grafu** otevřete podokno nastavení.
9. V dolní části _pole rozložení_ nastavte:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. V _nastavení formát uzlu_ v horní části nastavte:
    * _Horní sloupec obsahu_– použití: `Name` , Renderer sloupce:`Text`
    * _Center Content_-use Column: `Calls` , Renderer sloupců: `Big Number` , barevná paleta:`None`
    * _Dolní obsah_– sloupec použití: `Kind` , zobrazovací sloupec:`Text`
10. Klikněte na tlačítko _Uložit a zavřít_ v dolní části podokna.

![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Další kroky

* [Nasaďte](workbooks-automate.md) sešit pomocí Azure Resource Manager.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.
