---
title: Vytvořit upozornění Azure Advisor pro nová doporučení | Microsoft Docs
description: Vytvořit upozornění Azure Advisor pro nové doporučení
services: advisor
author: sagupt
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/09/2019
ms.author: sagupt
ms.openlocfilehash: a67034752b4c43533a5735b857186ee83934717a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935635"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Vytváření upozornění Azure Advisor při nových doporučeních 

V tomto článku se dozvíte, jak nastavit upozornění pro nová doporučení od Azure Advisor pomocí šablon Azure Portal a Azure Resource Manager. 

Kdykoli Azure Advisor zjistí nové doporučení pro jeden z vašich prostředků, uloží se do [protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview). Pro tyto události můžete nastavit výstrahy z Azure Advisor pomocí prostředí pro vytváření výstrah specifických pro doporučení. Můžete vybrat předplatné a volitelně také skupinu prostředků a určit prostředky, na které chcete dostávat výstrahy. 

Můžete také určit typy doporučení pomocí těchto vlastností:

* Kategorie
* Úroveň dopadu
* Typ doporučení

Můžete taky nakonfigurovat akci, která se provede, když se aktivuje výstraha:  

* Výběr existující skupiny akcí
* Vytváří se nová skupina akcí.

Další informace o skupinách akcí naleznete v tématu [vytvoření a Správa skupin akcí] (.. /azure-monitor/platform/action-groups.md.

> [!NOTE] 
> Výstrahy služby Advisor jsou momentálně dostupné jenom pro doporučení vysoké dostupnosti, výkonu a nákladů. Doporučení zabezpečení nejsou podporovaná. 

## <a name="in-the-azure-portal"></a>V Azure Portal
1. Na **portálu**vyberte **Azure Advisor**.

    ![Azure Advisor na portálu](./media/advisor-alerts/create1.png)

2. V části **monitorování** v levé nabídce vyberte **výstrahy**. 

    ![Výstrahy v Advisoru](./media/advisor-alerts/create2.png)

3. Vyberte **nové upozornění poradce**.

    ![Nové upozornění poradce](./media/advisor-alerts/create3.png)

4. V části **obor** vyberte předplatné a volitelně skupinu prostředků, na které chcete být upozorňováni. 

    ![Rozsah upozornění Advisoru](./media/advisor-alerts/create4.png)

5. V části **Podmínka** vyberte metodu, kterou chcete použít pro konfiguraci výstrahy. Pokud chcete upozornit na všechna doporučení pro určitou kategorii a úroveň dopadu, vyberte **kategorie a úroveň dopadu**. Pokud chcete upozornit na všechna doporučení určitého typu, vyberte **typ doporučení**.

    ![Podmínka upozornění Azure Advisor](./media/advisor-alerts/create5.png)

6. V závislosti na vybrané možnosti konfigurace podle budete moct zadat kritéria. Pokud chcete všechna doporučení, ponechte zbývající pole prázdné. 

    ![Skupina akcí výstrah Advisoru](./media/advisor-alerts/create6.png)

7. V části **skupiny akcí** vyberte **Přidat existující** a použijte skupinu akcí, kterou jste už vytvořili, nebo vyberte **vytvořit novou** a nastavte novou [skupinu akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups). 

    ![Upozornění poradce přidat existující](./media/advisor-alerts/create7.png)

8. V části Podrobnosti výstrahy zadejte název a krátký popis výstrahy. Pokud chcete, aby byla výstraha povolená, nechte možnost **Povolit pravidlo při vytváření** výběru nastavenou na **Ano**. Pak vyberte skupinu prostředků, do které chcete upozornění Uložit. Tato akce nemá vliv na obor cíle doporučení. 

    ![Azure Advisor banner](./media/advisor-alerts/create8.png)


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

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Konfigurace výstrah doporučení pro použití Webhooku
V této části se dozvíte, jak nakonfigurovat výstrahy Azure Advisor pro posílání dat doporučení prostřednictvím webhooků do stávajících systémů. 

Můžete nastavit upozornění, která se mají upozornit, když máte nové doporučení Poradce pro jeden z vašich prostředků. Tyto výstrahy vás můžou poslat e-mailem nebo textovou zprávou, ale dají se taky použít k integraci s vašimi stávajícími systémy prostřednictvím Webhooku. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Použití datové části upozornění pro doporučení Advisoru
Pokud chcete integrovat výstrahy služby Advisor do vlastních systémů pomocí Webhooku, budete muset analyzovat datovou část JSON, která je odeslána z oznámení. 

Když nastavíte skupinu akcí pro tuto výstrahu, můžete vybrat, jestli chcete použít společné schéma výstrah. Pokud vyberete běžné schéma výstrah, bude datová část vypadat takto: 

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

Pokud nepoužíváte společné schéma, vaše datová část vypadá následovně: 

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

V obou schématech můžete identifikovat události doporučení poradce pomocí hledání **EventSource** `Recommendation` `Microsoft.Advisor/recommendations/available/action`a **operace** .

Mezi další důležitá pole, která byste mohli chtít použít, patří: 

* *alertTargetIDs* (ve společném schématu) nebo *ResourceID* (starší schéma)
* *recommendationType*
* *doporučení*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>Správa výstrah 

Z Azure Advisor můžete výstrahy pro doporučení upravit, odstranit nebo zakázat a povolit. 

1. Na **portálu**vyberte **Azure Advisor**.

    ![Azure Advisor banner](./media/advisor-alerts/create1.png)

2. V části **monitorování** v levé nabídce vyberte **výstrahy**.

    ![Azure Advisor banner](./media/advisor-alerts/create2.png)

3. Chcete-li upravit výstrahu, kliknutím na název výstrahy otevřete výstrahu a upravte pole, která chcete upravit.

4. Pokud chcete výstrahu odstranit, povolit nebo zakázat, klikněte na tři tečky na konci řádku a pak vyberte akci, kterou chcete provést.
 

