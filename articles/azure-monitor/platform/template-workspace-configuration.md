---
title: Šablona Azure Resource Manager pro pracovní prostor Log Analytics
description: Šablony Azure Resource Manageru můžete použít k vytvoření a konfiguraci pracovních prostorů Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2020
ms.openlocfilehash: 81e46f53c0afc69c927918daa0488c4835d60805
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605023"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Správa pracovního prostoru Log Analytics pomocí šablon Azure Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Šablony Azure Resource Manageru](../../azure-resource-manager/templates/template-syntax.md) můžete použít k vytvoření a konfiguraci pracovních prostorů Log Analytics v Azure Monitoru. Příklady úkolů, které můžete provádět se šablonami:

* Vytvoření pracovního prostoru včetně nastavení cenové úrovně a rezervace kapacity
* Přidání řešení
* Vytvoření uložených hledání
* Vytvoření skupiny počítačů
* Povolení shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem systému Windows
* Sběr čítačů výkonu z počítačů S Linuxem a Windows
* Shromažďování událostí ze syslogu na počítačích s Linuxem 
* Shromažďování událostí z protokolů událostí systému Windows
* Shromažďování vlastních protokolů z počítače se systémem Windows
* Přidání agenta analýzy protokolů do virtuálního počítače Azure
* Konfigurace analýzy protokolů pro indexování dat shromážděných pomocí diagnostiky Azure

Tento článek obsahuje ukázky šablon, které ilustrují některé konfigurace, které lze provést se šablonami.

## <a name="api-versions"></a>Verze rozhraní API

V následující tabulce je uvedena verze rozhraní API pro prostředky použité v tomto příkladu.

| Prostředek | Typ prostředku | Verze rozhraní API |
|:---|:---|:---|
| Pracovní prostor   | pracovní prostory    | 2017-03-15-náhled |
| Search      | uloženoVyhledávání | 2015-03-20 |
| Zdroj dat | Datasources   | 2015-11-01-náhled |
| Řešení    | Řešení     | 2015-11-01-náhled |

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics

