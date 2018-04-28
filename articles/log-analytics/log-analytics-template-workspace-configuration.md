---
title: Pomocí šablony Azure Resource Manager vytvořit a nakonfigurovat pracovní prostor Log Analytics | Microsoft Docs
description: Šablony Azure Resource Manager můžete použít k vytvoření a konfigurace analýzy protokolů pracovních prostorů.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: d21ca1b0-847d-4716-bb30-2a8c02a606aa
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: json
ms.topic: article
ms.date: 04/25/2018
ms.author: richrund
ms.openlocfilehash: 297f15430c64e5de3c10e6f38855664a50d11a8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="manage-log-analytics-using-azure-resource-manager-templates"></a>Spravovat pomocí šablony Azure Resource Manager analýzy protokolů
Můžete použít [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) vytvořit a nakonfigurovat pracovní prostory analýzy protokolů. Mezi příklady úloh, které můžete provádět s šablonami patří:

* Vytvořit pracovní prostor včetně nastavení cenové úrovně 
* Přidání řešení
* Vytvoření uložených hledání
* Vytvořit skupinu počítačů
* Povolit shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem systému Windows
* Shromáždit čítače výkonu z počítačů se systémy Linux a Windows
* Shromažďování událostí z syslog počítačů se systémem Linux 
* Shromažďování událostí z protokolů událostí systému Windows
* Přidat agenta analýzy protokolů pro virtuální počítač Azure
* Konfigurace analýzy protokolů pro data indexu shromažďována pomocí Azure diagnostics

Tento článek obsahuje šablony vzorků, které ilustrovat některé z konfigurací, která je možné provádět pomocí šablony.

## <a name="create-a-log-analytics-workspace"></a>Vytvořit pracovní prostor analýzy protokolů
Následující příklad vytvoří pracovního prostoru pomocí šablony ze svého místního počítače. Šablona JSON je nakonfigurován pouze s výzvou k zadání názvu pracovního prostoru a určí výchozí hodnotu pro parametry, které se pravděpodobně použije jako standardní konfigurace ve vašem prostředí.  

Tyto parametry nastavit výchozí hodnotu:

* Umístění – výchozí hodnota je východní USA
* SKU – výchozí nastavení na nové za GB cenové úrovně vydané v dubnu 2018 cenový model

>[!WARNING]
>Pokud vytváříte nebo konfigurace pracovní prostor analýzy protokolů v odběru, který má vyjádřit výslovný souhlas do nové duben 2018 cenový model, pouze platné analýzy protokolů cenová úroveň je **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Vytvoření a nasazení šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          },
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```
2. Upravte šablonu, aby vyhovovalo vašim požadavkům.  Zkontrolujte [Microsoft.OperationalInsights/workspaces šablony](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) odkaz na další, jaké vlastnosti a hodnoty jsou podporovány. 
3. Uložte tento soubor jako **deploylaworkspacetemplate.json** do místní složky.
4. Jste připraveni k nasazení této šablony. Můžete použít PowerShell nebo příkazový řádek pro cretae pracovním prostoru.

   * Pro prostředí PowerShell použijte následující příkazy ze složky se šablonou:
   
        ```powershell
        New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
        ```

   * Pro příkazový řádek použijte následující příkazy ze složky se šablonou:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json
        ```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která zahrnuje výsledek:<br><br> ![Příklad výsledků po dokončení nasazení](./media/log-analytics-template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Konfigurovat pracovní prostor analýzy protokolů
Znázorňuje následující ukázka šablony postup:

1. Přidat řešení do pracovního prostoru
2. Vytvoření uložených hledání
3. Vytvořit skupinu počítačů
4. Povolit shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem systému Windows
5. Z počítače se systémem Linux shromáždit čítače výkonu logický Disk (% použitých uzlů; Volné megabajty; % Využitého místa; Přenosy disku/s; Čtení disku/s; Zápis disku/s)
6. Shromažďovat události procesu syslog z počítače se systémem Linux
7. Shromažďování událostí chyb a upozornění z protokolu událostí aplikace z počítače se systémem Windows
8. Shromažďovat čítač výkonu paměť v MB k dispozici z počítače se systémem Windows
9. Shromažďovat protokoly služby IIS a protokoly událostí systému Windows zapsaných správcem Azure diagnostiky do účtu úložiště

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
        "PerNode",
        "PerGB2018"
      ],
      "metadata": {
        "description": "Service Tier: Free, Standalone, PerNode, or PerGB2018"
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
            "Query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
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
* [Přidat existující účet úložiště do OMS](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Další postup
* [Nasazení agenta systému Windows k virtuálním počítačům Azure pomocí šablony Resource Manageru](../virtual-machines/windows/extensions-oms.md).
* [Nasazení agenta systému Linux k virtuálním počítačům Azure pomocí šablony Resource Manageru](../virtual-machines/linux/extensions-oms.md).

