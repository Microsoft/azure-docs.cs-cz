---
title: Začínáme s Průzkumníkem metrik Azure
description: Přečtěte si, jak vytvořit svůj první graf metriky pomocí Průzkumníka metrik Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248772"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Začínáme s Průzkumníkem metrik Azure

## <a name="where-do-i-start"></a>Kde mám začít
Průzkumník metrik Azure Monitoru je součástí portálu Microsoft Azure, který umožňuje vykreslovat grafy, vizuálně korelovat trendy a zkoumat špičky a poklesy hodnot metrik. Pomocí průzkumníka metrik prozkoumejte stav a využití vašich prostředků. Začněte v následujícím pořadí:

1. [Vyberte zdroj a metriku](#create-your-first-metric-chart) a uvidíte základní graf. Pak [vyberte časový rozsah,](#select-a-time-range) který je relevantní pro vaše vyšetřování.

1. Zkuste [použít filtry dimenzí a rozdělení](#apply-dimension-filters-and-splitting). Filtry a rozdělení umožňují analyzovat, které segmenty metriky přispívají k celkové hodnotě metriky, a identifikovat možné odlehlé hodnoty.

1. Pomocí [upřesňujících nastavení](#advanced-chart-settings) můžete graf před připnutím k řídicím panelům přizpůsobit. [Nakonfigurujte výstrahy](alerts-metric-overview.md) tak, aby přijímali oznámení, když hodnota metriky překročí nebo klesne pod prahovou hodnotu.

## <a name="create-your-first-metric-chart"></a>Vytvoření prvního metrického grafu

Pokud chcete vytvořit metrikový graf, v zobrazení prostředků, skupiny prostředků, předplatného nebo Azure Monitoru otevřete kartu **Metriky** a postupujte takto:

1. Pomocí výběru zdrojů vyberte zdroj, pro který chcete zobrazit metriky. (Zdroj je předem vybrán, pokud jste **otevřeli metriky** v kontextu konkrétního prostředku).

    > ![Výběr prostředku](./media/metrics-getting-started/resource-picker.png)

2. U některých prostředků je nutné vybrat obor názvů. Obor názvů je pouze způsob, jak uspořádat metriky tak, abyste je mohli snadno najít. Například účty úložiště mají samostatné obory názvů pro ukládání metrik Souborů, tabulek, objektů BLOB a Front. Mnoho typů prostředků má pouze jeden obor názvů.

3. Vyberte metriku ze seznamu dostupných metrik.

    > ![Výběr metriky](./media/metrics-getting-started/metric-picker.png)

4. Volitelně můžete změnit agregaci metriky. Můžete například chtít, aby graf zobrazoval minimální, maximální nebo průměrné hodnoty metriky.

> [!NOTE]
> Pokud chcete zobrazit více metrik vykreslených ve stejném grafu, použijte tlačítko **Přidat metriku** a opakujte tyto kroky. U více grafů v jednom zobrazení vyberte tlačítko **Přidat graf** nahoře.

## <a name="select-a-time-range"></a>Výběr časového rozsahu

Ve výchozím nastavení graf zobrazuje posledních 24 hodin dat metrik. Pomocí panelu **pro výběr času** můžete změnit časový rozsah, přiblížit nebo oddálit graf. 

![Panel Změna časového rozsahu](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Pomocí **časového štětce** prozkoumejte zajímavou oblast grafu (špičku nebo pokles). Položte ukazatel myši na začátek oblasti, klikněte a podržte levé tlačítko myši, táhněte na druhou stranu oblasti a poté tlačítko uvolněte. Graf tento časový rozsah zvětší. 

## <a name="apply-dimension-filters-and-splitting"></a>Použití filtrů dimenzí a rozdělení

[Filtrování](metrics-charts.md#apply-filters-to-charts) a [rozdělení](metrics-charts.md#apply-splitting-to-a-chart) jsou výkonné diagnostické nástroje pro metriky, které mají rozměry. Tyto funkce ukazují, jak různé segmenty metrik ("hodnoty dimenzí") ovlivňují celkovou hodnotu metriky, a umožňují identifikovat možné odlehlé hodnoty.

- **Filtrování** umožňuje zvolit, které hodnoty dimenzí budou zahrnuty do grafu. Můžete například chtít zobrazit úspěšné požadavky při vytváření grafů metriky *doby odezvy serveru.* Budete muset použít filtr na úspěch dimenze *požadavku.* 

- **Rozdělení** určuje, zda graf zobrazuje samostatné čáry pro každou hodnotu dimenze nebo agreguje hodnoty do jednoho řádku. Můžete například zobrazit jeden řádek pro průměrnou dobu odezvy napříč všemi instancemi serveru nebo zobrazit samostatné řádky pro každý server. Chcete-li zobrazit samostatné řádky, bylo by nutné použít rozdělení v dimenzi *instance serveru.*

Podívejte se [na příklady grafů,](metric-chart-samples.md) u kterých bylo použito filtrování a rozdělení. Článek ukazuje, že kroky byly použity ke konfiguraci grafů.

## <a name="advanced-chart-settings"></a>Upřesňující nastavení grafu

Můžete přizpůsobit styl grafu, název a upravit upřesňující nastavení grafu. Po dokončení vlastního nastavení jej připněte na řídicí panel a uložte svou práci. Můžete také nakonfigurovat upozornění metriky. Podle [dokumentace k produktu](metrics-charts.md) se dozvíte o těchto a dalších pokročilých funkcích průzkumníka metrik Azure Monitoru.

## <a name="next-steps"></a>Další kroky

* [Informace o pokročilých funkcích Průzkumníka metrik](metrics-charts.md)
* [Řešení potíží s Průzkumníkem metrik](metrics-troubleshoot.md)
* [Seznam dostupných metrik pro služby Azure](metrics-supported.md)
* [Příklady nakonfigurovaných grafů](metric-chart-samples.md)
