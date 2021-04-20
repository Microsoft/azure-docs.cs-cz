---
title: Vytváření upozornění pomocí SQL Insights (Náhled)
description: Vytváření upozornění pomocí SQL Insights v Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: bb42f74f6ac8487a93479bdf980c66ef87e8e742
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726857"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Vytváření upozornění pomocí SQL Insights (Náhled)
SQL Insights obsahuje sadu šablon pravidel upozornění, které můžete použít k vytvoření [pravidel upozornění v Azure monitor](../alert/../alerts/alerts-overview.md) pro běžné problémy SQL. Pravidla výstrah v SQL Insights jsou pravidla výstrah protokolů založená na datech výkonu uložených v tabulce *InsightsMetrics* v protokolech Azure monitor.  

> [!NOTE]
> Chcete-li vytvořit výstrahu pro SQL Insights pomocí šablony Resource Manageru, přečtěte si téma [Správce prostředků Samples Template for SQL Insights](resource-manager-sql-insights.md#create-an-alert-rule-for-sql-insights).


> [!NOTE]
> Pokud máte žádosti o další šablony pravidel upozornění SQL Insights, pošlete prosím svůj názor pomocí odkazu v dolní části této stránky nebo použijte odkaz na zpětnou vazbu SQL Insights v Azure Portal.

## <a name="enable-alert-rules"></a>Povolit pravidla upozornění 
Pomocí následujícího postupu povolíte výstrahy v Azure Monitor Azure Portal.Vytvořená pravidla výstrahy budou vymezena na všechny prostředky SQL monitorované v rámci vybraného profilu monitorování.  Když se aktivuje pravidlo výstrahy, aktivuje se konkrétní instance SQL nebo databáze.

> [!NOTE]
> Můžete také vytvořit vlastní [pravidla upozornění protokolů](../alerts/alerts-log.md) spuštěním dotazů na datové sady v tabulce *InsightsMetrics* a pak tyto dotazy uložit jako pravidlo výstrahy. 

V části **Přehled** v nabídce Azure Monitor v Azure Portal vyberte **SQL (Preview)** . Klikněte na **výstrahy** .

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Tlačítko výstrahy":::

Otevře se podokno **výstrahy** na pravé straně stránky. Ve výchozím nastavení se zobrazí aktivované výstrahy pro prostředky SQL ve vybraném profilu monitorování na základě pravidel upozornění, která jste už vytvořili. Vyberte **šablony výstrah**, ve kterých se zobrazí seznam dostupných šablon, pomocí kterých můžete vytvořit pravidlo upozornění.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Šablony výstrah":::

Na stránce **vytvořit pravidlo výstrahy** zkontrolujte výchozí nastavení pravidla a podle potřeby je upravte. Můžete také vybrat [skupinu akcí](../alerts/action-groups.md) a vytvořit tak oznámení a akce, když se aktivuje pravidlo výstrahy. Kliknutím na **zapnout pravidlo výstrahy** vytvoříte pravidlo upozornění, Jakmile ověříte všechny jeho vlastnosti.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Stránka pravidla výstrahy":::

Pokud chcete pravidlo výstrahy nasadit hned, klikněte na **nasadit pravidlo výstrahy**. Klikněte na možnost **Zobrazit šablonu** , pokud chcete před tím, než ji skutečně nasadíte, zobrazit šablonu pravidla.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Nasadit pravidlo výstrahy":::

Pokud se rozhodnete zobrazit šablony, vyberte na stránce Šablona možnost **nasadit** a vytvořte pravidlo upozornění.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Nasadit ze šablony zobrazení":::


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [výstrahách v Azure monitor](../alerts/alerts-overview.md).

