---
title: Ukázky šablon Správce prostředků pro pracovní prostory Log Analytics
description: Ukázky Azure Resource Manager šablon k nasazení Log Analytics pracovních prostorů a konfiguraci zdrojů dat v Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: 3cc79edf50c4f056f413292d2d43aa8cf8e65136
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683473"
---
# <a name="resource-manager-template-samples-for-log-analytics-workspaces-in-azure-monitor"></a>Ukázky šablon Správce prostředků pro Log Analytics pracovní prostory v Azure Monitor
Tento článek obsahuje ukázkové [Azure Resource Manager šablony](../../azure-resource-manager/templates/template-syntax.md) pro vytváření a konfiguraci Log Analytics pracovních prostorů v Azure monitor. Každá ukázka obsahuje soubor šablony a soubor parametrů s ukázkovými hodnotami, které se mají poskytnout šabloně.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>Odkazy na šablony

- [Pracovní prostory Microsoft. OperationalInsights](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces
) 
- [Pracovní prostory Microsoft. OperationalInsights/zdroje dat](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces/datasources)

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru Log Analytics
Následující ukázka vytvoří nový prázdný pracovní prostor Log Analytics.

### <a name="notes"></a>Poznámky

- Pokud zadáte cenovou úroveň **Free**, odeberte element **retentionInDays** .

### <a name="template-file"></a>Soubor šablony

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
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
          "switzerlandnorth",
          "switzerlandwest",
          "uksouth", 
          "ukwest", 
          "westcentralus", 
          "westeurope", 
          "westus", 
          "westus2" 
          ],
          "metadata": {
              "description": "Specifies the location for the workspace."
              }
        },
        "retentionInDays": {
          "type": "int",
          "defaultValue": 120,
          "metadata": {
            "description": "Number of days to retain data."
          }
        },
        "resourcePermissions": {
          "type": "bool",
          "metadata": {
            "description": "true to use resource or workspace permissions. false to require workspace permissions."
          }
      }

      },
      "resources": [
      {
          "type": "Microsoft.OperationalInsights/workspaces",
          "name": "[parameters('workspaceName')]",
          "apiVersion": "2020-08-01",
          "location": "[parameters('location')]",
          "properties": {
              "sku": {
                  "name": "[parameters('sku')]"
              },
              "retentionInDays": "[parameters('retentionInDays')]",
              "features": {
                  "searchVersion": 1,
                  "legacy": 0,
                  "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
              }
          }
      }
  ]
}
```

### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "sku": {
      "value": "pergb2018"
    },
    "location": {
      "value": "eastus"
    },
    "resourcePermissions": {
      "value": true
    }
  }
}
```

## <a name="collect-windows-events"></a>Shromáždit události systému Windows
Následující ukázka přidá kolekci [událostí Windows](../platform/data-sources-windows-events.md) do existujícího pracovního prostoru.

### <a name="notes"></a>Poznámky

- Přidejte element **DataSources** pro každý protokol událostí, který se má shromáždit. Pro každý protokol můžete zadat jinou sadu typů událostí.

### <a name="template-file"></a>Soubor šablony

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
  {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "datasources",
          "apiVersion": "2020-08-01",
          "name": "WindowsEventsSystem",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "System",
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
          "type": "datasources",
          "apiVersion": "2020-08-01",
          "name": "WindowsEventsApplication",
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
              },
              {
                "eventType": "Information"
              }
            ]
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="collect-syslog"></a>Shromáždit syslog
Následující ukázka přidá kolekci [událostí SYSLOG](../platform/data-sources-syslog.md) do existujícího pracovního prostoru.

### <a name="notes"></a>Poznámky

- Přidejte element **DataSources** pro každé zařízení, které se má shromáždit. Pro každé zařízení můžete zadat různou sadu závažnosti.

### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the workspace."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "type": "datasources",
                "apiVersion": "2020-08-01",
                "name": "SyslogKern",
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
                        },
                        {
                            "severity": "notice"
                        },
                        {
                            "severity": "info"
                        },
                        {
                            "severity": "debug"
                        }
                    ]
                }
            },
            {
                "type": "datasources",
                "apiVersion": "2020-08-01",
                "name": "SyslogDaemon",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslog",
                "properties": {
                    "syslogName": "daemon",
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
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "SyslogCollection",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslogCollection",
                "properties": {
                    "state": "Enabled"
                }
            }
        ]
      }
    ]
}

