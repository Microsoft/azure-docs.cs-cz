---
title: Automaticky povolení diagnostických nastavení pomocí šablony Resource Manageru
description: Zjistěte, jak pomocí šablony Resource Manageru k vytvoření nastavení diagnostiky, které vám umožní streamování diagnostických protokolů do služby Event Hubs a uložit je do účtu úložiště.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/26/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 1d199275d6c17dff72c020bd3a9807f50af8af1e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53544035"
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Automaticky povolení diagnostických nastavení při vytváření prostředků pomocí šablony Resource Manageru
V tomto článku vám ukážeme, jak můžete použít [šablony Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md) ke konfiguraci nastavení diagnostiky pro prostředek při jeho vytvoření. To umožňuje automaticky spustit streamování diagnostických protokolů a metrik do služby Event Hubs, archivovat v účtu úložiště nebo odeslání do Log Analytics, když se vytvoří prostředek.

> [!WARNING]
> 1. listopadu 2018 se formát dat protokolů v účtu úložiště změní na řádky JSON. [Informace o dopadu a postup pro aktualizaci nástrojů, aby si s novým formátem poradily, najdete v tomto článku](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md). 
>
> 

Metoda pro povolení diagnostických protokolů pomocí šablony Resource Manageru závisí na typu prostředku.

* **Non-Compute** používají prostředky (například skupiny zabezpečení sítě, Logic Apps, automatizace) [nastavení diagnostiky, které jsou popsané v tomto článku](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
* **COMPUTE** používají prostředky (WAD/LAD min.) [WAD/LAD konfigurační soubor je popsáno v tomto článku](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

V tomto článku zjistíte, jak nakonfigurovat diagnostiku pomocí některé z metod.

Základní kroky jsou následující:

1. Vytvoření šablony jako soubor JSON, který popisuje, jak vytvořit prostředek a povolit diagnostiku.
2. [Nasazení šablony pomocí libovolné metody nasazení](../../azure-resource-manager/resource-group-template-deploy.md).

Dále poskytujeme příklad souboru JSON šablony, které je potřeba vygenerovat pro rozsáhlých výpočetních prostředků a výpočetní prostředky.

## <a name="non-compute-resource-template"></a>Non-výpočetního prostředku šablony
Pro prostředky rozsáhlých výpočetních prostředků budete muset udělat dvě věci:

1. Přidání parametrů do objektu blob parametry pro název účtu úložiště, ID pravidla autorizace centra událostí nebo ID pracovního prostoru Log Analytics (povolení archivace diagnostických protokolů v účtu úložiště, streamování protokolů do služby Event Hubs nebo odesílání protokolů do Log Analytics).
   
    ```json
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
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
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. V poli prostředky prostředku, pro kterou chcete povolit diagnostické protokoly přidání prostředku typu `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
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
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ],
          "metrics": [
            {
              "category": "AllMetrics",
              "enabled": true,
              "retentionPolicy": {
                "enabled": false,
                "days": 0
              }
            }
          ]
        }
      }
    ]
    ```

Vlastnosti objektu blob pro nastavení diagnostiky následuje [formátu popsaném v tomto článku](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Přidávání `metrics` vlastnost vám umožní také odeslat metriky prostředků tyto stejné výstupy, za předpokladu, že [prostředek podporuje metrik Azure monitoru](../../azure-monitor/platform/metrics-supported.md).

Tady je úplný příklad, který vytváří aplikaci logiky a zapne streamování do Event Hubs a úložiště v účtu úložiště.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "http://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
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
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
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
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Výpočetních prostředků šablony
Povolit diagnostiku na výpočetní prostředek, třeba clusteru virtuálního počítače nebo Service Fabric, budete muset:

1. Přidání rozšíření Azure Diagnostics do definice prostředků virtuálního počítače.
2. Jako parametr uvést úložiště účtu a/nebo event hub.
3. Přidáte obsah souboru WADCfg XML do vlastnost XMLCfg správně uvození všechny znaky XML.

> [!WARNING]
> Tento poslední krok může být velmi obtížné zjistit správný. [Najdete v článku](../../virtual-machines/extensions/diagnostics-template.md#diagnostics-configuration-variables) příklad, který rozdělí schéma konfigurace diagnostiky do proměnné, které jsou uvozeny řídicími znaky a správně naformátované.
> 
> 

Celý proces, včetně ukázek, je popsaná [v tomto dokumentu](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Další postup
* [Další informace o diagnostických protokolech Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Stream diagnostické protokoly Azure do služby Event Hubs](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)

