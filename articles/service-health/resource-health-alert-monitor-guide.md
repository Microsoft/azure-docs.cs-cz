---
title: Vytváření upozornění Resource Health pomocí webu Azure Portal
description: Vytvořte výstrahu pomocí Azure Portal, která vás upozorní, když vaše prostředky Azure nebudou k dispozici.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: e48c400e5be3516b08496db7a4cb6a19e45d6c97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594629"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Konfigurace upozornění na stav prostředků s využitím webu Azure Portal

V tomto článku se dozvíte, jak nastavit výstrahy protokolu aktivit pro oznámení o stavu prostředků pomocí Azure Portal.

Azure Resource Health vás informují o aktuálním a historickém stavu vašich prostředků Azure. Upozornění služby Azure Resource Health vás můžou téměř v reálném čase informovat o změnách stavu těchto prostředků. Vytváření výstrah Resource Health programově umožňuje uživatelům vytvářet a přizpůsobovat hromadnou výstrahu.

Oznámení o stavu prostředků se ukládají do [protokolu aktivit Azure](../azure-monitor/essentials/platform-logs-overview.md) , který vám poskytne potenciálně velký objem informací uložených v protokolu aktivit. k dispozici je samostatné uživatelské rozhraní, které usnadňuje zobrazení a nastavení výstrah pro oznámení o stavu prostředků.
Můžete obdržet upozornění, když prostředek Azure pošle oznámení o stavu prostředků vašemu předplatnému Azure. Můžete nakonfigurovat výstrahu na základě:

* Ovlivněné předplatné.
* Počet ovlivněných typů prostředků:.
* Ovlivněné skupiny prostředků.
* Ovlivněné prostředky.
* Stav události vlivu na prostředky (y).
* Počet ovlivněných prostředků:
* Důvody ovlivněných prostředků (počet:).

Můžete také nakonfigurovat, komu má být upozornění odesláno:

* Vyberte existující skupinu akcí.
* Vytvořte novou skupinu akcí (kterou lze použít k budoucím výstrahám).

Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](../azure-monitor/alerts/action-groups.md).

Informace o tom, jak nakonfigurovat výstrahy pro oznamování stavu prostředků pomocí šablon Azure Resource Manager, najdete v tématu [Správce prostředků Templates](./resource-health-alert-arm-template-guide.md).
Výstraha Resource Health pomocí Azure Portal

## <a name="resource-health-alert-using-azure-portal"></a>Výstraha Resource Health pomocí webu Azure Portal

1. Na webu Azure [Portal](https://portal.azure.com/)vyberte **Service Health**.

    ![Service Health výběr](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. V části **Resource Health** vyberte možnost **Service Health**.
3. Vyberte **Přidat upozornění na stav prostředku** a vyplňte pole.
4. V části cíl výstrahy vyberte **předplatné**, **typy prostředků**, **skupiny prostředků** a **prostředek** , pro které chcete být upozorňováni.

    ![Výběr cíle výběru](./media/resource-health-alert-monitor-guide/alert-target.png)

5. V části podmínka výstrahy vyberte:
    1. **Stav události** , pro který chcete být upozorněni. Úroveň závažnosti události: aktivní, Vyřešeno, probíhá, Aktualizováno
    2. **Stav prostředku** , pro který chcete být upozorňováni. Stav prostředku události: k dispozici, nedostupný, neznámý, snížený
    3. **Typ důvodu** , pro který chcete být upozorněni. Příčina události: iniciovaná platforma, uživatelem iniciovaná možnost výběru ![ stavu výstrah](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. V části definovat podrobnosti výstrahy zadejte následující podrobnosti:
    1. **Název pravidla výstrahy**: název nového pravidla výstrahy.
    2. **Popis**: popis nového pravidla výstrahy.
    3. **Uložit upozornění do skupiny prostředků**: vyberte skupinu prostředků, do které chcete toto nové pravidlo Uložit.
7. V části **Skupina akcí** v rozevírací nabídce určete skupinu akcí, kterou chcete přiřadit k tomuto novému pravidlu výstrahy. Případně můžete [vytvořit novou skupinu akcí](../azure-monitor/alerts/action-groups.md) a přiřadit ji k novému pravidlu. Pokud chcete vytvořit novou skupinu, vyberte + **Nová skupina**.
8. Chcete-li po vytvoření pravidla povolit, vyberte možnost **Ano** pro **pravidlo Povolit při vytváření** .
9. Vyberte **Vytvořit pravidlo upozornění**.

Vytvoří se nové pravidlo výstrahy pro protokol aktivit a v pravém horním rohu okna se zobrazí potvrzovací zpráva.
Pravidlo můžete povolit, zakázat, upravit nebo odstranit. Přečtěte si další informace o [tom, jak spravovat pravidla protokolů aktivit](../azure-monitor/alerts/alerts-activity-log.md#view-and-manage-in-the-azure-portal).

## <a name="next-steps"></a>Další kroky

Další informace o Resource Health:

* [Přehled Azure Resource Health](Resource-health-overview.md)
* [Typy prostředků a kontroly stavu dostupné prostřednictvím služby Azure Resource Health](resource-health-checks-resource-types.md)

Vytvořit výstrahy Service Health:

* [Konfigurace upozornění pro Service Health](./alerts-activity-log-service-notifications-portal.md) 
* [Schéma událostí protokolu aktivit Azure](../azure-monitor/essentials/activity-log-schema.md)
* [Konfigurace upozornění na stav prostředků s využitím šablon Resource Manageru](./resource-health-alert-arm-template-guide.md)
