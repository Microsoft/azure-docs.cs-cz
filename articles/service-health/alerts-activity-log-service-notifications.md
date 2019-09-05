---
title: Doručení výstrah protokolu aktivit v oznámeních služby Azure
description: Když dojde ke službě Azure, dostanete oznámení prostřednictvím SMS, e-mailu nebo Webhooku.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 40ffe0b377a5cbb21f07c479097958d7c15a2879
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383159"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Vytváření výstrah protokolu aktivit u oznámení služby
## <a name="overview"></a>Přehled

V tomto článku se dozvíte, jak nastavit výstrahy protokolu aktivit pro oznámení o stavu služby pomocí Azure Portal.  

Oznámení o stavu služby se ukládají do [protokolu aktivit Azure](../azure-monitor/platform/activity-logs-overview.md) , který dostal potenciálně velký objem informací uložených v protokolu aktivit, existuje samostatné uživatelské rozhraní, které usnadňuje zobrazení a nastavení výstrah pro oznámení o stavu služby. 

Když Azure pošle oznámení o stavu služby do vašeho předplatného Azure, můžete obdržet upozornění. Můžete nakonfigurovat výstrahu na základě:

- Třída oznámení o stavu služby (problémy se službami, plánovaná údržba, Poradce pro stav).
- Ovlivněné předplatné.
- Služba (y) ovlivnila.
- Ovlivněné oblasti.

> [!NOTE]
> Oznámení o stavu služby neodesílají výstrahy týkající se událostí stavu prostředku.

Můžete také nakonfigurovat, komu má být upozornění odesláno:

- Vyberte existující skupinu akcí.
- Vytvořte novou skupinu akcí (kterou lze použít k budoucím výstrahám).

Další informace o skupinách akcí najdete v tématu [Vytváření a správa skupin akcí](../azure-monitor/platform/action-groups.md).

Informace o tom, jak nakonfigurovat upozornění na stav služby pomocí šablon Azure Resource Manager, najdete v tématu [Správce prostředků Templates](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Podívejte se na video o nastavení prvního Azure Service Health výstrahy.

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Upozornění a nová skupina akcí pomocí Azure Portal
1. Na [portálu](https://portal.azure.com)vyberte **Service Health**.

    ![Služba Service Health](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. V části **výstrahy** vyberte výstrahy týkající se **stavu**.

    ![Karta upozornění na stav](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Vyberte **vytvořit upozornění na stav služby** a vyplňte pole.

    ![Příkaz "vytvořit upozornění na stav služby"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Vyberte **předplatné**, **služby**a **oblasti** , pro které chcete být upozorňováni.

    ![Dialogové okno Přidat upozornění protokolu aktivit](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Toto předplatné slouží k uložení upozornění protokolu aktivit. Prostředek výstrahy se nasadí do tohoto předplatného a monitoruje události v protokolu aktivit.

1. Vyberte **typy událostí** , pro které chcete být upozorňováni: *Problém se službou*, *plánovaná údržba*a *Poradce pro stav* 

1. Zadejte podrobnosti výstrahy zadáním názvu a **popisu** **pravidla výstrahy** .

1. Vyberte **skupinu prostředků** , do které chcete výstrahu Uložit.

1. Vytvořte novou skupinu akcí výběrem **nové skupiny akcí**. Zadejte název do pole **název skupiny akcí** a zadejte název do pole **krátký název** . Na zkrácený název se odkazuje v oznámeních, která jsou odeslána při aktivaci této výstrahy.

    ![Vytvoří novou skupinu akcí.](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Definujte seznam přijímačů tím, že poskytnete přijímače:

    a. **Název**: Zadejte jméno, alias nebo identifikátor přijímače.

    b. **Typ akce**: Vyberte SMS, e-mail, Webhook, Azure App a další.

    c. **Podrobnosti**: Na základě zvoleného typu akce zadejte telefonní číslo, e-mailovou adresu, identifikátor URI Webhooku atd.

1. Výběrem **OK** vytvořte skupinu akcí a pak **Vytvořte pravidlo upozornění** pro dokončení výstrahy.

Během několika minut je výstraha aktivní a na základě podmínek, které jste zadali během vytváření, se spustí Trigger.

Naučte se [Konfigurovat oznámení Webhooku pro stávající systémy správy problémů](service-health-alert-webhook-guide.md). Informace o schématu Webhooku pro výstrahy protokolu aktivit najdete v tématu [Webhooky pro výstrahy protokolu aktivit Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Skupina akcí definovaná v těchto krocích se opakovaně používá jako existující skupina akcí pro všechny budoucí definice výstrah.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Upozornění s existující skupinou akcí pomocí Azure Portal

1. Postupujte podle kroků 1 až 6 v předchozí části a vytvořte oznámení o stavu služby. 

1. V části **definovat skupinu akcí**klikněte na tlačítko **Vybrat skupinu akcí** . Vyberte příslušnou skupinu akcí.

1. Vyberte **Přidat** a přidejte skupinu akcí a pak **Vytvořte pravidlo upozornění** pro dokončení výstrahy.

Během několika minut je výstraha aktivní a na základě podmínek, které jste zadali během vytváření, se spustí Trigger.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Upozornění a nová skupina akcí pomocí šablon Azure Resource Manager

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

Po vytvoření výstrahy je tato výstraha zobrazená v části **výstrahy** **monitorování**. Vyberte výstrahu, kterou chcete spravovat:

* Upravte ji.
* Odstraňte ji.
* Zakázat nebo povolit, pokud chcete dočasně zastavit nebo obnovit přijímání oznámení pro výstrahu.

## <a name="next-steps"></a>Další postup
- Přečtěte si o [osvědčených postupech pro nastavení výstrah Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Přečtěte si, jak [nastavit mobilní nabízená oznámení pro Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Naučte se [Konfigurovat oznámení Webhooku pro stávající systémy správy problémů](service-health-alert-webhook-guide.md).
- Přečtěte si o [oznámeních o stavu služby](service-notifications.md).
- Seznamte se s [omezením rychlosti oznámení](../azure-monitor/platform/alerts-rate-limiting.md).
- Zkontrolujte [schéma Webhooku upozornění protokolu aktivit](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Získejte [Přehled výstrah protokolu aktivit](../azure-monitor/platform/alerts-overview.md)a Naučte se přijímat výstrahy.
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/platform/action-groups.md).
