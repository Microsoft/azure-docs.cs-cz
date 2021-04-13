---
title: Přehled sešitů Azure Monitor
description: Naučte se, jak sešity poskytují flexibilní plátno pro analýzu dat a vytváření bohatých vizuálních sestav v rámci Azure Portal.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: a02e5fced0a9e338a32d8d8beaa9e4b5fca994e8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309469"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor sešity

Sešity poskytují flexibilní plátno pro analýzu dat a vytváření bohatých vizuálních sestav v rámci webu Azure Portal. Umožňují využívat více zdrojů dat v rámci Azure a kombinovat je do sjednocených interaktivních prostředí.

Tady je video s návodem k vytváření sešitů.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Zdroje dat

Sešity se můžou dotazovat na data z několika zdrojů v rámci Azure. Autoři sešitů můžou tato data transformovat, aby poskytovala přehledy o dostupnosti, výkonu, využití a celkovém stavu podkladových komponent. Například analýza protokolů výkonu z virtuálních počítačů za účelem identifikace vysokého procesoru nebo nedostatku paměti a zobrazení výsledků v interaktivní sestavě jako mřížku.
  
Ale skutečná síla sešitů je schopnost kombinovat data z různorodých zdrojů v rámci jedné sestavy. To umožňuje vytváření složených zobrazení prostředků nebo spojení mezi prostředky, které umožňují rozsáhlejší data a přehledy, které by jinak nebyly možné.

Sešity jsou aktuálně kompatibilní s následujícími zdroji dat:

