---
title: Ukázky šablon Správce prostředků pro pravidla shromažďování dat
description: Ukázka Azure Resource Manager šablon pro vytváření přidružení mezi pravidly shromažďování dat a virtuálními počítači v Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 12e6aa85f7d8c2c4aebe80efc92f771917e6a1d5
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746008"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Ukázky šablon Správce prostředků pro pravidla shromažďování dat v Azure Monitor
Tento článek obsahuje ukázkové [Azure Resource Manager šablony](../../azure-resource-manager/templates/template-syntax.md) pro nasazení a konfiguraci [agenta Log Analytics](../platform/log-analytics-agent.md) a [rozšíření diagnostiky](../platform/diagnostics-extension-overview.md) pro virtuální počítače v Azure monitor. Každá ukázka obsahuje soubor šablony a soubor parametrů s ukázkovými hodnotami, které se mají poskytnout šabloně.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Vytvoření asociace s virtuálním počítačem Azure

Následující ukázka nainstaluje agenta Azure Monitor na virtuální počítač Windows Azure. Vytvoří se přidružení mezi virtuálním počítačem Azure a pravidlem shromažďování dat.

### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
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
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-ith-azure-arc"></a>Vytvoření asociace-tá – Azure ARC

Následující ukázka nainstaluje agenta Azure Monitor na virtuální počítač Windows Azure. Vytvoří se přidružení mezi počítačem serveru s podporou ARC Azure a pravidlem shromažďování dat.

### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
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
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>Další kroky

* [Získejte další ukázkové šablony pro Azure monitor](resource-manager-samples.md).
* [Přečtěte si další informace o agentovi Log Analytics](../platform/log-analytics-agent.md).
* [Přečtěte si další informace o diagnostickém rozšíření](../platform/diagnostics-extension-overview.md).
