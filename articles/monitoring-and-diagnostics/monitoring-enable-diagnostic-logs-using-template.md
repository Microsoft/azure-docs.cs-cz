---
title: Automaticky povolte nastavení pro diagnostiku pomocí šablony Resource Manageru
description: Další informace o použití šablony Resource Manageru k vytvoření nastavení diagnostiky, které vám umožní datového proudu k diagnostickým protokolům do centra událostí nebo uchováváte v účtu úložiště.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/26/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 6c202afaca893609d41384ee8302b0c4c6c4a6f6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263384"
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Automaticky povolte nastavení pro diagnostiku při vytváření prostředků pomocí šablony Resource Manageru
V tomto článku jsme ukazují, jak můžete použít [šablony Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) ke konfiguraci nastavení pro diagnostiku na prostředku při jeho vytvoření. To umožňuje automatické spuštění streamování diagnostické protokoly a metriky do centra událostí, archivaci je v účtu úložiště nebo jejich odeslání k analýze protokolů při vytváření prostředku.

Metoda pro povolení diagnostických protokolů pomocí šablony Resource Manageru závisí na typu prostředku.

* **Bez výpočetní** prostředků (například skupiny zabezpečení sítě, Logic Apps automatizace), použijte [diagnostické nastavení popsané v tomto článku](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
* **Výpočetní** prostředků (WAD/LAD na základě), použijte [WAD/LAD konfigurační soubor popsané v tomto článku](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

V tomto článku jsme popisují, jak nakonfigurovat diagnostiku pomocí těchto metod.

Základní kroky jsou následující:

1. Vytvořte šablonu jako soubor JSON, který popisuje, jak vytvořit prostředek a zapněte diagnostiku.
2. [Šablonu nasadit pomocí libovolné metody nasazení](../azure-resource-manager/resource-group-template-deploy.md).

Níže nás dostanete příklad soubor JSON šablony, které potřebujete k vygenerování pro jiný výpočetní prostředí a výpočetní prostředky.

## <a name="non-compute-resource-template"></a>Bez výpočetních prostředků šablony
Pro jiné výpočetní prostředky budete muset udělat dvě věci:

1. Přidání parametrů do objektu blob parametry pro název účtu úložiště, ID události rozbočovače autorizační pravidlo nebo ID pracovního prostoru analýzy protokolů (povolení archivace diagnostických protokolů v účtu úložiště, datové proudy protokoluje události do centra událostí a odesílání protokolů k analýze protokolů).
   
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
2. V poli prostředky prostředků, pro který chcete povolit diagnostických protokolů přidat prostředek typu `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/[parameters('settingName')]",
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

Odpovídá vlastnosti objektu blob pro nastavení diagnostiky [formát popsaný v tomto článku](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Přidávání `metrics` vlastnost vám umožní také odeslat metrika prostředků tyto stejné výstupů za předpokladu, že [prostředek podporuje Azure monitorování metriky](monitoring-supported-metrics.md).

Zde je úplný příklad, který vytvoří aplikace logiky a zapne streamování centrům událostí a úložiště v účtu úložiště.

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
          "name": "Microsoft.Insights/[parameters('settingName')]",
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

## <a name="compute-resource-template"></a>Výpočetní šablony prostředků
Povolí se Diagnostika na výpočetních prostředků, například cluster virtuálního počítače nebo Service Fabric, budete muset:

1. Přidejte rozšíření Azure Diagnostics do definice prostředků virtuálního počítače.
2. Zadejte úložiště účet nebo události rozbočovače jako parametr.
3. Přidejte obsah souboru WADCfg XML do vlastnost XMLCfg správně uvozovací znaky všechny znaky XML.

> [!WARNING]
> Tento poslední krok může být složité získat správné. [Najdete v článku](../virtual-machines/extensions/diagnostics-template.md#diagnostics-configuration-variables) příklad, která rozdělí schématu konfigurace diagnostiky do proměnné, které jsou uvozené a správně naformátovaná.
> 
> 

Celý proces, včetně ukázky, je popsaný [v tomto dokumentu](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Další kroky
* [Další informace o diagnostických protokolů Azure.](monitoring-overview-of-diagnostic-logs.md)
* [Stream Azure diagnostických protokolů do centra událostí](monitoring-stream-diagnostic-logs-to-event-hubs.md)

