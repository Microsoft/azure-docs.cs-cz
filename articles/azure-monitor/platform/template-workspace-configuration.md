---
title: Šablona Azure Resource Manager pro pracovní prostor Log Analytics
description: Šablony Azure Resource Manager můžete použít k vytvoření a konfiguraci Log Analytics pracovních prostorů.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2020
ms.openlocfilehash: dbeaa58da109c5afceb03a560e69e0c8bf63ad42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81768130"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Správa pracovního prostoru Log Analytics pomocí šablon Azure Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Šablony Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) můžete použít k vytvoření a konfiguraci Log Analytics pracovních prostorů v Azure monitor. Mezi úlohy, které můžete provádět pomocí šablon, patří:

* Vytvoření pracovního prostoru včetně nastavení cenové úrovně a rezervace kapacity
* Přidání řešení
* Vytvořit uložená hledání
* Vytvoření skupiny počítačů
* Povolit shromažďování protokolů IIS z počítačů s nainstalovaným agentem Windows
* Shromažďování čítačů výkonu z počítačů se systémy Linux a Windows
* Shromažďování událostí z protokolu syslog v počítačích se systémem Linux 
* Shromažďovat události z protokolů událostí systému Windows
* Shromažďování vlastních protokolů z počítače se systémem Windows
* Přidání agenta Log Analytics do virtuálního počítače Azure
* Konfigurace Log Analytics na indexovaná data shromážděná pomocí diagnostiky Azure

Tento článek poskytuje ukázky šablon, které ilustrují některé konfigurace, které můžete s šablonami provádět.

## <a name="api-versions"></a>Verze rozhraní API

Následující tabulka uvádí verzi rozhraní API pro prostředky použité v tomto příkladu.

| Prostředek | Typ prostředku | Verze rozhraní API |
|:---|:---|:---|
| Pracovní prostor   | pracovní prostory    | 2017-03-15 – Preview |
| Search      | savedSearches | 2015-03-20 |
| Zdroj dat | zdroje dat   | 2015-11-01 – Preview |
| Řešení    | Řešení     | 2015-11-01 – Preview |

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics

