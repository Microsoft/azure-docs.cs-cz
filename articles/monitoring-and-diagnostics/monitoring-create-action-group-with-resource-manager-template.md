---
title: Vytvoření skupin akce pomocí šablony Resource Manageru
description: Naučte se vytvořit skupinu akce pomocí šablony Azure Resource Manager.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 9b49d21dad9bb1e48194cc31940c5cd53c909dc0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263095"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Vytvořit skupinu akce pomocí šablony Resource Manageru
V tomto článku se dozvíte, jak používat [šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) ke konfiguraci skupin akce. Pomocí šablony můžete automaticky nastavit skupiny akce, které lze znovu použít v určitých typů výstrahy. Tyto skupiny akce Ujistěte se, že všechny správné strany se upozorní, když se výstraha.

Toto jsou základní kroky:

1. Vytvořte šablonu jako soubor JSON, který popisuje, jak vytvořit skupinu akce.

2. Nasazení šablony pomocí [libovolnou metodu nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Nejdřív jsme popisují, jak vytvořit šablonu Resource Manageru pro skupinu akcí, kde definice akce jsou pevně zakódovaná v šabloně. Druhý jsme popisují, jak vytvořit šablonu, která přebírá informace o konfiguraci webhooku jako vstupní parametry při nasazení šablony.

## <a name="resource-manager-templates-for-an-action-group"></a>Šablony Resource Manageru pro skupinu akce

Pokud chcete vytvořit skupinu akce pomocí šablony Resource Manageru, vytvořit prostředek typu `Microsoft.Insights/actionGroups`. Potom můžete vyplnit všech souvisejících vlastností. Tady jsou dvě ukázkové šablony, které vytvořit skupinu akce.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Další postup
* Další informace o [skupiny akcí](monitoring-action-groups.md).
* Další informace o [výstrahy](monitoring-overview-alerts.md).
* Informace o postupu přidání [výstrah pomocí šablony Resource Manageru](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
