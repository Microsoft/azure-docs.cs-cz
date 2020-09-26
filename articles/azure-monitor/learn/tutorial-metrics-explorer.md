---
title: Kurz – vytvoření grafu metrik v Azure Monitor
description: Naučte se vytvořit graf metriky pomocí Průzkumníka metrik Azure.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 26b155484dafac4a63fe2a6c7ff292ee8bbacbc0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317204"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Kurz: vytvoření grafu metrik v Azure Monitor
Průzkumník metrik je funkce Azure Monitor v Azure Portal, která umožňuje vytvářet grafy z hodnot metriky, vizuálně sladit trendy a zkoumat špičky a neshodné hodnoty metriky. Pomocí Průzkumníka metrik můžete prozkoumat stav a využití vašich prostředků Azure nebo vykreslit grafy z vlastních metrik. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vyberte metriku, pro kterou chcete graf vykreslit.
> * Provádění různých agregací hodnot metriky
> * Úprava časového rozsahu a členitosti grafu

Následuje video, které zobrazuje rozsáhlejší scénář, než postup uvedený v tomto článku. Pokud s metrikami začínáte, doporučujeme nejdříve si projít tento článek a potom si Zobrazit video a podívat se na další podrobnosti. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete prostředek Azure, který se má monitorovat. Můžete použít libovolný prostředek v předplatném Azure, které podporuje metriky. Chcete-li zjistit, zda prostředek podporuje metriky, přejděte do příslušné nabídky v Azure Portal a ověřte, zda je v této nabídce v části **monitorování** k dispozici možnost **metriky** .


## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Přihlaste se k Azure Portal v [https://portal.azure.com](https://portal.azure.com) .

## <a name="open-metrics-explorer-and-select-a-scope"></a>Otevřete Průzkumníka metrik a vyberte obor.
Můžete otevřít Průzkumníka metrik buď z nabídky Azure Monitor, nebo z nabídky prostředku v Azure Portal. Metriky ze všech prostředků jsou dostupné bez ohledu na to, kterou možnost používáte. 

1. V nabídce **Azure monitor** vyberte **metriky** nebo v části **monitorování** v nabídce prostředku.

1. Vyberte **obor**, který je prostředkem, pro který chcete zobrazit metriky. Pokud jste v nabídce prostředku spustili průzkumníka metrik, obor se už naplní.

    ![Vybrat obor](media/tutorial-metrics-explorer/scope-picker.png)

2. Vyberte **obor názvů** , pokud má obor více než jeden. Obor názvů je jenom způsob, jak uspořádat metriky, abyste je mohli snadno najít. Například účty úložiště mají samostatné obory názvů pro ukládání souborů, tabulek, objektů BLOB a metriky front. Mnoho typů prostředků má pouze jeden obor názvů.

3. Vyberte metriku ze seznamu dostupných metrik pro vybraný obor a obor názvů.

    ![Vyberte metriku](media/tutorial-metrics-explorer/metric-picker.png)

4. Volitelně můžete změnit **agregaci**metriky. To definuje, jak se hodnoty metriky agregují napříč časovými rozlišeními grafu. Pokud je například časové rozlišení nastaveno na 15 minut a agregace je nastavena na součet, pak každý bod v grafu bude součtem všech shromážděných hodnot za každých 15 minut segmentů.

    ![Snímek obrazovky zobrazuje graf s názvem součet příchozího přenosu dat pro contosoretailweb.](media/tutorial-metrics-explorer/chart.png)

5. Použijte tlačítko **Přidat metriku** a opakujte tyto kroky, pokud chcete zobrazit více metrik vykreslených ve stejném grafu. U více grafů v jednom zobrazení vyberte tlačítko **Nový graf** .

## <a name="select-a-time-range-and-granularity"></a>Vyberte časový rozsah a členitost.

Ve výchozím nastavení se v grafu zobrazuje posledních 24 hodin dat metrik. Pomocí nástroje pro výběr času můžete změnit **časový rozsah** grafu nebo **časové rozlišení** , které definuje časový rozsah pro každý datový bod. Graf používá určenou agregaci k výpočtu všech vzorků hodnot v časovém intervalu, který je určen.

![Panel změnit rozsah času](media/tutorial-metrics-explorer/time-picker.png)


Použijte **časový štětec** k prozkoumání zajímavé oblasti grafu, jako je špička nebo DIP. Umístěte ukazatel myši na začátek oblasti, klikněte na levé tlačítko myši, přetáhněte ho na druhou stranu oblasti a uvolněte tlačítko. Graf se v tomto časovém rozsahu přiblíží. 

![Štětec času](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Použití filtrů dimenzí a rozdělení
V následujících odkazech najdete informace o pokročilých funkcích, které vám umožní provádět další analýzy metrik a identifikovat potenciální odlehlé hodnoty ve vašich datech.

- [Filtrování](../platform/metrics-charts.md#apply-filters-to-charts) umožňuje zvolit, které hodnoty dimenze budou zahrnuty v grafu. Například může být vhodné zobrazit pouze úspěšné požadavky při vytváření grafu metriky *doby odezvy serveru* . 

- [Rozdělení](../platform/metrics-charts.md#apply-splitting-to-a-chart) ovládacích prvků určuje, zda graf zobrazuje samostatné řádky pro každou hodnotu dimenze, nebo agreguje hodnoty do jednoho řádku. Například můžete chtít zobrazit jednu čáru pro průměrnou dobu odezvy napříč všemi instancemi serveru nebo můžete chtít samostatné řádky pro každý server. 

Podívejte [se na příklady grafů](../platform/metric-chart-samples.md) , které mají použité filtrování a rozdělení.

## <a name="advanced-chart-settings"></a>Upřesnit nastavení grafu

Můžete přizpůsobit styl grafu, název a upravit pokročilá nastavení grafu. Po dokončení přizpůsobení ho připněte na řídicí panel, abyste mohli svoji práci uložit. Můžete také nakonfigurovat výstrahy metrik. V tématu [Pokročilé funkce služby Azure Průzkumník metrik](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) se dozvíte o těchto a dalších pokročilých funkcích v průzkumníkovi metrik Azure monitor.


## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili pracovat s metrikami v Azure Monitor, Naučte se používat metriky k posílání proaktivní výstrah.

> [!div class="nextstepaction"]
> [Vytváření, zobrazení a správa upozornění na metriky pomocí služby Azure Monitor](../platform/alerts-metric.md)

