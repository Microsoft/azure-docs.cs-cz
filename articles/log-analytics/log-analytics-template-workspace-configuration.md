---
title: "Pomocí šablony Azure Resource Manager vytvořit a nakonfigurovat pracovní prostor Log Analytics | Microsoft Docs"
description: "Šablony Azure Resource Manager můžete použít k vytvoření a konfigurace analýzy protokolů pracovních prostorů."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: d21ca1b0-847d-4716-bb30-2a8c02a606aa
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: json
ms.topic: article
ms.date: 03/05/2018
ms.author: richrund
ms.openlocfilehash: db9b941e84c018a3a56dd683c118e47ee808259d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="manage-log-analytics-using-azure-resource-manager-templates"></a>Spravovat pomocí šablony Azure Resource Manager analýzy protokolů
Můžete použít [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) vytvořit a nakonfigurovat pracovní prostory analýzy protokolů. Mezi příklady úloh, které můžete provádět s šablonami patří:

* Vytvoření pracovního prostoru
* Přidat řešení
* Vytvoření uložených hledání
* Vytvořit skupinu počítačů
* Povolit shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem systému Windows
* Shromáždit čítače výkonu z počítačů se systémy Linux a Windows
* Shromažďování událostí z syslog počítačů se systémem Linux 
* Shromažďování událostí z protokolů událostí systému Windows
* Přidat agenta analýzy protokolů pro virtuální počítač Azure
* Konfigurace analýzy protokolů pro data indexu shromažďována pomocí Azure diagnostics

Tento článek obsahuje šablonu vzorků, které ilustrovat některé konfigurace, kterou můžete provést z šablony.

## <a name="api-versions"></a>Verze rozhraní API
V příkladu v tomto článku je pro [upgradovat pracovní prostor analýzy protokolů](log-analytics-log-search-upgrade.md).  Pokud chcete použít starší verze pracovní prostor, musíte změnit syntaxe dotazů na starší verze jazyka a změnit verzi rozhraní API pro každý zdroj.  Následující tabulka uvádí verze rozhraní API pro prostředky používané v tomto příkladu.

| Prostředek | Typ prostředku | Starší verze rozhraní API | Upgradovaná verze rozhraní API |
|:---|:---|:---|:---|
| Pracovní prostor   | Pracovní prostory    | 2015-11-01-preview | 2017-03-15-preview |
| Search      | savedSearches | 2015-11-01-preview | 2017-03-15-preview |
| Zdroj dat | zdroje dat   | 2015-11-01-preview | 2015-11-01-preview |
| Řešení    | Řešení     | 2015-11-01-preview | 2015-11-01-preview |


## <a name="create-and-configure-a-log-analytics-workspace"></a>Vytvořit a nakonfigurovat pracovní prostor Log Analytics
Znázorňuje následující ukázka šablony postup:

1. Vytvořit pracovní prostor, včetně nastavení uchovávání dat
2. Přidat řešení do pracovního prostoru
3. Vytvoření uložených hledání
4. Vytvořit skupinu počítačů
5. Povolit shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem systému Windows
6. Z počítače se systémem Linux shromáždit čítače výkonu logický Disk (% použitých uzlů; Volné megabajty; % Využitého místa; Přenosy disku/s; Čtení disku/s; Zápis disku/s)
7. Shromažďovat události procesu syslog z počítače se systémem Linux
8. Shromažďování událostí chyb a upozornění z protokolu událostí aplikace z počítače se systémem Windows
9. Shromažďovat čítač výkonu paměť v MB k dispozici z počítače se systémem Windows
11. Shromažďovat protokoly služby IIS a protokoly událostí systému Windows zapsaných správcem Azure diagnostiky do účtu úložiště

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "workspaceName"
      }
    },
    "serviceTier": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Standalone",
        "PerNode"
      ],
      "metadata": {
        "description": "Service Tier: Free, Standalone, or PerNode"
    }
      },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Free plans can only have 7 days, Standalone and OMS plans include 30 days for free"
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "East US",
        "West Europe",
        "Southeast Asia",
        "Australia Southeast"
      ]
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    }
  },
  "variables": {
    "Updates": {
      "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "Updates"
    },
    "AntiMalware": {
      "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "AntiMalware"
    },
    "SQLAssessment": {
      "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "SQLAssessment"
    },
    "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2017-03-15-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "Name": "[parameters('serviceTier')]"
        },
    "retentionInDays": "[parameters('dataRetention')]"
      },
      "resources": [
        {
          "apiVersion": "2017-03-15-preview",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "Category": "VMSS",
            "ETag": "*",
            "DisplayName": "VMSS Instance Count",
            "Query": "Event | where Source == "ServiceFabricNodeBootstrapAgent" | summarize AggregatedValue = count() by Computer",
            "Version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "pricingTier": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}

```
### <a name="deploying-the-sample-template"></a>Nasazení ukázkové šablony
Pokud chcete nasadit šablonu ukázka:

1. Například uložit do souboru připojené vzorku `azuredeploy.json` 
2. Upravit šablonu, kterou chcete mít požadovaná konfigurace
3. Nasazení šablony pomocí Powershellu nebo příkazového řádku

#### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Příkazový řádek
```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```


## <a name="example-resource-manager-templates"></a>Příklad Resource Manager šablony
Galerie pro šablonu Azure rychlý start zahrnuje několik šablon pro analýzy protokolů, včetně:

* [Nasazení virtuálního počítače se systémem Windows s rozšířením VM analýzy protokolů](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Nasazení virtuálního počítače s Linuxem pomocí rozšíření virtuálního počítače analýzy protokolů](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Monitorování pomocí existujícímu pracovnímu prostoru analýzy protokolů Azure Site Recovery.](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Monitorování pomocí existujícímu pracovnímu prostoru analýzy protokolů Azure Web Apps](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Monitorování pomocí existujícímu pracovnímu prostoru analýzy protokolů Azure SQL](https://azure.microsoft.com/documentation/templates/101-sqlazure-oms-monitoring/)
* [Nasazení clusteru Service Fabric a monitorování s existující pracovní prostor analýzy protokolů](https://azure.microsoft.com/documentation/templates/service-fabric-oms/)
* [Nasazení clusteru Service Fabric a vytvořit pracovní prostor analýzy protokolů ho chcete sledovat](https://azure.microsoft.com/documentation/templates/service-fabric-vmss-oms/)

## <a name="next-steps"></a>Další postup
* [Nasazení agentů do virtuálních počítačů Azure pomocí šablony Resource Manageru](log-analytics-azure-vm-extension.md)

