---
title: Vytvořit výstrahu protokolu aktivit pomocí šablony Resource Manageru
description: Upozorňování při vytváření prostředků Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2017
ms.author: ancav
ms.component: alerts
ms.openlocfilehash: a1e28f08231ae1fbef3e0d0306e986c1dc9d1d1c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262986"
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Vytvořit výstrahu protokolu aktivit pomocí šablony Resource Manageru
V tomto článku se dozvíte, jak používat [šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) konfigurace aktivity protokolu výstrah. Pomocí šablon můžete snadno nastavit více výstrah, které aktivovat na základě konkrétní aktivitu protokolu události podmínek jako součást procesu automatického nasazení.

Toto jsou základní kroky:

1. Vytvořte šablonu jako soubor JSON, který popisuje, jak vytvořit výstrahu protokolu aktivit.

2. Nasazení šablony pomocí [libovolnou metodu nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Šablony Resource Manageru pro výstrahu protokolu aktivit
Vytvořit výstrahu protokolu aktivit pomocí šablony Resource Manageru, vytvořit prostředek typu `microsoft.insights/activityLogAlerts`. Potom můžete vyplnit všech souvisejících vlastností. Zde je šablonu, která vytvoří výstrahu protokolu aktivit.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Navštivte naše [galerii pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights) pro některé příklady výstrahy šablony protokolu aktivit.

> [!NOTE]

> Můžete také vytvořit aktivity protokolu pravidla výstrah pomocí vylepšené uživatelské prostředí v nástroji Sledování > [výstrahy (Preview)](monitoring-overview-unified-alerts.md). Další informace o tom, jak vytvořit, naleznete v části [v tomto článku](monitoring-activity-log-alerts-new-experience.md).

## <a name="next-steps"></a>Další postup
- Další informace o [výstrahy](monitoring-overview-alerts.md).
- Informace o postupu přidání [skupiny akce pomocí šablony Resource Manageru](monitoring-create-action-group-with-resource-manager-template.md).
- Zjistěte, jak [vytvořit výstrahu protokolu aktivitu monitorovat všechny operace škálování modul vaše předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Zjistěte, jak [vytvořit výstrahu protokolu aktivitu monitorovat všechny operace škálování nebo škálovatelnou neúspěšné automatické škálování na vaše předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