* [Protokoly](../visualize/workbooks-data-sources.md#logs)
* [Metriky](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Výstrahy (Preview)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Stav úlohy](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Vizualizace

Pracovní sešity poskytují bohatou sadu funkcí pro vizualizaci vašich dat. Podrobné příklady jednotlivých typů vizualizací můžete získat z následujících odkazů:

* [Text](../visualize/workbooks-text-visualizations.md)
* [Grafy](../visualize/workbooks-chart-visualizations.md)
* [Mřížky](../visualize/workbooks-grid-visualizations.md)
* [Dlaždice](../visualize/workbooks-tile-visualizations.md)
* [Předmětů](../visualize/workbooks-tree-visualizations.md)
* [Diagram](../visualize/workbooks-graph-visualizations.md)
* [Složený pruhový](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Příklad vizualizací sešitu" border="false" lightbox="./media/workbooks-overview/visualizations.png":::

## <a name="getting-started"></a>Začínáme

Chcete-li prozkoumat prostředí sešitů, nejprve přejděte do služby Azure Monitor. To se dá udělat tak, že do vyhledávacího pole v Azure Portal zadáte **monitor** .

Pak vyberte **sešity**.

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Snímek obrazovky se zvýrazněným tlačítkem sešity v červeném poli" border="false":::

### <a name="gallery"></a>Galerie

Galerie usnadňuje uspořádání, řazení a správu sešitů všech typů.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Snímek obrazovky galerie na kartě vše" lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Karty galerie

V galerii jsou čtyři karty, které vám pomůžou organizovat typy sešitů.

| Karta              | Description                                       |
|------------------|---------------------------------------------------|
| Vše | Zobrazuje horní čtyři položky pro každý typ – sešity, veřejné šablony a moje šablony. Sešity jsou seřazené podle data změny, takže se zobrazí poslední osm upravených sešitů.|
| Workbooks | Zobrazuje seznam všech dostupných sešitů, které jste vytvořili nebo jsou s vámi sdíleny. |
| Veřejné šablony | Zobrazí seznam všech dostupných připravených k použití, začněte pracovat s funkčními šablonami publikovanými společností Microsoft. Seskupeno podle kategorie. |
| Moje šablony | Zobrazuje seznam všech dostupných šablon nasazených sešitů, které jste vytvořili nebo jsou s vámi sdíleny. Seskupeno podle kategorie. |

#### <a name="features"></a>Funkce

* Na každé kartě je k dispozici mřížka s informacemi v sešitech. Obsahuje popis, datum poslední změny, značky, předplatné, skupinu prostředků, oblast a sdílený stav. Tyto sešity můžete také seřadit podle těchto informací.
* Filtrovat podle skupiny prostředků, předplatných, názvu sešitu nebo šablony nebo kategorie šablony
* Vyberte více sešitů, které chcete odstranit nebo hromadně odstranit.
* Každý sešit má kontextovou nabídku (tři tečky/tři tečky na konci), výběr se otevře a zobrazí se seznam rychlých akcí.
    * Zobrazte kartu prostředek sešitu přístupu k prostředkům, kde můžete zobrazit ID prostředku sešitu, přidat značky, spravovat zámky atd.
    * Odstraňte nebo přejmenujte sešit.
    * Připnout sešit na řídicí panel

### <a name="workbooks-versus-workbook-templates"></a>Sešity oproti šablonám sešitu

_Sešit_ se zobrazí zeleně a řada _šablon sešitu_ se nachová. Šablony slouží jako obpracované sestavy navržené pro flexibilní opakované použití více uživatelů a týmů. Při otevření šablony se vytvoří přechodný sešit naplněný obsahem šablony.

Můžete upravit parametry sešitu založeného na šablonách a provádět analýzy bez obav, že bude možné budoucí generování sestav pro kolegy. Pokud otevřete šablonu, proveďte nějaké úpravy a potom vyberte ikonu Uložit, kterou uložíte šablonu jako sešit, který se pak zobrazí zeleně, když původní šablona zůstane beze změny.

V digestoři se šablony také liší od uložených sešitů. Při uložení sešitu se vytvoří přidružený prostředek Azure Resource Manager, zatímco přechodný sešit vytvořený při pouhém otevření šablony nemá přidružený žádný jedinečný prostředek. Další informace o tom, jak se řízení přístupu spravuje v sešitech, najdete v článku věnovaném [řízení přístupu do sešitů](../visualize/workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Seznámení s šablonou sešitu

Vyberte možnost **Analýza selhání aplikace** , aby se zobrazila jedna z výchozích šablon sešitu aplikace.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Snímek šablony analýzy selhání aplikace" border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Jak bylo uvedeno dříve, otevřením šablony se vytvoří dočasný sešit, ve kterém budete moci pracovat. Ve výchozím nastavení se sešit otevře v režimu čtení, který zobrazí jenom informace pro zamýšlené prostředí analýzy, které vytvořil původní autor šablony.

V případě tohoto konkrétního sešitu je prostředí interaktivní. Můžete upravit předplatné, cílové aplikace a časový rozsah dat, která chcete zobrazit. Po provedení těchto výběrů je mřížka požadavků HTTP také interaktivní, a to tak, že výběr jednotlivého řádku změní data vykreslená v obou grafech v dolní části sestavy.

### <a name="editing-mode"></a>Režim úprav

Chcete-li pochopit, jak je šablona sešitu seznáma, je nutné přepnout do režimu úprav výběrem možnosti **Upravit**.

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Snímek obrazovky s tlačítkem Upravit v sešitech" border="false" :::

Po přepnutí do režimu úprav si všimněte, že se vpravo v jednotlivých aspektech vašeho sešitu zobrazí několik **textových polí.**

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Snímek obrazovky s tlačítkem Upravit" border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Když v mřížce dat požadavku vybereme tlačítko Upravit hned, uvidíme, že tato část sešitu se skládá z Kusto dotazu na data z prostředku Application Insights.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Snímek obrazovky základního dotazu Kusto" border="false" lightbox="./media/workbooks-overview/kusto.png":::


Kliknutím na další tlačítka pro **Úpravy** na pravé straně se zobrazí řada základních komponent, které tvoří sešity, jako jsou [textová pole](../visualize/workbooks-text-visualizations.md)založená na Markdownu, prvky uživatelského rozhraní pro [Výběr parametrů](../visualize/workbooks-parameters.md) a další [typy grafů a vizualizací](#visualizations).

Seznamte se s předem vytvořenými šablonami v režimu úprav a pak je upravte tak, aby vyhovovaly vašim potřebám, a uložte si vlastní sešit je skvělým způsobem, jak se naučit, co je možné u Azure Monitorch sešitů.

## <a name="pinning-visualizations"></a>Připnutí vizualizací

Kroky pro text, dotazy a metriky v sešitě lze připnout pomocí tlačítka připnout na tyto položky, když je sešit v režimu připnutí, nebo pokud autor sešitu povolil nastavení pro tento prvek, aby ikona kódu PIN byla viditelná.

Chcete-li získat přístup k režimu připnutí, klikněte na tlačítko **Upravit** a zadejte režim úprav a v horním panelu vyberte ikonu modrý kód PIN. Na pravé straně obrazovky se pak zobrazí ikona jednotlivého kódu PIN nad každým odpovídajícím polem pro *úpravu* příslušné části sešitu.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Snímek obrazovky s možností připnutí" border="false":::

> [!NOTE]
> Stav sešitu se uloží v době kódu PIN a připojené sešity na řídicím panelu se neaktualizují, pokud je upravený příslušný sešit. Aby bylo možné aktualizovat připnuté součásti sešitu, je nutné tuto část odstranit a znovu připnout.

## <a name="dashboard-time-ranges"></a>Časové rozsahy řídicího panelu

Připnuté části dotazu na sešit budou respektovat časový rozsah řídicího panelu, pokud je připojená položka nakonfigurována k použití parametru *časového rozsahu* . Hodnota časového rozsahu řídicího panelu se použije jako hodnota parametru časového rozsahu a jakákoli změna rozsahu času řídicího panelu způsobí aktualizaci připnuté položky. Pokud připojená část používá časový rozsah řídicího panelu, zobrazí se při každém změně časového rozsahu podnadpis připnuté části aktualizace, aby se zobrazil časový rozsah řídicího panelu.

Připnuté části sešitu s použitím parametru časového rozsahu se automaticky aktualizují rychlostí určenou časovým rozsahem řídicího panelu. Čas posledního spuštění dotazu se zobrazí v podnadpisu připnuté části.

Pokud připnutý krok explicitně nastavil časový rozsah (nepoužívá parametr časového rozsahu), bude tento časový rozsah vždy použit pro řídicí panel bez ohledu na nastavení řídicího panelu. Podnadpis připnuté části nezobrazuje časový rozsah řídicího panelu a dotaz na řídicím panelu nebude automaticky aktualizován. V podnadpisu se zobrazí čas posledního spuštění dotazu.

> [!NOTE]
> Dotazy, které používají zdroj dat *sloučení* , se v současné době připnutí k řídicím panelům aktuálně nepodporují.

## <a name="sharing-workbook-templates"></a>Sdílení šablon sešitu

Po zahájení vytváření vlastních šablon sešitu ho můžete chtít sdílet s širší komunitou. Pokud chcete získat další informace a prozkoumat jiné šablony, které nejsou součástí výchozího zobrazení galerie Azure Monitor, navštivte naše [úložiště GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Pokud chcete procházet existující sešity, přejděte do [knihovny sešitů](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) na GitHubu.

## <a name="next-step"></a>Další krok

* [Začínáme](#visualizations) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.
* [Řízení](../visualize/workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.