---
title: Příjem upozornění protokolu aktivit na oznámení ch služeb Azure
description: Získejte upozornění prostřednictvím SMS, e-mailu nebo webhooku, když dojde ke službě Azure.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d318adc76959ac24f4be9946167965a83053f632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749318"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Vytváření upozornění protokolu aktivit pro oznámení služby
## <a name="overview"></a>Přehled

Tento článek ukazuje, jak nastavit výstrahy protokolu aktivit pro oznámení o stavu služby pomocí portálu Azure.  

Oznámení o stavu služby jsou uloženy v [protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md) Vzhledem k tomu, že je možné velké množství informací uložených v protokolu aktivit, je samostatné uživatelské rozhraní, které usnadňuje zobrazení a nastavení výstrah na oznámení o stavu služby. 

Můžete obdržet upozornění, když Azure odesílá oznámení o stavu služby do vašeho předplatného Azure. Výstrahu můžete nakonfigurovat na základě:

- Třída oznámení o stavu služby (Problémy se službou, Plánovaná údržba, Doporučení stavu).
- Ovlivněné předplatné.
- Ovlivněné služby.
- Ovlivněný region(y).

> [!NOTE]
> Oznámení o stavu služby neodesílá výstrahu týkající se událostí stavu prostředků.

Můžete také nakonfigurovat, komu má být výstraha odeslána:

- Vyberte existující skupinu akcí.
- Vytvořte novou skupinu akcí (kterou lze použít pro budoucí výstrahy).

Další informace o skupinách akcí najdete v [tématu Vytváření a správa skupin akcí](../azure-monitor/platform/action-groups.md).

Informace o konfiguraci výstrah o stavu služby pomocí šablon Azure Resource Manageru najdete v [tématu Šablony Správce prostředků](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Podívejte se na video o nastavení prvnívýstrahy služby Azure Service Health

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Výstraha a nová skupina akcí pomocí portálu Azure
1. Na [portálu](https://portal.azure.com)vyberte **možnost Stav služby**.

    ![Služba "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. V části **Výstrahy** vyberte **výstrahy stavu**.

    ![Karta Výstrahy stavu](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Vyberte **Vytvořit výstrahu stavu služby** a vyplňte pole.

    ![Příkaz Vytvořit výstrahu stavu služby](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Vyberte **předplatné**, **služby**a **oblasti,** na které chcete být upozorňováni.

    ![Dialogové okno Přidat výstrahu protokolu aktivit](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Toto předplatné slouží k uložení výstrahy protokolu aktivit. Prostředek výstrahy se nasadí do tohoto předplatného a monitoruje události v protokolu aktivit pro něj.

1. Vyberte **typy událostí,** na které chcete být upozorněni: *Problém se službou*, *Plánovaná údržba*a *Informační zpravodaje stavu.* 

1. Podrobnosti výstrahy definujte zadáním názvu a **popisu** **pravidla výstrahy** .

1. Vyberte **skupinu Prostředků,** do které chcete výstrahu uložit.

1. Vytvořte novou skupinu akcí výběrem **nové skupiny akcí**. Zadejte název do pole **Název skupiny akcí** a do pole **Krátký název** zadejte název. Krátký název je odkazován v oznámení, které jsou odesílány při této výstrahy požáry.

    ![Vytvoření nové skupiny akcí](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Definujte seznam přijímačů poskytnutím přijímače:

    a. **Název**: Zadejte jméno, alias nebo identifikátor příjemce.

    b. **Typ akce:** Vyberte SMS, e-mail, webhook, aplikaci Azure a další.

    c. **Podrobnosti**: Na základě zvoleného typu akce zadejte telefonní číslo, e-mailovou adresu, identifikátor URI s webhookem atd.

1. Chcete-li vytvořit skupinu akcí, vyberte **OK** a potom **vytvořte pravidlo výstrahy** k dokončení výstrahy.

Během několika minut je výstraha aktivní a začne se aktivovat na základě podmínek, které jste zadali při vytváření.

Přečtěte [si, jak konfigurovat oznámení webhooku pro stávající systémy pro správu problémů](service-health-alert-webhook-guide.md). Informace o schématu webhooku pro výstrahy protokolu aktivit naleznete v tématu [Webhooks for Azure activity log alerts](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Skupina akcí definovaná v těchto krocích je opakovaně použitelná jako existující skupina akcí pro všechny budoucí definice výstrah.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Výstraha s existující skupinou akcí pomocí portálu Azure

1. Podle kroků 1 až 6 v předchozí části vytvořte oznámení o stavu služby. 

1. V části **Definovat skupinu akcí**klepněte na tlačítko **Vybrat skupinu akcí.** Vyberte příslušnou skupinu akcí.

1. Vyberte **Přidat,** chcete-li přidat skupinu akcí, a pak **vytvořit pravidlo výstrahy** k dokončení výstrahy.

Během několika minut je výstraha aktivní a začne se aktivovat na základě podmínek, které jste zadali při vytváření.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Výstraha a nová skupina akcí pomocí šablon Azure Resource Manager

Následuje příklad, který vytvoří skupinu akcí s cílem e-mailu a povolí všechna oznámení o stavu služby pro cílové předplatné.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>Správa výstrah

Po vytvoření výstrahy je viditelná v části **Výstrahy** **v monitoru**. Vyberte výstrahu, kterou chcete spravovat:

* Upravte ji.
* Smažte ji.
* Pokud chcete dočasně zastavit nebo obnovit příjem oznámení o výstraze, zakažte ji nebo ji povolte.

## <a name="next-steps"></a>Další kroky
- Přečtěte si [o doporučených postupech pro nastavení výstrah Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Přečtěte si, jak [nastavit mobilní nabízená oznámení pro Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Přečtěte si, jak [konfigurovat oznámení webhooku pro stávající systémy pro správu problémů](service-health-alert-webhook-guide.md).
- Další informace o [oznámeních o stavu služby](service-notifications.md).
- Informace o [omezení rychlosti oznamování](../azure-monitor/platform/alerts-rate-limiting.md).
- Zkontrolujte [webhookové schéma výstrahy protokolu aktivit](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Získejte [přehled upozornění protokolu aktivit](../azure-monitor/platform/alerts-overview.md)a zjistěte, jak přijímat výstrahy.
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/platform/action-groups.md).
