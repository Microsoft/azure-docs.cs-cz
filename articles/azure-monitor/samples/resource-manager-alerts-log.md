---
title: Ukázky šablon Správce prostředků pro výstrahy dotazů protokolu
description: Ukázka Azure Resource Manager šablon pro nasazení Azure Monitor výstrah dotazů protokolu.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: 0da8698026e897adb72792970e8d0565e9ec73f0
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "83854442"
---
# <a name="resource-manager-template-samples-for-log-alert-rules-in-azure-monitor"></a>Ukázky šablon Správce prostředků pro pravidla upozornění protokolů v Azure Monitor
Tento článek obsahuje vzorové [Azure Resource Manager šablony](../../azure-resource-manager/templates/template-syntax.md) pro vytváření a konfiguraci výstrah dotazů protokolu v Azure monitor. Každá ukázka obsahuje soubor šablony a soubor parametrů s ukázkovými hodnotami, které se mají poskytnout šabloně.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]



## <a name="number-of-results-alert-rule"></a>Počet pravidel upozornění na výsledky
Následující ukázka vytvoří [počet pravidel upozornění výsledků](../platform/alerts-unified-log.md#metric-measurement-alert-rules).

### <a name="notes"></a>Poznámky

- Tato ukázka zahrnuje [datovou část Webhooku](../platform/alerts-log-webhook.md). Pokud pravidlo výstrahy nemá aktivovat Webhook, odeberte element **customWebhookPayload** .

### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the Log Analytisc workspace."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for the alert. Must be the same location as the workspace."
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated."
            }
        }
    },
    "resources":[ 
        {
            "type":"Microsoft.Insights/scheduledQueryRules",
            "name":"Sample log query alert",
            "apiVersion": "2018-04-16",
            "location": "[parameters('location')]",
            "properties":{
                "description": "Sample log query alert",
                "enabled": "true",
                "source": {
                    "query": "Event | where EventLevelName == \"Error\" | summarize count() by Computer",
                    "dataSourceId": "[parameters('sourceId')]",
                    "queryType":"ResultCount"
                },
                "schedule":{
                    "frequencyInMinutes": 15,
                    "timeWindowInMinutes": 60
                },
                "action":{
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                    "severity": "4",
                    "aznsAction":{
                        "actionGroup": "[array(parameters('actionGroupId'))]",
                        "emailSubject": "Alert mail subject",
                        "customWebhookPayload":"{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
                    },
                    "trigger":{
                        "thresholdOperator": "GreaterThan",
                        "threshold": 1
                    }
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "sourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/bw-samples-arm/providers/microsoft.operationalinsights/workspaces/bw-arm-01"
      },
      "location": {
        "value": "westus"
      },
      "actionGroupId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/bw-samples-arm/providers/microsoft.insights/actionGroups/ARM samples group 01"
      }
  }
}
```

## <a name="metric-measurement-alert"></a>Výstraha měření metriky
Následující ukázka vytvoří [pravidlo upozornění měření metriky](../platform/alerts-unified-log.md#metric-measurement-alert-rules).

### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the Log Analytics workspace."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for the alert. Must be the same location as the workspace."
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated."
            }
        }
    },
    "resources":[ 
        {
            "type":"Microsoft.Insights/scheduledQueryRules",
            "name":"Sample metric measurement log query alert",
            "apiVersion": "2018-04-16",
            "location": "[parameters('location')]",
            "properties":{
                "description": "Sample metric measurement query alert rule",
                "enabled": "true",
                "source": {
                    "query": "Event | where EventLevelName == \"Error\" | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Computer",
                    "dataSourceId": "[parameters('sourceId')]",
                    "queryType":"ResultCount"
                },
                "schedule":{
                    "frequencyInMinutes": 15,
                    "timeWindowInMinutes": 60
                },
                "action":{
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                    "severity": "4",
                    "aznsAction":{
                        "actionGroup": "[array(parameters('actionGroupId'))]",
                        "emailSubject": "Alert mail subject"
                    },
                    "trigger":{
                        "thresholdOperator": "GreaterThan",
                        "threshold": 10,
                        "metricTrigger":{
                            "thresholdOperator": "Equal",
                            "threshold": 1,
                            "metricTriggerType": "Consecutive",
                            "metricColumn": "Computer"
                        }
                    }
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "sourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/bw-samples-arm/providers/microsoft.operationalinsights/workspaces/bw-arm-01"
      },
      "location": {
        "value": "westus"
      },
      "actionGroupId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/bw-samples-arm/providers/microsoft.insights/actionGroups/ARM samples group 01"
      }
  }
}
```

## <a name="next-steps"></a>Další kroky

* [Získejte další ukázkové šablony pro Azure monitor](resource-manager-samples.md).
* [Přečtěte si další informace o pravidlech výstrah](../platform/alerts-overview.md).

