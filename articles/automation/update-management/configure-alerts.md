---
title: Jak vytvořit výstrahy pro Azure Automation Update Management
description: Tento článek obsahuje informace o tom, jak nakonfigurovat výstrahy Azure, aby upozornily na stav posouzení aktualizací nebo nasazení.
services: automation
ms.subservice: update-management
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 74207fe088034ff8d102fb2254d8ab78a6d57671
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579703"
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

2. Na stránce **vytvořit pravidlo výstrahy** už je váš účet Automation vybraný jako prostředek. Pokud ho chcete změnit, vyberte **Upravit prostředek**.

3. Na stránce Vybrat prostředek zvolte **účty Automation** v rozevíracím seznamu **filtrovat podle typu prostředku** .

4. Vyberte účet Automation, který chcete použít, a potom vyberte **Hotovo**.

5. Vyberte **Přidat podmínku** a zvolte signál, který je vhodný pro váš požadavek.

6. V poli dimenze vyberte platnou hodnotu ze seznamu. Pokud hodnota, kterou chcete, není v seznamu, vyberte možnost **\+** vedle dimenze a zadejte vlastní název. Pak vyberte hodnotu, kterou chcete vyhledat. Pokud chcete vybrat všechny hodnoty pro dimenzi, vyberte tlačítko **vybrat \*** . Pokud nevyberete hodnotu dimenze, Update Management tuto dimenzi ignoruje.

    ![Konfigurace logiky signálů](./media/manage-updates-for-vm/signal-logic.png)

7. V části **logika výstrahy** zadejte hodnoty do polí **Časová agregace** a **prahová hodnota** a potom vyberte **Hotovo**.

8. Na další stránce zadejte název a popis výstrahy.

9. Nastavte pole **závažnost** na **informativní (závažnost 2)** pro úspěšné spuštění nebo **informativní (závažnost 1)** pro neúspěšné spuštění.

    ![Snímek obrazovky se zvýrazněnou částí definovat podrobnosti výstrahy s názvem, popisem a poli závažnost pravidla výstrahy.](./media/manage-updates-for-vm/define-alert-details.png)

10. Vyberte **Ano** , pokud chcete pravidlo výstrahy povolit.

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurace skupin akcí pro vaše výstrahy

Jakmile budete mít nakonfigurovaná upozornění, můžete nastavit skupinu akcí, která je skupinou akcí pro použití v rámci více výstrah. Akce můžou zahrnovat e-mailová oznámení, Runbooky, Webhooky a spoustu dalších. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](../../azure-monitor/alerts/action-groups.md).

1. Vyberte výstrahu a potom v části **skupiny akcí** vyberte **vytvořit novou** .

2. Zadejte celé jméno a krátký název skupiny akcí. Při odesílání oznámení pomocí zadané skupiny používá Update Management krátký název.

3. V části **Akce** zadejte název, který určuje akci, například **e-mailové oznámení**.

4. Jako **typ akce** vyberte vhodný typ, například **e-mail/SMS/Push/Voice**.

5. Vyberte **Upravit podrobnosti**.

6. Vyplňte podokno pro typ akce. Pokud například používáte **e-mail/SMS/Push/Voice**, zadejte název akce, zaškrtněte políčko **e-mail** , zadejte platnou e-mailovou adresu a pak vyberte **OK**.

    ![Konfigurace e-mailové skupiny akcí](./media/manage-updates-for-vm/configure-email-action-group.png)

7. V podokně Přidat skupinu akcí vyberte **OK**.

8. U e-mailu s výstrahou můžete přizpůsobit předmět e-mailu. V části **vytvořit pravidlo** vyberte **přizpůsobit akce** a pak vyberte **Předmět e-mailu**.

9. Jakmile budete hotovi, vyberte **Vytvořit pravidlo upozornění**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [výstrahách v Azure monitor](../../azure-monitor/alerts/alerts-overview.md).

* Přečtěte si o [dotazech protokolu](../../azure-monitor/logs/log-query-overview.md) pro načtení a analýzu dat z log Analyticsho pracovního prostoru.

* Správa [využití a nákladů pomocí Azure Monitorch protokolů](../../azure-monitor/logs/manage-cost-storage.md) popisuje, jak řídit náklady změnou doby uchovávání dat a jak analyzovat a upozorňovat na využití vašich dat.