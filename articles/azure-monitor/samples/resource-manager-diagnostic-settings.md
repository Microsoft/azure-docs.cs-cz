---
title: Ukázky šablon Správce prostředků pro nastavení diagnostiky
description: Ukázky Azure Resource Manager šablon, které Azure Monitor nastavení diagnostiky použít u prostředku Azure.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: 4330f70328d00766c829478cebeb2cdbb9ad21c1
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83854478"
---
# <a name="resource-manager-template-samples-for-diagnostic-settings-in-azure-monitor"></a>Ukázky Správce prostředků šablon pro nastavení diagnostiky v Azure Monitor
Tento článek obsahuje vzorové [Azure Resource Manager šablony](../../azure-resource-manager/templates/template-syntax.md) pro vytvoření nastavení diagnostiky pro prostředek Azure. Každá ukázka obsahuje soubor šablony a soubor parametrů s ukázkovými hodnotami, které se mají poskytnout šabloně.

Chcete-li vytvořit nastavení diagnostiky, přidejte <resource namespace> do šablony prostředek typu/Providers/diagnosticSettings. Tento článek poskytuje příklady pro dva různé typy prostředků, ale stejný vzor lze použít na jiné typy prostředků. Kolekce povolených protokolů a metrik se bude lišit pro každý typ prostředku.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="diagnostic-setting-for-azure-key-vault"></a>Nastavení diagnostiky pro Trezor klíčů Azure 
Následující příklad přidá dva dotazy protokolu do trezoru klíčů Azure.

### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vaultName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "Microsoft.KeyVault/vaults/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('vaultName'), '/Microsoft.Insights/Send to all destinations')]",
          "dependsOn": [
            
          ],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "AuditEvent",
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
}
```

### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vaultName": {
        "value": "MyVault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "MyKeyVault"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-sql-database"></a>Nastavení diagnostiky pro Azure SQL Database
Následující příklad přidá dva dotazy protokolu do databáze SQL Azure.
### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serverName": {
            "type": "String"
        },
        "dbName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "microsoft.sql/servers/databases/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('serverName'),'/',parameters('dbName'),'/microsoft.insights/SQL diagnostic setting')]",
          "dependsOn": [
            
          ],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "SQLInsights",
                "enabled": true
              },
              {
                "category": "AutomaticTuning",
                "enabled": true
              },
              {
                "category": "QueryStoreRuntimeStatistics",
                "enabled": true
              },
              {
                "category": "QueryStoreWaitStatistics",
                "enabled": true
              },
              {
                "category": "Errors",
                "enabled": true
              },
              {
                "category": "DatabaseWaitStatistics",
                "enabled": true
              },
              {
                "category": "Timeouts",
                "enabled": true
              },
              {
                "category": "Blocks",
                "enabled": true
              },
              {
                "category": "Deadlocks",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "Basic",
                "enabled": true
              },
              {
                "category": "InstanceAndAppAdvanced",
                "enabled": true
              },
              {
                "category": "WorkloadManagement",
                "enabled": true
              }
            ]
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
      "serverName": {
        "value": "MySqlServer"
      },
      "dbName": {
        "value": "MySqlDb"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "MyKeyVault"
      }
  }
}
```


## <a name="next-steps"></a>Další kroky

* [Získejte další ukázkové šablony pro Azure monitor](resource-manager-samples.md).
* [Přečtěte si další informace o nastavení diagnostiky](../platform/diagnostic-settings.md).
