---
title: Jak vytvořit výstrahy pro Azure Automation Update Management
description: Tento článek obsahuje informace o tom, jak nakonfigurovat výstrahy Azure, aby upozornily na stav posouzení aktualizací nebo nasazení.
services: automation
ms.subservice: update-management
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 224a7b5457a099fd763ac657349fc5497824ab76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601408"
---
# <a name="how-to-create-alerts-for-update-management"></a>Jak vytvořit výstrahy pro Update Management

Výstrahy v Azure proaktivně upozorňují na výsledky úloh Runbooku, problémů se stavem služby nebo jiných scénářů týkajících se vašeho účtu Automation. Azure Automation nezahrnuje předem nakonfigurovaná pravidla výstrah, ale můžete vytvořit vlastní, a to na základě dat, která generují. Tento článek obsahuje pokyny k vytváření pravidel výstrah pomocí metrik, které jsou součástí Update Management.

## <a name="available-metrics"></a>Dostupné metriky

Azure Automation vytvoří dvě různé metriky platforem související s Update Management shromažďovanými a předávanými do Azure Monitor. Tato metrika je k dispozici pro analýzu pomocí [Průzkumník metrik](../../azure-monitor/essentials/metrics-charts.md) a pro upozorňování pomocí [pravidla výstrahy metrik](../../azure-monitor/alerts/alerts-metric.md).

Vysílané dvě metriky jsou:

* Celkový počet spuštěných počítačů nasazení aktualizace
* Celkový počet spuštění nasazení aktualizací

Při použití pro výstrahy podporují obě metriky dimenze, které obsahují další informace, které vám pomůžou určit podrobnosti o konkrétnímu nasazení aktualizace. V následující tabulce jsou uvedeny podrobnosti o metrikě a dimenzích, které jsou k dispozici při konfiguraci výstrahy.

|Název signálu|Dimenze|Description
|---|---|---|
|`Total Update Deployment Runs`|– Název nasazení aktualizace<br>-Status | Upozorní na celkový stav nasazení aktualizace.|
|`Total Update Deployment Machine Runs`|– Název nasazení aktualizace</br>-Status</br>– Cílový počítač</br>– Aktualizovat ID běhu nasazení    |Upozorní na stav nasazení aktualizace zaměřeného na konkrétní počítače.|

## <a name="create-alert"></a>Vytvoření upozornění

Pomocí následujících kroků nastavte výstrahy, které vám pomůžou zjistit stav nasazení aktualizace. Pokud s výstrahami Azure začínáte, přečtěte si téma [Přehled výstrah Azure](../../azure-monitor/alerts/alerts-overview.md).

1. Ve svém účtu Automation v části **monitorování** vyberte **výstrahy** a pak vyberte **nové pravidlo výstrahy**.

1. Na stránce **vytvořit pravidlo výstrahy** už je váš účet Automation vybraný jako prostředek. Pokud ho chcete změnit, vyberte **Upravit prostředek**.

1. Na stránce Vybrat prostředek zvolte **účty Automation** v rozevíracím seznamu **filtrovat podle typu prostředku** .

1. Vyberte účet Automation, který chcete použít, a potom vyberte **Hotovo**.

1. Vyberte **Přidat podmínku** a zvolte signál, který je vhodný pro váš požadavek.

1. V poli dimenze vyberte platnou hodnotu ze seznamu. Pokud hodnota, kterou chcete, není v seznamu, vyberte možnost **\+** vedle dimenze a zadejte vlastní název. Pak vyberte hodnotu, kterou chcete vyhledat. Pokud chcete vybrat všechny hodnoty pro dimenzi, vyberte tlačítko **vybrat \*** . Pokud nevyberete hodnotu dimenze, Update Management tuto dimenzi ignoruje.

    ![Konfigurace logiky signálů](./media/manage-updates-for-vm/signal-logic.png)

1. V části **logika výstrahy** zadejte hodnoty do polí **Časová agregace** a **prahová hodnota** a potom vyberte **Hotovo**.

1. Na další stránce zadejte název a popis výstrahy.

1. Nastavte pole **závažnost** na **informativní (závažnost 2)** pro úspěšné spuštění nebo **informativní (závažnost 1)** pro neúspěšné spuštění.

    ![Snímek obrazovky se zvýrazněnou částí definovat podrobnosti výstrahy s názvem, popisem a poli závažnost pravidla výstrahy.](./media/manage-updates-for-vm/define-alert-details.png)

1. Vyberte **Ano** , pokud chcete pravidlo výstrahy povolit.

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurace skupin akcí pro vaše výstrahy

Jakmile budete mít nakonfigurovaná upozornění, můžete nastavit skupinu akcí, která je skupinou akcí pro použití v rámci více výstrah. Akce můžou zahrnovat e-mailová oznámení, Runbooky, Webhooky a spoustu dalších. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](../../azure-monitor/alerts/action-groups.md).

1. Vyberte výstrahu a potom v části **Akce** vyberte **Přidat skupiny akcí** . Zobrazí se okno **Vyberte skupinu akcí, která se má připojit k tomuto pravidlu upozornění** .

   :::image type="content" source="./media/manage-updates-for-vm/select-an-action-group.png" alt-text="Využití a odhadované náklady.":::

1. Zaškrtněte políčko pro skupinu akcí, která se má připojit, a stiskněte tlačítko vybrat.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [výstrahách v Azure monitor](../../azure-monitor/alerts/alerts-overview.md).

* Přečtěte si o [dotazech protokolu](../../azure-monitor/logs/log-query-overview.md) pro načtení a analýzu dat z log Analyticsho pracovního prostoru.

* Správa [využití a nákladů pomocí Azure Monitorch protokolů](../../azure-monitor/logs/manage-cost-storage.md) popisuje, jak řídit náklady změnou doby uchovávání dat a jak analyzovat a upozorňovat na využití vašich dat.