---
title: Vytvoření diagnostického nastavení v Azure pomocí šablony Správce prostředků
description: Vytvořte diagnostická nastavení pomocí šablony Správce prostředků k předávání protokolů platformy Azure do protokolů monitorování Azure, úložiště Azure nebo Center událostí Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672425"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Vytvoření diagnostického nastavení v Azure pomocí šablony Správce prostředků
[Diagnostická nastavení](diagnostic-settings.md) v Azure Monitoru určují, kam se mají odesílat [protokoly platformy,](platform-logs-overview.md) které jsou shromažďovány prostředky Azure a platformou Azure, na které závisí. Tento článek obsahuje podrobnosti a příklady pro použití [šablony Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) k vytvoření a konfiguraci nastavení diagnostiky pro shromažďování protokolů platformy do různých cílů.

> [!NOTE]
> Vzhledem k tomu, že nelze [vytvořit diagnostické nastavení](diagnostic-settings.md) pro protokol aktivit Azure pomocí PowerShellnebo CLI jako diagnostická nastavení pro jiné prostředky Azure, vytvořte šablonu Správce prostředků pro protokol aktivit pomocí informací v tomto článku a nasadit šablonu pomocí PowerShell nebo CLI.

## <a name="deployment-methods"></a>Metody nasazení
Šablony Správce prostředků můžete nasadit pomocí libovolné platné metody, včetně prostředí PowerShell a CLI. Diagnostická nastavení protokolu aktivit se `az deployment create` musí nasadit `New-AzDeployment` do předplatného pomocí zapoužití zaúžek nebo prostředí PowerShell. Diagnostická nastavení protokolů prostředků se musí `az group deployment create` nasadit `New-AzResourceGroupDeployment` do skupiny prostředků, která používá pro cli nebo pro PowerShell.

Podrobnosti [najdete v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md) a [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI.](../../azure-resource-manager/templates/deploy-cli.md) 





## <a name="resource-logs"></a>Protokoly prostředků
Pro protokoly prostředků přidejte `<resource namespace>/providers/diagnosticSettings` do šablony prostředek typu. Oddíl vlastností se řídí formátem popsaným v [části Nastavení diagnostiky – Vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Zadejte `category` v `logs` části pro každou z kategorií platných pro zdroj, který chcete shromáždit. Přidejte `metrics` vlastnost pro shromažďování metrik prostředků do stejných cílů, pokud [prostředek podporuje metriky](metrics-supported.md).

Následuje šablona, která shromažďuje kategorii protokolu prostředků pro konkrétní prostředek do pracovního prostoru Analýzy protokolů, účtu úložiště a centra událostí.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>Příklad
Následuje příklad, který vytvoří diagnostické nastavení pro nastavení automatického škálování, které umožňuje streamování protokolů prostředků do centra událostí, účtu úložiště a pracovního prostoru Analýzy protokolů.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Protokol aktivit
Pro protokol aktivit Azure přidejte `Microsoft.Insights/diagnosticSettings`prostředek typu . Dostupné kategorie jsou [uvedeny](activity-log-view.md#categories-in-the-activity-log)v seznamu Kategorie v protokolu aktivit . Následuje šablona, která shromažďuje všechny kategorie protokolu aktivit do pracovního prostoru Analýzy protokolů, účtu úložiště a centra událostí.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [protokolech platformy v Azure](platform-logs-overview.md).
* Informace o [diagnostických nastaveních](diagnostic-settings.md).
