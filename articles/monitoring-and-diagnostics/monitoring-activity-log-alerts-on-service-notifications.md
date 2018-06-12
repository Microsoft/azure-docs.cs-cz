---
title: Výstrahy v protokolu aktivit na oznámení služby Azure
description: SMS, e-mailem nebo webhooku dostat upozornění, když dojde k služby Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 01dc3a3c6489b694af26c78ae3b4756f3e8f00b7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263112"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Vytvoření aktivity protokolu upozornění na oznámení o službách
## <a name="overview"></a>Přehled
Tento článek ukazuje, jak nastavit výstrahy protokolu aktivit pro oznámení o stavu služby pomocí portálu Azure.  

Můžete zobrazit upozornění, když Azure odesílá oznámení o stavu služby k předplatnému Azure. Můžete nakonfigurovat výstrahy na základě:

- Třída oznámení stavu služby (Service problémy, plánovaná údržba, stavu zpravodaje).
- Odběr vliv.
- Služeb vliv.
- Oblast(i) vliv.

Můžete také konfigurovat kdo výstraha by měly být odeslány na:

- Vyberte existující skupinu akce.
- Vytvořte novou skupinu akce (který můžete použít pro budoucí výstrahy).

Další informace o skupinách akce najdete v tématu [vytvořit a spravovat skupiny akce](monitoring-action-groups.md).

Informace o tom, jak nakonfigurovat služby stavu oznámení výstrah pomocí šablon Azure Resource Manageru najdete v tématu [šablony Resource Manageru](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Vytvořit výstrahu na oznámení stavu služby pro novou skupinu akce pomocí portálu Azure
1. V [portál](https://portal.azure.com), vyberte **stav služby**.

    ![Službu "Služba stavu"](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. V **výstrahy** vyberte **výstrahy stavu**.

    ![Na kartě "Stav výstrahy"](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. Vyberte **výstraha stavu služby vytvořit** a vyplňte příslušná pole.

    ![Příkaz "Vytvoření služby stavu upozornění"](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. Vyberte **předplatné**, **služby**, a **oblasti** chcete generovat výstrahy pro.

    ![Dialogové okno "Přidat aktivitu protokolu upozornění"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Toto předplatné se používá pro uložení výstraha aktivity protokolu. Výstrahy prostředků je nasazen na toto předplatné a sleduje události v protokolu aktivit pro ni.

5. Vyberte **typů událostí** chcete generovat výstrahy pro: *služby problém*, *plánované údržby*, a *zpravodaje stavu* 

6. Definovat zadáním podrobností o výstrahách **název pravidla výstrahy** a **popis**.

7. Vyberte **skupiny prostředků** místo, kam chcete výstrahu, kterou chcete uložit.

8. Vytvořit novou skupinu akce výběrem **nové skupiny akce**. Zadejte název do pole **název skupiny akce** pole a zadejte název do pole **krátký název** pole. Krátký název je odkazováno na oznámení, která se posílají, když se aktivuje se tato výstraha.

    ![Vytvořit novou skupinu akce](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

9. Definujte seznam příjemců tím, že poskytuje příjemce:

    a. **Název**: Zadejte název, alias nebo identifikátor příjemce.

    b. **Typ akce**: Vyberte SMS, e-mailu, webhooku, aplikace Azure a další.

    c. **Podrobnosti o**: v závislosti na typu akce vybrali, zadejte telefonní číslo, e-mailovou adresu, webhooku identifikátor URI, atd.

10. Vyberte **OK** se vytvořit skupinu akce a potom **vytvořit pravidlo výstrahy** k dokončení upozornění.

Během několika minut výstraha je aktivní a začne k aktivaci na základě podmínek, které jste zadali při vytváření.

Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy správy problém](../service-health/service-health-alert-webhook-guide.md). Informace o schématu webhooku pro aktivitu protokolu výstrahy naleznete v tématu [Webhooky Azure aktivity protokolu výstrahy](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Skupina akce definované v těchto kroků je opakovaně použitelné jako stávající skupina akce pro všechny budoucí výstrahy definice.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Vytvořit výstrahu na oznámení stavu služby pro stávající skupinu akce pomocí portálu Azure

1. Postupujte podle kroků 1 až 7 v předchozí části, chcete-li vytvořit oznámení služby stavu. 

2. V části **definovat akce skupiny**, klikněte na tlačítko **vyberte akce skupiny** tlačítko. Vyberte skupinu příslušnou akci.

3. Vyberte **přidat** k přidání skupiny akce a potom **vytvořit pravidlo výstrahy** k dokončení upozornění.

Během několika minut výstraha je aktivní a začne k aktivaci na základě podmínek, které jste zadali při vytváření.

## <a name="manage-your-alerts"></a>Spravovat oznámení

Po vytvoření výstrahy, se zobrazí na **výstrahy** části **monitorování**. Vyberte výstrahy, kterou chcete spravovat:

* Upravte.
* Odstraňte ji.
* Zakázat nebo povolit, pokud chcete dočasně zastavit nebo obnovit příjem oznámení pro výstrahy.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy správy problém](../service-health/service-health-alert-webhook-guide.md).
- Další informace o [oznámení o stavu služby](monitoring-service-notifications.md).
- Další informace o [omezení rychlosti oznámení](monitoring-alerts-rate-limiting.md).
- Zkontrolujte [schéma výstrahy webhooku protokolu činnosti](monitoring-activity-log-alerts-webhook.md).
- Získat [přehled výstrah aktivity protokolu](monitoring-overview-alerts.md)a zjistěte, jak dostávat výstrahy. 
- Další informace o [skupiny akcí](monitoring-action-groups.md).
