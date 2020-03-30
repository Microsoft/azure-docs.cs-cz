---
title: Vytváření výstrah Azure Advisor pro nová doporučení
description: Vytvoření výstrah Azure Advisor pro nové doporučení
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443155"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Vytváření výstrah Azure Advisor u nových doporučení 

Tento článek ukazuje, jak nastavit výstrahu pro nová doporučení od Azure Advisor pomocí portálu Azure a šablony Azure Resource Manager. 

Kdykoli Azure Advisor zjistí nové doporučení pro jeden z vašich prostředků, událost se uloží v [protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview). Můžete nastavit výstrahy pro tyto události z Azure Advisor pomocí prostředí pro vytváření výstrah specifických pro doporučení. Můžete vybrat předplatné a volitelně skupinu prostředků a určit prostředky, na které chcete dostávat výstrahy. 

Můžete také určit typy doporučení pomocí těchto vlastností:

* Kategorie
* Úroveň dopadu
* Typ doporučení

Můžete také nakonfigurovat akci, která se bude konat, když je výstraha spuštěna:  

* Výběr existující skupiny akcí
* Vytvoření nové skupiny akcí

Další informace o skupinách akcí najdete v [tématu Vytváření a správa skupin akcí](../azure-monitor/platform/action-groups.md).

> [!NOTE] 
> Upozornění poradců jsou v současné době k dispozici pouze pro doporučení vysoké dostupnosti, výkonu a nákladů. Doporučení zabezpečení nejsou podporována. 

## <a name="in-the-azure-portal"></a>Na webu Azure Portal
1. Na **portálu**vyberte **Azure Advisor**.

    ![Azure Advisor na portálu](./media/advisor-alerts/create1.png)

2. V části **Monitorování** v levé nabídce vyberte **výstrahy**. 

    ![Upozornění v poradci](./media/advisor-alerts/create2.png)

3. Vyberte **výstrahu nového poradce**.

    ![Výstraha nového poradce](./media/advisor-alerts/create3.png)

4. V části **Obor** vyberte předplatné a volitelně skupinu prostředků, na kterou chcete být upozorňováni. 

    ![Obor výstrahy poradce](./media/advisor-alerts/create4.png)

5. V části **Podmínka** vyberte metodu, kterou chcete použít pro konfiguraci výstrahy. Pokud chcete upozornit na všechna doporučení pro určitou kategorii a/nebo úroveň dopadu, vyberte **kategorii a úroveň dopadu**. Pokud chcete upozornit na všechna doporučení určitého typu, vyberte **typ doporučení**.

    ![Podmínka výstrahy Azure Advisor](./media/advisor-alerts/create5.png)

6. V závislosti na možnosti Konfigurovat podle, kterou vyberete, budete moci zadat kritéria. Pokud chcete všechna doporučení, nechte zbývající pole prázdná. 

    ![Skupina akcí upozornění poradce](./media/advisor-alerts/create6.png)

7. V části **Skupiny akcí** vyberte **Přidat existující,** chcete-li použít skupinu akcí, kterou jste již vytvořili, nebo vyberte **Vytvořit nový** a nastavte novou [skupinu akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups). 

    ![Upozornění poradce přidat existující](./media/advisor-alerts/create7.png)

8. V části Podrobnosti upozornění pojmenujte výstrahu, pojmenujete a uvězněte krátký popis. Pokud chcete, aby byla výstraha povolena, **ponechejte povolit pravidlo při výběru vytvoření** nastavenou na **ano**. Pak vyberte skupinu prostředků, do které chcete výstrahu uložit. To nebude mít vliv na rozsah cílení doporučení. 

    ![Azure Advisor Banner](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Se šablonou Azure Resource Manager

Tato šablona Správce prostředků vytvoří výstrahu doporučení a novou skupinu akcí.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
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
      "comments": "Azure Advisor Activity Log Alert",
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
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
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

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Konfigurace upozornění na doporučení pro použití webového háku
Tato část ukazuje, jak nakonfigurovat výstrahy Azure Advisor pro odesílání dat doporučení prostřednictvím webhooků do vašich stávajících systémů. 

Můžete nastavit výstrahy, které mají být upozorňovány, když máte nové doporučení poradce na jednom z vašich prostředků. Tato upozornění vás mohou upozornit prostřednictvím e-mailu nebo textové zprávy, ale mohou být také použity k integraci s vašimi stávajícími systémy prostřednictvím webhooku. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Použití datové části upozornění na doporučení poradce
Pokud chcete integrovat upozornění poradce do vlastních systémů pomocí webhooku, budete muset analyzovat datovou část JSON, která je odeslána z oznámení. 

Při nastavování skupiny akcí pro tuto výstrahu vyberete, zda chcete použít společné schéma výstrah. Pokud vyberete společné schéma výstrah, bude vaše datová část vypadat takto: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Pokud nepoužíváte běžné schéma, vaše datová část vypadá takto: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

V obou schématech můžete identifikovat události doporučení poradce hledáním `Recommendation` **eventSource** `Microsoft.Advisor/recommendations/available/action`je a **operationName** je .

Některé z dalších důležitých polí, které můžete chtít použít, jsou: 

* *alertTargetID (ve* společném schématu) nebo *resourceId* (starší schéma)
* *recommendationType*
* *název doporučení*
* *doporučeníKategorie*
* *doporučeníDopad*
* *doporučeníResourceLink*


## <a name="manage-your-alerts"></a>Správa výstrah 

Z Azure Advisor můžete upravit, odstranit nebo zakázat a povolit upozornění doporučení. 

1. Na **portálu**vyberte **Azure Advisor**.

    ![Azure Advisor Banner](./media/advisor-alerts/create1.png)

2. V části **Monitorování** v levé nabídce vyberte **výstrahy**.

    ![Azure Advisor Banner](./media/advisor-alerts/create2.png)

3. Chcete-li výstrahu upravit, kliknutím na název výstrahy otevřete výstrahu a upravíte pole, která chcete upravit.

4. Chcete-li výstrahu odstranit, povolit nebo zakázat, klikněte na elipsu na konci řádku a vyberte akci, kterou chcete provést.
 

