---
title: Vytvoření nastavení diagnostiky v Azure pomocí šablony Správce prostředků
description: Vytvořte nastavení diagnostiky pomocí šablony Správce prostředků pro přeposílání protokolů platformy Azure do protokolů Azure Monitor, Azure Storage nebo Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 73f2b3ea90cc94fa3411552c7b812fe53eb4dbbb
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607320"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Vytvoření nastavení diagnostiky v Azure pomocí šablony Správce prostředků
[Nastavení diagnostiky](diagnostic-settings.md) v Azure monitor určují, kam se mají posílat [protokoly platforem](platform-logs-overview.md) , které shromažďují prostředky Azure, a platforma Azure, na které jsou závislé. V tomto článku najdete podrobné informace a příklady použití [šablony Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) k vytvoření a konfiguraci nastavení diagnostiky pro shromažďování protokolů platforem do různých umístění.

> [!NOTE]
> Vzhledem k tomu, že nemůžete [vytvořit nastavení diagnostiky](diagnostic-settings.md) pro protokol aktivit Azure pomocí PowerShellu nebo rozhraní příkazového řádku, jako je nastavení diagnostiky pro jiné prostředky Azure, vytvořte šablonu správce prostředků pro protokol aktivit pomocí informací v tomto článku a nasaďte tuto šablonu pomocí PowerShellu nebo rozhraní příkazového řádku.

## <a name="deployment-methods"></a>Metody nasazení
Šablony Správce prostředků můžete nasadit pomocí libovolné platné metody, včetně PowerShellu a rozhraní příkazového řádku. Nastavení diagnostiky pro protokol aktivit musí být nasazeno do předplatného pomocí rozhraní příkazového `az deployment create` řádku nebo `New-AzDeployment` PowerShellu. Nastavení diagnostiky pro protokoly prostředků se musí nasadit do skupiny prostředků pomocí rozhraní příkazového `az group deployment create` řádku nebo `New-AzResourceGroupDeployment` PowerShellu.

Podrobnosti najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) a [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md) . 





## <a name="resource-logs"></a>Protokoly prostředků
V případě protokolů prostředků přidejte do šablony prostředek typu `<resource namespace>/providers/diagnosticSettings` . Oddíl Properties (vlastnosti) se řídí formátem popsaným v tématu [nastavení diagnostiky – vytvořit nebo aktualizovat](/rest/api/monitor/diagnosticsettings/createorupdate). Zadejte `category` v `logs` části pro každou z kategorií platných pro prostředek, který chcete shromáždit. Přidejte `metrics` vlastnost ke shromáždění metrik prostředků do stejných cílů, pokud [prostředek podporuje metriky](metrics-supported.md).

Následuje šablona, která shromažďuje kategorii protokolu prostředků pro určitý prostředek do Log Analyticsho pracovního prostoru, účtu úložiště a centra událostí.

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
Následuje příklad, který vytvoří nastavení diagnostiky pro nastavení automatického škálování, které umožňuje streamování protokolů prostředků do centra událostí, účtu úložiště a Log Analytics pracovního prostoru.

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
Pro protokol aktivit Azure Přidejte prostředek typu `Microsoft.Insights/diagnosticSettings` . Kategorie k dispozici jsou uvedené v [protokolech aktivit v protokolu aktivit](./activity-log.md#view-the-activity-log). Následuje šablona, která shromažďuje všechny kategorie protokolů aktivit do Log Analyticsho pracovního prostoru, účtu úložiště a centra událostí.


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
* Přečtěte si další informace o [protokolech platforem v Azure](platform-logs-overview.md).
* Přečtěte si o [nastavení diagnostiky](diagnostic-settings.md).
