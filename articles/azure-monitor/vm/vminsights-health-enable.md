---
title: Povolit stav hosta virtuálního počítače Insights (Preview)
description: Popisuje, jak ve svém předplatném povolit stav hosta služby VM Insights a jak připojit virtuální počítače.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/05/2021
ms.custom: references_regions
ms.openlocfilehash: 6f6c67e0e9ef1dd2a4092033a9479801cd0e8e7d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449303"
---
# <a name="enable-vm-insights-guest-health-preview"></a>Povolit stav hosta virtuálního počítače Insights (Preview)
Stav hosta pro virtuální počítač Insights umožňuje zobrazit stav virtuálního počítače podle definice sady měření výkonu, které jsou v pravidelných intervalech vzorkované. Tento článek popisuje, jak povolit tuto funkci ve vašem předplatném a jak povolit monitorování hostů pro každý virtuální počítač.

## <a name="current-limitations"></a>Aktuální omezení
Stav hosta virtuálních počítačů Insights má ve verzi Public Preview tato omezení:

- V tuto chvíli se podporují jenom virtuální počítače Azure. Azure Arc pro servery se v současné době nepodporuje.
- Síťové proxy servery nejsou aktuálně podporovány.


## <a name="supported-operating-systems"></a>Podporované operační systémy
Virtuální počítač musí používat jeden z následujících operačních systémů: 

  - CentOS 7,5, 7,6, 7,7, 7,8, 7,9
  - RedHat 7,5, 7,6, 7,7, 7,8, 7,9
  - Ubuntu 16,04 LTS, Ubuntu 18,04 LTS
  - Windows Server 2012 nebo novější

## <a name="supported-regions"></a>Podporované oblasti

Virtuální počítač se musí nacházet v jedné z následujících oblastí:

- Austrálie – střed
- Austrálie – východ
- Austrálie – jihovýchod
- Střední Kanada
- Indie – střed
- USA – střed
- Východní Asie
- East US
- USA – východ 2
- Východní USA 2 EUAP
- Francie – střed
- Německo – středozápad
- Japan East
- Jižní Korea – střed
- USA – středosever
- Severní Evropa
- Středojižní USA
- Jižní Afrika – sever
- Southeast Asia
- Švýcarsko – sever
- Spojené království – jih
- Spojené království – západ
- USA – středozápad
- West Europe
- USA – západ
- Západní USA 2


Log Analytics pracovní prostor se musí nacházet v jedné z následujících oblastí:

- Austrálie – střed
- Austrálie – východ
- Austrálie – jihovýchod
- Střední Kanada
- Kanada – Indie
- USA – střed
- Východní Asie
- East US
- USA – východ 2
- Východní USA 2 EUAP
- Francie – střed
- Japan East
- USA – středosever
- Severní Evropa
- Středojižní USA
- Southeast Asia
- Švýcarsko – sever
- Spojené království – jih
- Oblast Západní Evropa
- USA – západ
- Západní USA 2

## <a name="prerequisites"></a>Požadavky

- Virtuální počítač musí být připojen k virtuálnímu počítači Insights.
- Uživatel, který spouští kroky připojování, musí mít minimální přístup na úrovni přispěvatele k předplatnému, kde se nachází pravidlo pro virtuální počítače a shromažďování dat.
- Požadovaná poskytovatelé prostředků Azure musí být zaregistrovaná, jak je popsáno v následující části.

## <a name="register-required-azure-resource-providers"></a>Registrace požadovaných poskytovatelů prostředků Azure
Následující poskytovatelé prostředků Azure si zaregistrují pro vaše předplatné, aby povolili stav hosta pro virtuální počítače. 

- Microsoft. monitor zátěže byl
- Microsoft. Insights

K registraci poskytovatele prostředků můžete použít kteroukoli z dostupných metod, jak je popsáno v tématu [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md). K ověření Azure Resource Manager můžete použít také následující vzorový příkaz s použitím armclient, post nebo jiné metody:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Povolení virtuálního počítače pomocí webu Azure Portal
Když pro virtuální počítač povolíte stav hosta na webu Azure Portal, veškerá požadovaná konfigurace se provede za vás. To zahrnuje vytvoření pravidla vyžadovat shromažďování dat, instalaci rozšíření stavu hosta na virtuálním počítači a vytvoření přidružení s pravidlem shromažďování dat.

V zobrazení **Začínáme** v části VM Insights klikněte na odkaz vedle zprávy o upgradu pro virtuální počítač a pak klikněte na tlačítko **upgradovat** . Můžete také vybrat více virtuálních počítačů a upgradovat je společně.

![Povolit funkci stavu na virtuálním počítači](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Povolení virtuálního počítače pomocí šablony Resource Manageru
K povolení virtuálních počítačů pomocí Azure Resource Manager se vyžadují tři kroky.

- Vytvořit pravidlo shromažďování dat.
- Nainstalujte na každý virtuální počítač rozšíření pro stav hosta.
- Vytvoření přidružení mezi virtuálním počítačem a pravidlem shromažďování dat

### <a name="create-data-collection-rule-dcr"></a>Vytvořit pravidlo shromažďování dat (DCR)

> [!NOTE]
> Pokud povolíte virtuální počítač pomocí Azure Portal, vytvoří se pro vás pravidlo shromažďování dat popsané tady. V takovém případě tento krok nemusíte provádět.

Konfigurace monitorování ve stavu hosta virtuálních počítačů Insights je uložená v [pravidlech shromažďování dat (DCR)](../agents/data-collection-rule-overview.md). Každý virtuální počítač s rozšířením stavu hosta bude potřebovat přidružení s tímto pravidlem.

> [!NOTE]
> Můžete vytvořit další pravidla shromažďování dat pro úpravu výchozí konfigurace monitorování, jak je popsáno v tématu [Konfigurace monitorování ve stavu hosta VM Insights (Preview)](vminsights-health-configure.md).

Šablona vyžaduje hodnoty pro následující parametry:

- **defaultHealthDataCollectionRuleName**: ponechte výchozí název definovaný v šabloně.
- **destinationWorkspaceResourceId**: ID prostředku pracovního prostoru Log Analytics používaného pro shromažďování dat virtuálního počítače.
- **dataCollectionRuleLocation**: oblast pravidla shromažďování dat. Ta se musí shodovat s oblastí pracovního prostoru Log Analytics.


Nasaďte šablonu pomocí libovolné [metody nasazení pro správce prostředků šablon](../../azure-resource-manager/templates/deploy-powershell.md). Následující příkazy nasadí šablonu a soubor parametrů pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

Pravidlo shromažďování dat definované v níže uvedené šabloně Správce prostředků umožňuje všem monitorům pro virtuální počítače s rozšířením stavu hosta. Musí zahrnovat zdroje dat pro každý z čítačů výkonu používaných monitory.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>Ukázkový soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Nainstalovat rozšíření pro stav hosta a přidružit k pravidlu shromažďování dat
Pomocí následující šablony Správce prostředků povolte virtuálnímu počítači stav hosta. Tím se nainstaluje rozšíření pro stav hosta a vytvoří se přidružení s pravidlem shromažďování dat. Tuto šablonu můžete nasadit pomocí libovolné [metody nasazení pro správce prostředků šablony](../../azure-resource-manager/templates/deploy-powershell.md).


Pomocí následujících příkazů můžete například nasadit šablonu a soubor parametrů do skupiny prostředků pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>Soubor šablony

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>Ukázkový soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>Další kroky

- [Přizpůsobení monitorování povolených službou VM Insights](vminsights-health-configure.md)
