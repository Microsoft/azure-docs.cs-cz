---
title: Vytváření skupin akcí pomocí šablon Správce prostředků
description: Naučte se vytvořit skupinu akcí pomocí šablony Azure Resource Manager.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 2275ea059b762e81330d3e6150c563e18a64b554
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609706"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Vytvoření skupiny akcí s Správce prostředků šablonou
V tomto článku se dozvíte, jak použít [šablonu Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) ke konfiguraci skupin akcí. Pomocí šablon můžete automaticky nastavit skupiny akcí, které se dají znovu použít v určitých typech výstrah. Tyto skupiny akcí zajistí, že při aktivaci výstrahy budou všechny správné strany upozorněny.

Základní kroky:

1. Vytvořte šablonu jako soubor JSON, který popisuje, jak vytvořit skupinu akcí.

2. Nasaďte šablonu pomocí [jakékoli metody nasazení](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="resource-manager-templates-for-an-action-group"></a>Šablony Správce prostředků pro skupinu akcí

Chcete-li vytvořit skupinu akcí pomocí šablony Správce prostředků, vytvořte prostředek typu `Microsoft.Insights/actionGroups` . Pak vyplníte všechny související vlastnosti. Tady jsou dvě vzorové šablony, které vytvářejí skupinu akcí.

První šablona popisuje, jak vytvořit šablonu Správce prostředků pro skupinu akcí, kde jsou definice akcí pevně zakódované v šabloně. Druhá šablona popisuje, jak vytvořit šablonu, která při nasazení šablony převezme informace o konfiguraci Webhooku jako vstupní parametry.

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


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [skupinách akcí](../platform/action-groups.md).
* Přečtěte si další informace o [výstrahách](../platform/alerts-overview.md).
* Naučte se přidávat [výstrahy pomocí šablony Správce prostředků](../platform/alerts-activity-log.md).

