---
title: Doručování výstrah protokolu aktivit v oznámeních o službě Azure pomocí Azure Portal
description: Naučte se používat Azure Portal k nastavení výstrah protokolu aktivit pro oznámení o stavu služby pomocí Azure Portal.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48126d923cb0baa33058c6fd55e48f31d793fade
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570172"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Vytváření výstrah protokolu aktivit u oznámení služby pomocí Azure Portal
## <a name="overview"></a>Přehled

V tomto článku se dozvíte, jak použít Azure Portal k nastavení výstrah protokolu aktivit pro oznámení o stavu služby pomocí Azure Portal.  

Oznámení o stavu služby se ukládají do [protokolu aktivit Azure](../azure-monitor/essentials/platform-logs-overview.md). Vzhledem k velkému objemu informací uložených v protokolu aktivit je k dispozici samostatné uživatelské rozhraní, které usnadňuje zobrazení a nastavení výstrah pro oznámení o stavu služby. 

Když Azure pošle oznámení o stavu služby do vašeho předplatného Azure, můžete obdržet upozornění. Můžete nakonfigurovat výstrahu na základě:

- Třída oznámení o stavu služby (problémy se službami, plánovaná údržba, Poradce pro stav, informační zpravodaje zabezpečení).
- Ovlivněné předplatné.
- Služba (y) ovlivnila.
- Ovlivněné oblasti.

> [!NOTE]
> Oznámení o stavu služby neodesílají výstrahy pro události stavu prostředku.

Můžete také nakonfigurovat, komu má být upozornění odesláno:

- Vyberte existující skupinu akcí.
- Vytvořte novou skupinu akcí (kterou lze použít k budoucím výstrahám).

Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](../azure-monitor/alerts/action-groups.md).

Informace o tom, jak nakonfigurovat upozornění na stav služby pomocí šablon Azure Resource Manager, najdete v tématu [Správce prostředků Templates](../azure-monitor/alerts/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Podívejte se na video o nastavení prvního Azure Service Health výstrahy.

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Vytvoření výstrahy Service Health pomocí Azure Portal
1. Na [portálu](https://portal.azure.com)vyberte **Service Health**.

    ![Služba Service Health](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. V části **výstrahy** vyberte výstrahy týkající se **stavu**.

    ![Karta upozornění na stav](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Vyberte **Přidat upozornění na stav služby** a vyplňte pole.

    ![Příkaz "vytvořit upozornění na stav služby"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Vyberte **předplatné**, **služby** a **oblasti** , pro které chcete být upozorňováni.

    [![Dialogové okno Přidat upozornění protokolu aktivit](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>Toto předplatné slouží k uložení upozornění protokolu aktivit. Prostředek výstrahy se nasadí do tohoto předplatného a monitoruje události v protokolu aktivit.

5. Vyberte **typy událostí** , pro které chcete být upozorňováni: *problém služby*, *plánovaná údržba*, *Poradce pro stav* a *zpravodaj zabezpečení*.

6. Klikněte na **Vybrat skupinu akcí** a zvolte existující skupinu akcí nebo vytvořte novou skupinu akcí. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups in the Azure Portal](../azure-monitor/alerts/action-groups.md).


7. Zadejte podrobnosti výstrahy zadáním názvu a **popisu** **pravidla výstrahy** .

8. Vyberte **skupinu prostředků** , do které chcete výstrahu Uložit.



Během několika minut je výstraha aktivní a na základě podmínek, které jste zadali během vytváření, se spustí Trigger.

Naučte se [Konfigurovat oznámení Webhooku pro stávající systémy správy problémů](service-health-alert-webhook-guide.md). Informace o schématu Webhooku pro výstrahy protokolu aktivit najdete v tématu [Webhooky pro výstrahy protokolu aktivit Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).


## <a name="next-steps"></a>Další kroky
- Přečtěte si o [osvědčených postupech pro nastavení výstrah Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Přečtěte si, jak [nastavit mobilní nabízená oznámení pro Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Naučte se [Konfigurovat oznámení Webhooku pro stávající systémy správy problémů](service-health-alert-webhook-guide.md).
- Přečtěte si o [oznámeních o stavu služby](service-notifications.md).
- Seznamte se s [omezením rychlosti oznámení](../azure-monitor/alerts/alerts-rate-limiting.md).
- Zkontrolujte [schéma Webhooku upozornění protokolu aktivit](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Získejte [Přehled výstrah protokolu aktivit](../azure-monitor/alerts/alerts-overview.md)a Naučte se přijímat výstrahy.
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/alerts/action-groups.md).