```


### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="collect-windows-performance-counters"></a>Shromažďování čítačů výkonu systému Windows
Následující ukázka přidá kolekci [čítačů výkonu Windows](../platform/data-sources-performance-counters.md) do existujícího pracovního prostoru.

### <a name="notes"></a>Poznámky

- Přidejte element **DataSources** pro každý čítač a instanci ke shromáždění. Pro každou kombinaci čítače a instance můžete zadat různou četnost shromažďování.
  
### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the workspace."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
              "description": "Location of the workspace."
            }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfMemoryAvailableBytes",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Memory",
              "instanceName": "*",
              "intervalSeconds": 10,
              "counterName": "Available MBytes "
            }
          },
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfMemoryPercentageBytes",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Memory",
              "instanceName": "*",
              "intervalSeconds": 10,
              "counterName": "% Committed Bytes in Use"
            }
          },
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfProcessorPercentage",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Processor",
              "instanceName": "_Total",
              "intervalSeconds": 10,
              "counterName": "% Processor Time"
            }
          }
        ]
      }
    ]
}

```

### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-linux-performance-counters"></a>Shromáždit čítače výkonu pro Linux
Následující ukázka přidá kolekci [čítačů výkonu systému Linux](../platform/data-sources-performance-counters.md) do existujícího pracovního prostoru.

### <a name="notes"></a>Poznámky

- Přidejte element **DataSources** pro každý objekt a instanci ke shromáždění. Pro každou kombinaci objektů a instancí můžete zadat jinou sadu čítačů, pro všechny čítače ale můžete zadat jenom jednu sazbu.
  
### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Specifies the location in which to create the workspace."
          }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "LinuxPerformanceLogicalDisk",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxPerformanceObject",
                "properties": {
                    "objectName": "Logical Disk",
                    "instanceName": "*",
                    "intervalSeconds": 10,
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
                    ]
                }
            },
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "LinuxPerformanceProcessor",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxPerformanceObject",
                "properties": {
                    "objectName": "Processor",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "performanceCounters": [
                        {
                            "counterName": "% Processor Time"
                        },
                        {
                            "counterName": "% Privileged Time"
                        }
                    ]
                }
            }
        ]
      }
    ]
}
```

### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-custom-logs"></a>Shromažďování vlastních protokolů
Následující ukázka přidá kolekci [vlastních protokolů](../platform/data-sources-custom-logs.md) do existujícího pracovního prostoru.

### <a name="notes"></a>Poznámky

- Konfigurace oddělovačů a extrakcí může být složitá. Pro pomoc můžete definovat vlastní protokol pomocí Azure Portal a načíst jeho konfiguraci pomocí [Get-AzOperationalInsightsDataSource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.8.0) s **-Kind** nastaveného na **CustomLog**.

  
### <a name="template-file"></a>Soubor šablony

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
          }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Specifies the location in which to create the workspace."
        }
      }
  },
  "resources": [
  {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
            "apiVersion": "2020-08-01",
            "type": "dataSources",
            "name": "[concat(parameters('workspaceName'), 'armlog_timedelimited')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
            ],
            "kind": "CustomLog",
            "properties": {
                "customLogName": "arm_log_timedelimited",
                "description": "this is a description",
                "inputs": [
                  {
                      "location": {
                        "fileSystemLocations": {
                            "linuxFileTypeLogPaths": [ "/var/logs" ],
                            "windowsFileTypeLogPaths": ["c:\\Windows\\Logs\\*.txt"]
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
                ],
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
                ]
            }
        },
        {
          "apiVersion": "2020-08-01",
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), 'armlog_newline')]",
          "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
              "customLogName": "armlog_newline",
              "description": "this is a description",
              "inputs": [
                {
                    "location": {
                      "fileSystemLocations": {
                          "linuxFileTypeLogPaths": [ "/var/logs" ],
                          "windowsFileTypeLogPaths": ["c:\\Windows\\Logs\\*.txt"]
                      }
                    },
                    "recordDelimiter": {
                      "regexDelimiter": {
                        "pattern": "\\n",
                        "matchIndex": 0,
                        "numberdGroup": null
                      }
                    }
                }
              ],
              "extractions": [
                {
                  "extractionName": "TimeGenerated",
                  "extractionType": "DateTime",
                  "extractionProperties": {
                    "dateTimeExtraction": {
                        "regex": null,
                        "joinStringRegex": null
                    }
                  }
                }
              ]
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-iis-log"></a>Shromažďovat protokol IIS
Následující ukázka přidá kolekci [protokolů služby IIS](../platform/data-sources-iis-logs.md) do existujícího pracovního prostoru.

### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Specifies the location in which to create the workspace."
          }
        }
    },
    "resources": [
    {
        "type": "Microsoft.OperationalInsights/workspaces",
        "apiVersion": "2020-08-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "IISLog",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "IISLogs",
                "properties": {
                    "state": "OnPremiseEnabled"
                }
            }
        ]
      }
    ]
}

```

### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```



## <a name="next-steps"></a>Další kroky

* [Získejte další ukázkové šablony pro Azure monitor](resource-manager-samples.md).
* [Přečtěte si další informace o log Analyticsch pracovních prostorech](../learn/quick-create-workspace.md).
* [Přečtěte si další informace o zdrojích dat agenta](../platform/agent-data-sources.md).