Následující příklad vytvoří pracovní prostor pomocí šablony z místního počítače. Šablona JSON je nakonfigurována tak, aby vyžadovala pouze název a umístění nového pracovního prostoru. Používá hodnoty zadané pro jiné parametry pracovního prostoru, jako je [například režim řízení přístupu](design-logs-deployment.md#access-control-mode), cenová úroveň, uchovávání informací a úroveň rezervace kapacity.

> [!WARNING]
> Následující šablona vytvoří pracovní prostor Analýzy protokolů a konfiguruje shromažďování dat. To může změnit nastavení fakturace. Zkontrolujte [Správa využití a nákladů pomocí protokolů monitorování Azure,](manage-cost-storage.md) abyste porozuměli fakturaci dat shromážděných v pracovním prostoru Log Analytics před jejich použitím ve vašem prostředí Azure.

Pro rezervaci kapacity definujete vybranou rezervaci kapacity pro `CapacityReservation` ingestování dat zadáním skladové položky a hodnoty v GB pro vlastnost `capacityReservationLevel`. Následující seznam podrobně popisuje podporované hodnoty a chování při konfiguraci.

- Jakmile nastavíte limit rezervace, nemůžete změnit na jinou skladovou položku do 31 dnů.

- Jakmile nastavíte hodnotu rezervace, můžete ji zvýšit pouze do 31 dnů.

- Hodnotu lze nastavit `capacityReservationLevel` pouze v násobcích 100 s maximální hodnotou 50000.

- Pokud zvýšíte úroveň rezervace, časovač se resetuje a od této aktualizace jej nelze změnit dalších 31 dní.  

- Pokud změníte jakoukoli jinou vlastnost pracovního prostoru, ale zachováte limit rezervace na stejnou úroveň, časovač se neresetuje. 

### <a name="create-and-deploy-template"></a>Vytvoření šablony nasazení

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
      "sku": {
        "type": "string",
        "allowedValues": [
          "pergb2018",
          "Free",
          "Standalone",
          "PerNode",
          "Standard",
          "Premium"
          ],
        "defaultValue": "pergb2018",
        "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
        }
      },
      "location": {
        "type": "String",
        "allowedValues": [
        "australiacentral", 
        "australiaeast", 
        "australiasoutheast", 
        "brazilsouth",
        "canadacentral", 
        "centralindia", 
        "centralus", 
        "eastasia", 
        "eastus", 
        "eastus2", 
        "francecentral", 
        "japaneast", 
        "koreacentral", 
        "northcentralus", 
        "northeurope", 
        "southafricanorth", 
        "southcentralus", 
        "southeastasia", 
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2" 
        ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
        }
      }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
                },
                "retentionInDays": 120,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
          }
       ]
    }
    ```

   >[!NOTE]
   >Pro nastavení rezervace kapacity použijte tyto vlastnosti v části "sku":
   >* "jméno": "CapacityReservation",
   >* "capacityReservationLevel": 100

2. Upravte šablonu tak, aby vyhovovala vašim požadavkům. Zvažte vytvoření [souboru parametrů Správce prostředků](../../azure-resource-manager/templates/parameter-files.md) namísto předávání parametrů jako vsazených hodnot. Projděte si odkaz na [šablonu microsoft.operationalinsights/pracovní prostory,](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) abyste zjistili, jaké vlastnosti a hodnoty jsou podporovány. 

3. Uložte tento soubor jako **deploylaworkspacetemplate.json** do místní složky.

4. Jste připraveni k nasazení této šablony. K vytvoření pracovního prostoru se používá prostředí PowerShell nebo příkazový řádek a jako součást příkazu zadáte název a umístění pracovního prostoru. Název pracovního prostoru musí být globálně jedinečný ve všech předplatných Azure.

   * Pro Prostředí PowerShell použijte následující příkazy ze složky obsahující šablonu:
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * Pro příkazový řádek použijte následující příkazy ze složky obsahující šablonu:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která obsahuje výsledek:<br><br> ![Příklad výsledku po dokončení nasazení](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Konfigurace pracovního prostoru Analýzy protokolů

Následující ukázka šablony ukazuje, jak:

1. Přidání řešení do pracovního prostoru
2. Vytvořte uložená hledání. Chcete-li zajistit, aby nasazení nepřepsala uložená hledání náhodně, měla by být do prostředku "savedSearches" přidána vlastnost eTag, která by přepsala a zachovala idempotenci uložených vyhledávání.
3. Vytvoření skupiny počítačů
4. Povolení shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem systému Windows
5. Sbírat čítače perf logického disku z počítačů se systémem Linux (% použité inody; Megabajty zdarma; % využitémísto; Přenosy disků/s; Čtení disku/s; Zápisy na disk/s)
6. Shromažďování událostí syslogu z počítačů s Linuxem
7. Shromažďování událostí chyb a upozornění z protokolu událostí aplikace z počítačů se systémem Windows
8. Čítač výkonu Shromáždit dostupné paměti Mbytes z počítačů se systémem Windows
9. Shromažďování protokolů služby IIS a protokolů událostí systému Windows napsaných diagnostikou Azure do účtu úložiště
10. Shromažďování vlastních protokolů z počítače se systémem Windows

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Workspace name"
      }
    },
    "sku": {
      "type": "string",
      "allowedValues": [
        "PerGB2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "defaultValue": "pergb2018",
      "metadata": {
        "description": "Pricing tier: pergb2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
      }
    },
    "immediatePurgeDataOn30Days": {
      "type": "bool",
      "defaultValue": "[bool('false')]",
      "metadata": {
        "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral", 
        "australiaeast", 
        "australiasoutheast", 
        "brazilsouth",
        "canadacentral", 
        "centralindia", 
        "centralus", 
        "eastasia", 
        "eastus", 
        "eastus2", 
        "francecentral", 
        "japaneast", 
        "koreacentral", 
        "northcentralus", 
        "northeurope", 
        "southafricanorth", 
        "southcentralus", 
        "southeastasia", 
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
      }
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
    },
    "customLogName": {
    "type": "string",
    "metadata": {
      "description": "The custom log name"
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
        "retentionInDays": "[parameters('dataRetention')]",
        "features": {
          "immediatePurgeDataOn30Days": "[parameters('immediatePurgeDataOn30Days')]"
        },
        "sku": {
          "name": "[parameters('sku')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "category": "VMSS",
            "eTag": "*",
            "displayName": "VMSS Instance Count",
            "query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "version": 1
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
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), parameters('customLogName'))]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
            "customLogName": "[parameters('customLogName')]",
            "description": "this is a description",
            "extractions": [
              {
                "extractionName": "TimeGenerated",
                "extractionProperties": {
                  "dateTimeExtraction": {
                    "regex": [
                      {
                        "matchIndex": 0,
                        "numberdGroup": null,
                        "pattern": "((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]"
                      }
                    ]
                  }
                },
                "extractionType": "DateTime"
              }
            ],
            "inputs": [
              {
                "location": {
                  "fileSystemLocations": {
                    "linuxFileTypeLogPaths": null,
                    "windowsFileTypeLogPaths": [
                      "[concat('c:\\Windows\\Logs\\',parameters('customLogName'))]"
                    ]
                  }
                },
                "recordDelimiter": {
                  "regexDelimiter": {
                    "matchIndex": 0,
                    "numberdGroup": null,
                    "pattern": "(^.*((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9].*$)"
                  }
                }
              }
            ]
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
          "apiVersion": "2015-03-20",
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
    "sku": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "immediatePurgeDataOn30Days": {  
      "type": "bool",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').features.immediatePurgeDataOn30Days]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}
```

### <a name="deploying-the-sample-template"></a>Nasazení ukázkové šablony

Nasazení ukázkové šablony:

1. Uložení připojené ukázky do souboru, například`azuredeploy.json` 
2. Úprava šablony tak, aby měla požadovanou konfiguraci
3. Nasazení šablony pomocí PowerShellu nebo příkazového řádku

#### <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Příkazový řádek

```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Příklad šablon Správce prostředků

Galerie šablon rychlého startu Azure obsahuje několik šablon pro analýzu protokolů, včetně:

* [Nasazení virtuálního počítače se systémem Windows s rozšířením virtuálního počítače Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Nasazení virtuálního počítače se systémem Linux s rozšířením virtuálního počítače Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Monitorování Azure Site Recovery pomocí existujícího pracovního prostoru Log Analytics](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Monitorování webových aplikací Azure pomocí existujícího pracovního prostoru Log Analytics](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Přidání existujícího účtu úložiště do služby Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Další kroky

* [Nasazení agenta Windows do virtuálních počítačů Azure pomocí šablony Správce prostředků](../../virtual-machines/extensions/oms-windows.md).

* [Nasazení agenta Linuxu do virtuálních počítačů Azure pomocí šablony Správce prostředků](../../virtual-machines/extensions/oms-linux.md).
