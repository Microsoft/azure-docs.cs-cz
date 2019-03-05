---
title: Začínáme s Průzkumníkem metrik Azure
description: Zjistěte, jak vytvořit první graf metrik s Průzkumníkem metrik Azure.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: a94e53644b2032af7be1575cd13807e0484eb182
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344704"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Začínáme s Průzkumníkem metrik Azure

## <a name="where-do-i-start"></a>Kde mám začít

> [!NOTE]
> Tento článek popisuje klíčové koncepty, které pomáhají novým uživatelům Začínáme s Průzkumníkem metrik Azure monitoru. Podrobnější dokumentaci a informace o nastavení pokročilé grafu a metriky, najdete v článku [pokročilé funkce Průzkumníka metrik Azure monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts).

Pomocí Průzkumníka metrik k prozkoumání stavu a využití vašich prostředků. Spusťte v následujícím pořadí:

1. Začněte tím, že [výběr prostředku a metriku](#creating-your-first-metric-chart) a zobrazí se základní graf. Potom [vyberte časový rozsah](#picking-time-range) , který je relevantní pro šetření.

1. Po získání informací o základní grafy, můžete chtít zkuste [použití filtrů dimenzí a rozdělování](#applying-dimension-filters-and-splitting). Filtry a rozdělování umožňují analyzovat, které segmenty metriky přispívat na hodnotu celkové metriky a identifikovat odlehlé hodnoty je to možné.

1. Použití [upřesňující nastavení](#advanced-chart-settings-and-next-steps) přizpůsobení grafu před připnutím na řídicí panely. [Konfigurace výstrah](alerts-metric-overview.md) pro příjem oznámení, když hodnota metriky překročí nebo klesne pod prahovou hodnotou.

## <a name="create-your-first-metric-chart"></a>Vytvoření prvního grafu metriky

Chcete-li vytvořit grafu metrik z prostředků, skupinu prostředků, předplatné nebo zobrazení monitorování Azure, otevřete **metriky** kartu a postupujte podle těchto kroků:

1. Pomocí nástroje pro výběr prostředků vyberte prostředek, pro kterou chcete zobrazit metriky. (Pokud jste otevřeli je předem vybraná prostředek **metriky** v kontextu konkrétní prostředek).

    > ![Výběr prostředku](./media/metrics-getting-started/resource-picker.png)

2. Pro některé zdroje musíte vybrat obor názvů. Obor názvů je pouze způsob uspořádání metrik tak, že je budete moci snadno najít. Například účty úložiště mají samostatné obory názvů pro ukládání metriky pro soubory, tabulky, objekty BLOB a fronty. Mnoho typů prostředků mít pouze jeden obor názvů.

3. Vyberte metriku ze seznamu dostupných metrik.

    > ![Vyberte metriku](./media/metrics-getting-started/metric-picker.png)

4. Volitelně můžete změnit agregace metrik. Například může být vhodné grafu zobrazíte minimální, maximální a průměrné hodnoty metriky.

> [!NOTE]
> Použití **přidat metriku** tlačítko a opakujte tyto kroky, pokud chcete zobrazit několik metrik zobrazených ve stejném grafu. Několik grafů v jednom zobrazení, vyberte **přidat graf** tlačítko v horní části.

## <a name="pick-a-time-range"></a>Vyberte časový rozsah

Tento graf zobrazuje ve výchozím nastavení, posledních 24 hodin data metrik. Použití **výběr času** panel změnit časový rozsah, Přiblížit nebo oddálit v grafu. 

![Panel rozsahu změnu času](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>Použití filtrů dimenzí a rozdělení

[Filtrování](metrics-charts.md#apply-filters-to-charts) a [rozdělení](metrics-charts.md#apply-splitting-to-a-chart) jsou výkonné diagnostické nástroje pro metriky, které mají dimenze. Tyto funkce Zobrazit různých metrik segmentů ("hodnoty dimenze") mít vliv na celkovou hodnotu metriky a umožní vám identifikovat odlehlé hodnoty je to možné.

- **Filtrování** umožní vybrat hodnot dimenzí, které jsou zahrnuty v grafu. Například můžete chtít zobrazit úspěšné žádosti při vytvoření grafu *doba odezvy serveru* metriku. Je třeba použít filtr *úspěch požadavku* dimenze. 

- **Rozdělení** ovládací prvky, zda se v grafu zobrazí samostatné řádky pro každou hodnotu dimenze nebo agreguje hodnoty do jednoho řádku. Můžete například zobrazit jeden řádek pro průměrné doby odezvy napříč všemi instancemi serveru nebo najdete v samostatných řádků pro každý server. Je třeba použít rozdělení na *instance serveru* dimenze zobrazíte na samostatných řádcích.

Zobrazit [příklady grafy](metric-chart-samples.md) , filtrování a rozdělování použily. Tento článek ukazuje, že postup se používá ke konfiguraci grafy.

## <a name="advanced-chart-settings-and-next-steps"></a>Nastavení pokročilých grafu a další kroky

Můžete přizpůsobit styl grafu, title a upravit nastavení pokročilé grafu. Až budete hotovi s přizpůsobováním, můžete ji připnete na řídicí panel a práci uložte. Můžete také nastavit upozornění metriky. Postupujte podle [dokumentaci k produktu](metrics-charts.md) Další informace o těchto a dalších pokročilých funkcí Průzkumník metrik Azure monitoru.

## <a name="next-steps"></a>Další postup

* [Podívejte se do seznamu dostupných metrik pro služby Azure](metrics-supported.md)
* [Další informace o pokročilých funkcích sady Průzkumník metrik](metrics-charts.md)
* [Podívejte se na příklady nakonfigurované grafů](metric-chart-samples.md)