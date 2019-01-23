---
title: Odeslání metrik hostovaného operačního systému do Azure monitoru metriky ukládat pomocí šablony Resource Manageru pro virtuální počítače s Windows
description: Odeslání metrik operačního systému hosta metriky Azure Monitor ukládat pomocí šablony Resource Manageru pro virtuální počítače s Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 49c37c5dd2910cda234c479dc624f6c6ae92f6c1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467913"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Odeslání metrik hostovaného operačního systému do Azure monitoru metriky ukládat pomocí šablony Resource Manageru pro virtuální počítače s Windows

Pomocí Azure monitoru [diagnostické rozšíření](diagnostics-extension-overview.md), můžete shromažďovat metriky a protokoly z hostovaný operační systém (Guest OS), na kterém běží v rámci virtuálního počítače, cloudové služby nebo clusteru Service Fabric. Rozšíření mohla odesílat telemetrii na [různými umístěními.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Tento článek popisuje postup pro odesílání metriky výkonu hostovaného operačního systému pro virtuální počítače s Windows do úložiště dat Azure Monitor. Spouští se s diagnostikou verze 1.11, můžete napsat metriky přímo do Azure monitoru ukládat metriky, kde již shromažďuje metriky na standard platformy.

Ukládání v tomto umístění umožňuje přístup ke stejné akce pro platformu metriky. Akce zahrnují téměř v reálném čase výstrahy, grafů, směrování a přístup k rozhraní REST API a další. Diagnostické rozšíření v minulosti zapsala do služby Azure Storage, ale ne k úložišti dat monitorování Azure.

Pokud jste začínáte se šablonami Resource Manageru, přečtěte si o [nasazení šablon](../../azure-resource-manager/resource-group-overview.md) a jejich struktury a syntaxe.

## <a name="prerequisites"></a>Požadavky

- Předplatné musí být zaregistrovaná s [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal).

- Musíte mít buď [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) nebo [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) nainstalované.


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Nastavení jako datová jímka Azure monitoru
Rozšíření Azure Diagnostics používá funkci s názvem "dat jímky" trasy metriky a protokoly do různých umístění. Následující kroky ukazují, jak používat šablony Resource Manageru a Powershellu k nasazení virtuálního počítače s použitím nového datová jímka "Azure Monitor".

## <a name="author-resource-manager-template"></a>Šablony Resource Manageru autora
V tomto příkladu můžete použít veřejně dostupné Ukázková šablona. Výchozí šablony jsou v https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy.JSON** je předem nakonfigurované šablony Resource Manageru pro nasazení virtuálního počítače.

- **Azuredeploy.Parameters.JSON** je soubor parametrů, který obsahuje informace, jako je například jaké uživatelské jméno a heslo, které byste rádi nastavení pro váš virtuální počítač. Během nasazování šablony Resource Manageru používá parametry, které jsou nastaveny v tomto souboru.

Stáhněte a uložte místně oba soubory.

### <a name="modify-azuredeployparametersjson"></a>Modify azuredeploy.parameters.json
Otevřít *azuredeploy.parameters.json* souboru

1. Zadejte hodnoty pro **adminUsername** a **adminPassword** pro virtuální počítač. Tyto parametry se používají pro vzdálený přístup k virtuálnímu počítači. Abyste nemuseli váš virtuální počítač zachycena, nepoužívejte hodnoty v této šabloně. Roboti prohledávání Internetu pro uživatelská jména a hesla ve veřejných úložištích GitHub. Jsou pravděpodobně testovat virtuální počítače s tyto výchozí hodnoty.

1. Vytvořte jedinečný dnsname pro virtuální počítač.

### <a name="modify-azuredeployjson"></a>Modify azuredeploy.json

Otevřít *azuredeploy.json* souboru

Přidat ID účtu úložiště do **proměnné** část šablony po vstupu pro **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Přidat toto rozšíření Identity spravované služby (MSI) do šablony v horní části **prostředky** oddílu. Rozšíření se zajistí, že přijímá monitorování Azure, která jsou probíhá emitovány.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "WADExtensionSetup",
        "apiVersion": "2015-05-01-preview",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Přidat **identity** konfigurace k prostředku virtuálního počítače zajistit, že Azure přiřadí rozšíření MSI systému identit. Tento krok zajistí, že virtuální počítač může vysílat metrik hosta o samotné do Azure monitoru.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Přidejte následující konfiguraci chcete povolit rozšíření diagnostiky na virtuálním počítači s Windows. Pro jednoduché založené na správci prostředků virtuálního počítače můžeme přidat konfiguraci rozšíření k poli prostředky pro virtuální počítač. Řádek "jímky"&mdash; "AzMonSink" a odpovídající "SinksConfig" dále v části&mdash;povolit rozšíření generovat metriky přímo do Azure monitoru. Můžete přidat nebo odebrat čítače výkonu, podle potřeby.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "extensions",
            "name": "Microsoft.Insights.VMDiagnosticsSettings",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Uložte a zavřete oba soubory.


## <a name="deploy-the-resource-manager-template"></a>Nasazení šablony Resource Manageru

> [!NOTE]
> Musí být spuštěna verze rozšíření diagnostiky Azure 1.5 nebo novější a mají **autoUpgradeMinorVersion**: vlastnost na hodnotu "true" v šabloně Resource Manageru. Azure pak načte správné rozšíření při spuštění virtuálního počítače. Pokud nemáte k dispozici tato nastavení v šabloně, je změnit a znovu nasaďte šablonu.


K nasazení šablony Resource Manageru, můžeme využít prostředí Azure PowerShell.

1. Spusťte PowerShell.
1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`.
1. Získat seznam vašich předplatných s použitím `Get-AzureRmSubscription`.
1. Nastavte předplatné, které používáte k vytvoření nebo aktualizaci virtuálního počítače:

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Chcete-li vytvořit novou skupinu prostředků pro virtuální počítač, který se nasazuje, spusťte následující příkaz:

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Nezapomeňte [použijte oblast Azure, který je povolený pro vlastní metriky](metrics-custom-overview.md).

1. Spuštěním následujících příkazů nasaďte virtuální počítač pomocí šablony Resource Manageru.
   > [!NOTE]
   > Pokud chcete aktualizovat existující virtuální počítač, stačí přidat *-režim přírůstkového* na konec příkazu.

   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Po úspěšném nasazení virtuální počítač by měl být na webu Azure Portal, generování metrik do Azure monitoru.

   > [!NOTE]
   > Můžete narazit na chyby kolem vybrané vmSkuSize. Pokud k tomu dojde, vraťte se do souboru azuredeploy.json a aktualizujte výchozí hodnotu parametru vmSkuSize. V takovém případě doporučujeme vyzkoušet "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Graf metrik

1. Přihlaste se k webu Azure Portal.

2. V nabídce vlevo vyberte **monitorování**.

3. Na stránce monitorování vyberte **metriky**.

   ![Stránka metriky](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Na období agregace změnit **posledních 30 minut**.

5. V rozevírací nabídce prostředků vyberte virtuální počítač, který jste vytvořili. Pokud jste nezměnili název v šabloně, měla by být *SimpleWinVM2*.

6. V rozevírací nabídce obory názvů, vyberte **azure.vm.windows.guest**

7. V rozevírací nabídce metrik vyberte **paměti\%používané svěřené bajty použití**.


## <a name="next-steps"></a>Další postup
- Další informace o [vlastní metriky](metrics-custom-overview.md).

