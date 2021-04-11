---
title: Začínáme s Průzkumníkem metrik Azure
description: Naučte se, jak vytvořit první graf metriky pomocí Průzkumníka metrik Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.openlocfilehash: df745e7612dbd5b5bb9029b89d7f74974270c2d1
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962709"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Začínáme s Průzkumníkem metrik Azure

## <a name="where-do-i-start"></a>Kde mám začít
Azure Monitor Průzkumník metrik je součástí portál Microsoft Azure, která umožňuje vykreslovat grafy, vizuálně korelovat trendy a zkoumat špičky a rozmítají metriky v hodnotách metriky. Pomocí Průzkumníka metrik můžete prozkoumat stav a využití vašich prostředků. Začněte v tomto pořadí:

1. [Vyberte prostředek a metriku](#create-your-first-metric-chart) a uvidíte základní graf. Pak [vyberte časový rozsah](#select-a-time-range) , který je pro vaše šetření relevantní.

1. Zkuste [použít filtry dimenzí a rozdělení](#apply-dimension-filters-and-splitting). Filtry a rozdělení umožňují analyzovat, které segmenty metriky přispívají k celkové hodnotě metriky a identifikují možné odlehlé hodnoty.

1. Před připnutím k řídicím panelům můžete graf přizpůsobit pomocí [rozšířených nastavení](#advanced-chart-settings) . [Nakonfigurujte výstrahy](../alerts/alerts-metric-overview.md) pro příjem oznámení, když hodnota metriky překročí nebo klesne pod prahovou hodnotu.

## <a name="create-your-first-metric-chart"></a>Vytvoření prvního grafu metriky

Pokud chcete vytvořit graf metriky, ze svého prostředku, skupiny prostředků, předplatného nebo Azure Monitorho zobrazení, otevřete kartu **metriky** a proveďte následující kroky:

1. Klikněte na tlačítko Vybrat obor a otevřete tak výběr oboru prostředků. To vám umožní vybrat prostředky, pro které chcete zobrazit metriky. Pokud jste v nabídce prostředku spustili průzkumníka metrik, prostředek by se už měl naplnit. Pokud se chcete dozvědět, jak zobrazit metriky napříč více prostředky, [Přečtěte si tento článek](./metrics-dynamic-scope.md).
    > ![Výběr prostředku](./media/metrics-getting-started/scope-picker.png)

2. U některých prostředků je nutné vybrat obor názvů. Obor názvů je jenom způsob, jak uspořádat metriky, abyste je mohli snadno najít. Například účty úložiště mají samostatné obory názvů pro ukládání souborů, tabulek, objektů BLOB a metriky front. Mnoho typů prostředků má pouze jeden obor názvů.

3. Vyberte metriku ze seznamu dostupných metrik.

    > ![Vyberte metriku](./media/metrics-getting-started/metrics-dropdown.png)

4. Volitelně můžete [změnit agregaci metriky](../essentials/metrics-charts.md#aggregation). Můžete například chtít, aby v grafu bylo možné zobrazit minimální, maximální nebo průměrné hodnoty metriky.

> [!TIP]
> Použijte tlačítko **Přidat metriku** a opakujte tyto kroky, pokud chcete zobrazit více metrik vykreslených ve stejném grafu. U více grafů v jednom zobrazení vyberte tlačítko **přidat graf** nahoře.

## <a name="select-a-time-range"></a>Vyberte časový rozsah.

> [!WARNING]
> [Většina metrik v Azure se ukládá po dobu 93 dnů](../essentials/data-platform-metrics.md#retention-of-metrics). Nemůžete ale zadat dotaz na data na libovolný graf na více než 30 dnů. Chcete-li zobrazit úplné uchovávání, můžete graf [Posunout](metrics-charts.md#pan) . Omezení 30 dnů se nevztahuje na [metriky založené na protokolu](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

Ve výchozím nastavení se v grafu zobrazuje posledních 24 hodin dat metrik. Panel **pro výběr času** použijte ke změně časového rozsahu, přiblížení nebo oddálení v grafu. 

![Panel změnit rozsah času](./media/metrics-getting-started/time.png)

> [!TIP]
> Použijte **časový štětec** k prozkoumání zajímavé oblasti grafu (špička nebo DIP). Umístěte ukazatel myši na začátek oblasti, klikněte na levé tlačítko myši, přetáhněte ho na druhou stranu oblasti a pak uvolněte tlačítko. Graf se v tomto časovém rozsahu přiblíží. 

## <a name="apply-dimension-filters-and-splitting"></a>Použití filtrů dimenzí a rozdělení

[Filtrování](../essentials/metrics-charts.md#filters) a [rozdělení](../essentials/metrics-charts.md#apply-splitting) jsou výkonné diagnostické nástroje pro metriky, které mají rozměry. Tyto funkce ukazují, jak různé segmenty metrik ("hodnoty dimenzí") ovlivňují celkovou hodnotu metriky a umožňují identifikovat možné odlehlé hodnoty.

- **Filtrování** umožňuje zvolit, které hodnoty dimenze budou zahrnuty v grafu. Například můžete chtít zobrazit úspěšné požadavky při vytváření grafu metriky *Doba odezvy serveru* . Je nutné použít filtr na *úspěch dimenze požadavku* . 

- **Rozdělení** ovládacích prvků určuje, zda graf zobrazuje samostatné řádky pro každou hodnotu dimenze, nebo agreguje hodnoty do jednoho řádku. Například můžete zobrazit jeden řádek pro průměrnou dobu odezvy napříč všemi instancemi serveru nebo zobrazit samostatné řádky pro každý server. Aby se zobrazily samostatné řádky, museli byste použít rozdělení na dimenzi *instance serveru* .

Podívejte [se na příklady grafů](../essentials/metric-chart-samples.md) , které mají použité filtrování a rozdělení. Tento článek popisuje kroky, které byly použity ke konfiguraci grafů.

## <a name="share-your-metric-chart"></a>Sdílení grafu metriky
V současnosti existují dva způsoby, jak sdílet graf metriky. Níže najdete pokyny, jak sdílet informace z grafů metrik prostřednictvím Excelu a odkazu.
 
### <a name="download-to-excel"></a>Stáhnout do Excelu
Klikněte na sdílet a vyberte stáhnout do Excelu. Váš soubor ke stažení by se měl spustit hned.

![snímek obrazovky s postupem sdílení grafu metriky přes Excel](./media/metrics-getting-started/share-excel.png)

### <a name="share-a-link"></a>Sdílet odkaz
Klikněte na sdílet a vyberte Kopírovat odkaz. Měli byste obdržet oznámení, že se odkaz úspěšně zkopíroval.

![snímek obrazovky s postupem sdílení grafu metriky prostřednictvím odkazu](./media/metrics-getting-started/share-link.png)


## <a name="advanced-chart-settings"></a>Upřesnit nastavení grafu

Můžete přizpůsobit styl grafu, název a upravit pokročilá nastavení grafu. Po dokončení přizpůsobení ho připněte na řídicí panel, abyste mohli svoji práci uložit. Můžete také nakonfigurovat výstrahy metrik. V [dokumentaci k produktu](../essentials/metrics-charts.md) se dozvíte další informace o těchto a dalších pokročilých funkcích Azure monitor Průzkumníku metrik.

## <a name="next-steps"></a>Další kroky

* [Další informace o rozšířených funkcích Průzkumník metrik](../essentials/metrics-charts.md)
* [Zobrazení více prostředků v Průzkumník metrik](./metrics-dynamic-scope.md)
* [Řešení potíží s Průzkumníkem metrik](metrics-troubleshoot.md)
* [Seznam dostupných metrik pro služby Azure](./metrics-supported.md)
* [Příklady nakonfigurovaných grafů](../essentials/metric-chart-samples.md)