Následující příklad vytvoří pracovní prostor pomocí šablony z místního počítače. Šablona JSON je nakonfigurována tak, aby vyžadovala pouze název a umístění nového pracovního prostoru. Používá hodnoty zadané pro jiné parametry pracovního prostoru, jako je [režim řízení přístupu](design-logs-deployment.md#access-control-mode), cenová úroveň, uchování a úroveň rezervace kapacity.

> [!WARNING]
> Následující šablona vytvoří pracovní prostor Log Analytics a nakonfiguruje shromažďování dat. To může změnit nastavení fakturace. Přečtěte si téma [Správa využití a nákladů pomocí protokolů Azure monitor](manage-cost-storage.md) , abyste porozuměli fakturaci na data shromážděná v pracovním prostoru Log Analytics a teprve potom je použijete v prostředí Azure.

Pro rezervaci kapacity definujte vybranou rezervaci kapacity pro ingestování dat zadáním SKU `CapacityReservation` a hodnoty v GB pro vlastnost. `capacityReservationLevel` Následující seznam podrobně popisuje podporované hodnoty a chování při jejich konfiguraci.

- Po nastavení limitu rezervace se nemůžete změnit na jinou skladovou jednotku během 31 dnů.

- Jakmile nastavíte hodnotu rezervace, můžete ji zvětšit pouze do 31 dnů.

- Hodnotu lze nastavit pouze `capacityReservationLevel` v násobcích hodnot 100 s maximální hodnotou 50000.

- Pokud zvýšíte úroveň rezervace, časovač se resetuje a dokud ho nebudete moct změnit na další 31 dní z této aktualizace.  

- Pokud upravíte jakoukoli jinou vlastnost pracovního prostoru, ale zachováte limit rezervace na stejnou úroveň, časovač se neresetuje. 

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
   >Pro nastavení rezervace kapacity použijte tyto vlastnosti v části SKU:
   >* "Name": "CapacityReservation",
   >* "capacityReservationLevel": 100

2. Upravte šablonu tak, aby splňovala vaše požadavky. Místo předání parametrů jako vložené hodnoty zvažte vytvoření [souboru parametrů správce prostředků](../../azure-resource-manager/templates/parameter-files.md) . Informace o podporovaných vlastnostech a hodnotách najdete v referenčních informacích k [šabloně Microsoft. OperationalInsights/Workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) . 

3. Uložte tento soubor jako **deploylaworkspacetemplate. JSON** do místní složky.

4. Jste připraveni k nasazení této šablony. K vytvoření pracovního prostoru pomocí PowerShellu nebo příkazového řádku zadejte název pracovního prostoru a umístění jako součást příkazu. Název pracovního prostoru musí být globálně jedinečný v rámci všech předplatných Azure.

   * Pro PowerShell použijte následující příkazy ze složky, která obsahuje tuto šablonu:
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * Pro příkazový řádek použijte následující příkazy ze složky, která obsahuje tuto šablonu:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která obsahuje výsledek:<br><br> ![Příklad výsledku po dokončení nasazení](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Konfigurace pracovního prostoru Log Analytics

Následující ukázka šablony ukazuje, jak:

1. Přidání řešení do pracovního prostoru
2. Vytváření uložených hledání. Aby se zajistilo, že nasazení nepřepisují uložené výsledky hledání omylem, je třeba přidat do prostředku "savedSearches" vlastnost eTag pro přepsání a udržování idempotence uložených hledání.
3. Vytvoří uloženou funkci. Značka eTag by měla být přidána k přepsání funkce a udržování idempotence.
4. Vytvoření skupiny počítačů
5. Povolit shromažďování protokolů IIS z počítačů s nainstalovaným agentem Windows
6. Shromáždit čítače výkonu logických disků z počítačů se systémem Linux (% využitých uzlů inode; Volné megabajty; % Využitého místa; Přenosy disku/s; Čtení z disku/s; Zápisy na disk/s)
7. Shromažďovat události syslogu z počítačů se systémem Linux
8. Shromažďování událostí chyb a upozornění z protokolu událostí aplikace z počítačů se systémem Windows
9. Shromažďovat čítač výkonu MB dostupné paměti z počítačů s Windows
10. Shromažďování protokolů IIS a protokolů událostí Windows zapsaných diagnostikou Azure do účtu úložiště
11. Shromažďování vlastních protokolů z počítače se systémem Windows

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
        "description": "If set to true, changing retention to 30 days will immediately delete older data. Use this with extreme caution. This only applies when retention is being set to 30 days."
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
            "eTag": "*",
            "category": "VMSS",
            "displayName": "VMSS Instance Count",
            "query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "version": 1
          }
        },
        {
          "apiVersion": "2017-04-26-preview",
          "name": "Cross workspace function",
          "type": "savedSearches",
            "dependsOn": [
             "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "failedLogOnEvents",
              "category": "Security",
              "FunctionAlias": "failedlogonsecurityevents",
              "query": "
                union withsource=SourceWorkspace
                workspace('workspace1').SecurityEvent,
                workspace('workspace2').SecurityEvent,
                workspace('workspace3').SecurityEvent,
                | where EventID == 4625"
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

Postup nasazení ukázkové šablony:

1. Uložte si připojenou ukázku do souboru, například`azuredeploy.json` 
2. Upravte šablonu na požadovanou konfiguraci.
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

## <a name="example-resource-manager-templates"></a>Příklady šablon Správce prostředků

Galerie šablon pro rychlý Start Azure obsahuje několik šablon pro Log Analytics, včetně:

* [Nasazení virtuálního počítače s Windows s rozšířením virtuálního počítače s Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Nasazení virtuálního počítače se systémem Linux s rozšířením virtuálního počítače s Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Monitorování Azure Site Recovery pomocí stávajícího pracovního prostoru Log Analytics](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Monitorování Web Apps Azure pomocí existujícího pracovního prostoru Log Analytics](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Přidat existující účet úložiště do Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Další kroky

* [Nasaďte agenta Windows do virtuálních počítačů Azure pomocí šablony Správce prostředků](../../virtual-machines/extensions/oms-windows.md).

* [Nasaďte agenta pro Linux do virtuálních počítačů Azure pomocí šablony Správce prostředků](../../virtual-machines/extensions/oms-linux.md).
