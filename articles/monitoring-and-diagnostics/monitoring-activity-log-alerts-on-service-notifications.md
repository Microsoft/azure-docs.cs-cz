---
title: Výstrahy v protokolu aktivit na oznámení služby Azure | Microsoft Docs
description: SMS, e-mailem nebo webhooku dostat upozornění, když dojde k služby Azure.
author: johnkemnetz
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: johnkem
ms.openlocfilehash: b4c4fdeb825bbcab54f074c5224140282a24d196
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
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

4. Zadejte název do pole **výstrahy název aktivity protokolu** pole a zadejte **popis**.

    ![Dialogové okno "Přidat aktivitu protokolu upozornění"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group-sh.png)

5. **Předplatné** pole autofills s vaším aktuálním předplatným. Toto předplatné se používá pro uložení výstraha aktivity protokolu. Výstrahy prostředků je nasazen na toto předplatné a sleduje události v protokolu aktivit pro ni.

6. Vyberte **skupiny prostředků** v výstrahy prostředek je vytvořen. Tato akce není skupině prostředků, který je monitorován výstraha. Místo toho je skupina prostředků, kde je umístěný prostředek výstrahy.

7. **Kategorie události** pole se automaticky nastaví na **stav služby**. Volitelně vyberte **služby**, **oblast**, a **typ** z oznámení o stavu služby, které chcete dostávat.

8. V části **výstrahy prostřednictvím**, vyberte **nový** tlačítko akce skupiny. Zadejte název do pole **název skupiny akce** pole a zadejte název do pole **krátký název** pole. Krátký název je odkazováno na oznámení, která se posílají, když se aktivuje se tato výstraha.

9. Definujte seznam příjemců tím, že poskytuje příjemce:

    a. **Název**: Zadejte název, alias nebo identifikátor příjemce.

    b. **Typ akce**: Vyberte SMS, e-mailu, webhooku, aplikace Azure a další.

    c. **Podrobnosti o**: v závislosti na typu akce vybrali, zadejte telefonní číslo, e-mailovou adresu, webhooku identifikátor URI, atd.

10. Vyberte **OK** vytvořit výstrahu.

Během několika minut výstraha je aktivní a začne k aktivaci na základě podmínek, které jste zadali při vytváření.

Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy správy problém](../service-health/service-health-alert-webhook-guide.md). Informace o schématu webhooku pro aktivitu protokolu výstrahy naleznete v tématu [Webhooky Azure aktivity protokolu výstrahy](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Skupina akce definované v těchto kroků je opakovaně použitelné jako stávající skupina akce pro všechny budoucí výstrahy definice.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Vytvořit výstrahu na oznámení stavu služby pro stávající skupinu akce pomocí portálu Azure

1. Postupujte podle kroků 1 až 7 v předchozí části, chcete-li vytvořit oznámení služby stavu. 

2. V části **výstrahy prostřednictvím**, vyberte **existující** tlačítko akce skupiny. Vyberte skupinu příslušnou akci.

3. Vyberte **OK** vytvořit výstrahu.

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
