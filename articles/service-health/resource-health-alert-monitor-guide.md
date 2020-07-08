---
title: Vytváření upozornění Resource Health pomocí webu Azure Portal
description: Vytvořte výstrahu pomocí Azure Portal, která vás upozorní, když vaše prostředky Azure nebudou k dispozici.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: 2ba7597283ed254b8c03e8ea58c3c7b1257250ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85217995"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Konfigurace upozornění na stav prostředků s využitím webu Azure Portal

V tomto článku se dozvíte, jak nastavit výstrahy protokolu aktivit pro oznámení o stavu prostředků pomocí Azure Portal.

Azure Resource Health vás informují o aktuálním a historickém stavu vašich prostředků Azure. Výstrahy Azure Resource Health vás můžou zobrazit téměř v reálném čase, když tyto prostředky mají změnu stavu. Vytváření výstrah Resource Health programově umožňuje uživatelům vytvářet a přizpůsobovat hromadnou výstrahu.

> [!NOTE]
> Výstrahy Resource Health jsou v tuto chvíli ve verzi Preview.

Oznámení o stavu prostředků se ukládají do [protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview) , který vám poskytne potenciálně velký objem informací uložených v protokolu aktivit. k dispozici je samostatné uživatelské rozhraní, které usnadňuje zobrazení a nastavení výstrah pro oznámení o stavu prostředků.
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

Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

Informace o tom, jak nakonfigurovat výstrahy pro oznamování stavu prostředků pomocí šablon Azure Resource Manager, najdete v tématu [Správce prostředků Templates](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide).
Výstraha Resource Health pomocí Azure Portal

## <a name="resource-health-alert-using-azure-portal"></a>Výstraha Resource Health pomocí webu Azure Portal

1. Na webu Azure [Portal](https://portal.azure.com/)vyberte **Service Health**.

    ![Service Health výběr](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. V části **Resource Health** vyberte možnost **Resource Health**.
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
7. V části **Skupina akcí**v rozevírací nabídce určete skupinu akcí, kterou chcete přiřadit k tomuto novému pravidlu výstrahy. Případně můžete [vytvořit novou skupinu akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) a přiřadit ji k novému pravidlu. Pokud chcete vytvořit novou skupinu, vyberte + **Nová skupina**.
8. Chcete-li po vytvoření pravidla povolit, vyberte možnost **Ano** pro **pravidlo Povolit při vytváření** .
9. Vyberte **Vytvořit pravidlo upozornění**.

Vytvoří se nové pravidlo výstrahy pro protokol aktivit a v pravém horním rohu okna se zobrazí potvrzovací zpráva.
Pravidlo můžete povolit, zakázat, upravit nebo odstranit. Přečtěte si další informace o [tom, jak spravovat pravidla protokolů aktivit](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#view-and-manage-in-the-azure-portal).

## <a name="next-steps"></a>Další kroky

Další informace o Resource Health:

* [Přehled Azure Resource Health](Resource-health-overview.md)
* [Typy prostředků a kontroly stavu dostupné prostřednictvím služby Azure Resource Health](resource-health-checks-resource-types.md)

Vytvořit výstrahy Service Health:

* [Konfigurace upozornění pro Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
* [Schéma událostí protokolu aktivit Azure](../azure-monitor/platform/activity-log-schema.md)
* [Konfigurace upozornění na stav prostředků s využitím šablon Resource Manageru](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)