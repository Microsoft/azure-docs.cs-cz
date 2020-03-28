---
title: Kurz – vytvoření grafu metrik v Azure Monitoru
description: Přečtěte si, jak vytvořit svůj první graf metriky pomocí Průzkumníka metrik Azure.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: d8f72471dd3109bf584d18f032ec2f4d949a4993
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79082808"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Kurz: Vytvoření grafu metrik v Azure Monitoru
Průzkumník metrik je funkce Azure Monitoru na webu Azure Portal, která umožňuje vytvářet grafy z hodnot metrik, vizuálně korelovat trendy a zkoumat špičky a poklesy hodnot metrik. Pomocí průzkumníka metrik prozkoumejte stav a využití prostředků Azure nebo vykreslujte grafy z vlastních metrik. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vyberte metriku, pro kterou chcete graf vykreslit.
> * Provádění různých agregací hodnot metrik
> * Změna časového rozsahu a rozlišovací schopnost grafu

Následuje video, které ukazuje rozsáhlejší scénář než postup popsaný v tomto článku. Pokud s metrikami teprve začínáte, doporučujeme vám nejprve přečíst si tento článek a potom si video prohlédnout, abyste viděli další podrobnosti. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu budete potřebovat prostředek Azure ke sledování. Můžete použít libovolný prostředek v předplatném Azure, který podporuje metriky. Chcete-li zjistit, zda prostředek podporuje metriky, přejděte do jeho nabídky na webu Azure portal a ověřte, že v části **Monitorování** v nabídce je možnost **Metriky.**


## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="open-metrics-explorer-and-select-a-scope"></a>Otevření průzkumníka metrik a výběr oboru
Průzkumník metrik můžete otevřít buď z nabídky Azure Monitor nebo z nabídky prostředku na webu Azure Portal. Metriky ze všech zdrojů jsou k dispozici bez ohledu na to, kterou možnost používáte. 

1. V nabídce **Azure Monitor** nebo v části **Monitorování** nabídky prostředku vyberte **Metriky.**

1. Vyberte **obor**, což je prostředek, pro který chcete zobrazit metriky. Obor je již naplněn, pokud jste otevřeli průzkumník metrik z nabídky prostředku.

    ![Výběr oboru](media/tutorial-metrics-explorer/scope-picker.png)

2. Vyberte **obor názvů,** pokud obor má více než jeden. Obor názvů je pouze způsob, jak uspořádat metriky tak, abyste je mohli snadno najít. Například účty úložiště mají samostatné obory názvů pro ukládání metrik Souborů, tabulek, objektů BLOB a Front. Mnoho typů prostředků má pouze jeden obor názvů.

3. Vyberte metriku ze seznamu dostupných metrik pro vybraný obor a obor názvů.

    ![Výběr metriky](media/tutorial-metrics-explorer/metric-picker.png)

4. Volitelně můžete změnit metriku **Agregace**. To definuje, jak se budou agregovat hodnoty metriky v časovém rozlišovacím čase pro graf. Pokud je například rozlišovací schopnost nastavena na 15 minut a agregace je nastavena na hodnotu součet, bude každý bod v grafu součtem všech shromážděných hodnot v každém segmentu 15 minut.

    ![Graf](media/tutorial-metrics-explorer/chart.png)

5. Pokud chcete zobrazit více metrik vykreslených ve stejném grafu, použijte tlačítko **Přidat metriku** a opakujte tyto kroky. Pro více grafů v jednom zobrazení vyberte tlačítko **Nový graf.**

## <a name="select-a-time-range-and-granularity"></a>Výběr časového rozsahu a rozlišovací schopnost

Ve výchozím nastavení graf zobrazuje posledních 24 hodin dat metrik. Pomocí výběru času můžete změnit **časový rozsah** grafu nebo **rozlišovací schopnost Čas,** který definuje časový rozsah pro každý datový bod. Graf používá zadanou agregaci k výpočtu všech vzorkovaných hodnot za zadanou časovou rozlišovací schopnost.

![Panel Změna časového rozsahu](media/tutorial-metrics-explorer/time-picker.png)


Pomocí **časového štětce** prozkoumejte zajímavou oblast grafu, například špičku nebo pokles. Položte ukazatel myši na začátek oblasti, klepněte na levé tlačítko myši a podržte ho, táhněte na druhou stranu oblasti a uvolněte tlačítko. Graf tento časový rozsah zvětší. 

![Časový štětec](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Použití filtrů dimenzí a rozdělení
Pokročilé funkce, které umožňují provádět další analýzy metrik a identifikovat potenciální odlehlé hodnoty v datech, naleznete v následujících odkazech.

- [Filtrování](../platform/metrics-charts.md#apply-filters-to-charts) umožňuje zvolit, které hodnoty dimenzí budou zahrnuty do grafu. Můžete například chtít zobrazit pouze úspěšné požadavky při vytváření grafů metriky *doby odezvy serveru.* 

- [Rozdělení](../platform/metrics-charts.md#apply-splitting-to-a-chart) určuje, zda graf zobrazuje samostatné čáry pro každou hodnotu dimenze nebo agreguje hodnoty do jednoho řádku. Můžete například chtít zobrazit jeden řádek pro průměrnou dobu odezvy napříč všemi instancemi serveru nebo můžete chtít samostatné řádky pro každý server. 

Podívejte se [na příklady grafů,](../platform/metric-chart-samples.md) u kterých bylo použito filtrování a rozdělení.

## <a name="advanced-chart-settings"></a>Upřesňující nastavení grafu

Můžete přizpůsobit styl grafu, název a upravit upřesňující nastavení grafu. Po dokončení vlastního nastavení jej připněte na řídicí panel a uložte svou práci. Můžete také nakonfigurovat upozornění metriky. Podívejte se na [pokročilé funkce Průzkumníka metrik Azure,](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) kde najdete informace o těchto a dalších pokročilých funkcích průzkumníka metrik Azure Monitoru.


## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili pracovat s metrikami ve Službě Azure Monitor, zjistěte, jak pomocí metrik odesílat proaktivní výstrahy.

> [!div class="nextstepaction"]
> [Vytváření, zobrazení a správa upozornění na metriky pomocí služby Azure Monitor](../platform/alerts-metric.md)

