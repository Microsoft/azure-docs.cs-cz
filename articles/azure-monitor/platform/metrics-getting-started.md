---
title: Začínáme s Průzkumníkem metrik Azure
description: Naučte se, jak vytvořit první graf metriky pomocí Průzkumníka metrik Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 3e5514c4a54083bcc6ef8187f2cd97515d2dd680
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88590179"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Začínáme s Průzkumníkem metrik Azure

## <a name="where-do-i-start"></a>Kde mám začít
Azure Monitor Průzkumník metrik je součástí portál Microsoft Azure, která umožňuje vykreslovat grafy, vizuálně korelovat trendy a zkoumat špičky a rozmítají metriky v hodnotách metriky. Pomocí Průzkumníka metrik můžete prozkoumat stav a využití vašich prostředků. Začněte v tomto pořadí:

1. [Vyberte prostředek a metriku](#create-your-first-metric-chart) a uvidíte základní graf. Pak [vyberte časový rozsah](#select-a-time-range) , který je pro vaše šetření relevantní.

1. Zkuste [použít filtry dimenzí a rozdělení](#apply-dimension-filters-and-splitting). Filtry a rozdělení umožňují analyzovat, které segmenty metriky přispívají k celkové hodnotě metriky a identifikují možné odlehlé hodnoty.

1. Před připnutím k řídicím panelům můžete graf přizpůsobit pomocí [rozšířených nastavení](#advanced-chart-settings) . [Nakonfigurujte výstrahy](alerts-metric-overview.md) pro příjem oznámení, když hodnota metriky překročí nebo klesne pod prahovou hodnotu.

## <a name="create-your-first-metric-chart"></a>Vytvoření prvního grafu metriky

Pokud chcete vytvořit graf metriky, ze svého prostředku, skupiny prostředků, předplatného nebo Azure Monitorho zobrazení, otevřete kartu **metriky** a proveďte následující kroky:

1. Pomocí ovládacího prvku pro výběr prostředku vyberte prostředek, pro který chcete zobrazit metriky. (Prostředek je předem vybraný, pokud jste spustili **metriky** v kontextu konkrétního prostředku).

    > ![Výběr prostředku](./media/metrics-getting-started/resource-picker.png)

2. U některých prostředků je nutné vybrat obor názvů. Obor názvů je jenom způsob, jak uspořádat metriky, abyste je mohli snadno najít. Například účty úložiště mají samostatné obory názvů pro ukládání souborů, tabulek, objektů BLOB a metriky front. Mnoho typů prostředků má pouze jeden obor názvů.

3. Vyberte metriku ze seznamu dostupných metrik.

    > ![Vyberte metriku](./media/metrics-getting-started/metric-picker.png)

4. Volitelně můžete [změnit agregaci metriky](metrics-charts.md#changing-aggregation). Můžete například chtít, aby v grafu bylo možné zobrazit minimální, maximální nebo průměrné hodnoty metriky.

> [!TIP]
> Použijte tlačítko **Přidat metriku** a opakujte tyto kroky, pokud chcete zobrazit více metrik vykreslených ve stejném grafu. U více grafů v jednom zobrazení vyberte tlačítko **přidat graf** nahoře.

## <a name="select-a-time-range"></a>Vyberte časový rozsah.

> [!WARNING]
> [Většina metrik v Azure se ukládá po dobu 93 dnů](data-platform-metrics.md#retention-of-metrics). Nemůžete ale zadat dotaz na data na libovolný graf na více než 30 dnů. Toto omezení neplatí pro [metriky založené na protokolech](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

Ve výchozím nastavení se v grafu zobrazuje posledních 24 hodin dat metrik. Panel **pro výběr času** použijte ke změně časového rozsahu, přiblížení nebo oddálení v grafu. 

![Panel změnit rozsah času](./media/metrics-getting-started/time-picker.png)

> [!TIP]
> Použijte **časový štětec** k prozkoumání zajímavé oblasti grafu (špička nebo DIP). Umístěte ukazatel myši na začátek oblasti, klikněte na levé tlačítko myši, přetáhněte ho na druhou stranu oblasti a pak uvolněte tlačítko. Graf se v tomto časovém rozsahu přiblíží. 

## <a name="apply-dimension-filters-and-splitting"></a>Použití filtrů dimenzí a rozdělení

[Filtrování](metrics-charts.md#apply-filters-to-charts) a [rozdělení](metrics-charts.md#apply-splitting-to-a-chart) jsou výkonné diagnostické nástroje pro metriky, které mají rozměry. Tyto funkce ukazují, jak různé segmenty metrik ("hodnoty dimenzí") ovlivňují celkovou hodnotu metriky a umožňují identifikovat možné odlehlé hodnoty.

- **Filtrování** umožňuje zvolit, které hodnoty dimenze budou zahrnuty v grafu. Například můžete chtít zobrazit úspěšné požadavky při vytváření grafu metriky *Doba odezvy serveru* . Je nutné použít filtr na *úspěch dimenze požadavku* . 

- **Rozdělení** ovládacích prvků určuje, zda graf zobrazuje samostatné řádky pro každou hodnotu dimenze, nebo agreguje hodnoty do jednoho řádku. Například můžete zobrazit jeden řádek pro průměrnou dobu odezvy napříč všemi instancemi serveru nebo zobrazit samostatné řádky pro každý server. Aby se zobrazily samostatné řádky, museli byste použít rozdělení na dimenzi *instance serveru* .

Podívejte [se na příklady grafů](metric-chart-samples.md) , které mají použité filtrování a rozdělení. Tento článek popisuje kroky, které byly použity ke konfiguraci grafů.

## <a name="advanced-chart-settings"></a>Upřesnit nastavení grafu

Můžete přizpůsobit styl grafu, název a upravit pokročilá nastavení grafu. Po dokončení přizpůsobení ho připněte na řídicí panel, abyste mohli svoji práci uložit. Můžete také nakonfigurovat výstrahy metrik. V [dokumentaci k produktu](metrics-charts.md) se dozvíte další informace o těchto a dalších pokročilých funkcích Azure monitor Průzkumníku metrik.

## <a name="next-steps"></a>Další kroky

* [Další informace o rozšířených funkcích Průzkumník metrik](metrics-charts.md)
* [Řešení potíží s Průzkumníkem metrik](metrics-troubleshoot.md)
* [Seznam dostupných metrik pro služby Azure](metrics-supported.md)
* [Příklady nakonfigurovaných grafů](metric-chart-samples.md)
