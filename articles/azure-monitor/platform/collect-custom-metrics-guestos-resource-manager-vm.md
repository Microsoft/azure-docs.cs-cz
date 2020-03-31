---
title: Shromažďování metrik virtuálních virtuálních počítače s Windows ve službě Azure Monitor pomocí šablony
description: Odeslání metrik hostovaného operačního systému do úložiště metrik Azure Monitor pomocí šablony Správce prostředků pro virtuální počítač s Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e747ca89912c36538bfb9d02986629fe57c5adcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657363"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Odeslání metrik hostovaného operačního systému do úložiště metrik Azure Monitor pomocí šablony Správce prostředků pro virtuální počítač s Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pomocí [rozšíření Diagnostika](diagnostics-extension-overview.md)monitorování Azure můžete shromažďovat metriky a protokoly z hostovaného operačního systému (hostovaný operační systém), který běží jako součást virtuálního počítače, cloudové služby nebo clusteru Service Fabric. Rozšíření můžete odeslat telemetrie do [mnoha různých umístění.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Tento článek popisuje proces odesílání metrik výkonu hostovaného operačního systému pro virtuální počítač s Windows do úložiště dat Azure Monitor. Počínaje diagnostikou verze 1.11 můžete psát metriky přímo do úložiště metrik Azure Monitoru, kde se už shromažďují metriky standardní platformy.

Jejich umístění v tomto umístění umožňuje přístup ke stejným akcím pro metriky platformy. Akce zahrnují téměř v reálném čase upozorňování, vytváření grafů, směrování a přístup z rozhraní REST API a další. V minulosti rozšíření Diagnostika zapisoval o Azure Storage, ale ne do úložiště dat Azure Monitor.

Pokud s šablonami Správce prostředků tečte, přečtěte si informace o [nasazeních šablon](../../azure-resource-manager/management/overview.md) a jejich struktuře a syntaxi.

## <a name="prerequisites"></a>Požadavky

- Vaše předplatné musí být zaregistrováno u [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Musíte mít nainstalovaný [Buď Azure PowerShell](/powershell/azure) nebo [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview)

- Prostředek virtuálního počítače musí být v [oblasti, která podporuje vlastní metriky](metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Nastavení Azure Monitoru jako jímky dat
Rozšíření Diagnostika Azure používá funkci s názvem "jímky dat" ke směrování metriky a protokoly do různých umístění. Následující kroky ukazují, jak pomocí šablony Správce prostředků a Prostředí PowerShell nasadit virtuální počítač pomocí nového jímky dat "Azure Monitor".

## <a name="author-resource-manager-template"></a>Šablona Správce zdrojů autora
V tomto příkladu můžete použít veřejně dostupnou ukázkovou šablonu. Počáteční šablony jsou https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windowsna .

- **Azuredeploy.json** je předkonfigurovaná šablona Správce prostředků pro nasazení virtuálního počítače.

- **Azuredeploy.parameters.json** je soubor parametrů, který ukládá informace, jako je uživatelské jméno a heslo, které chcete nastavit pro váš virtuální počítač. Během nasazení používá šablona Správce prostředků parametry, které jsou nastaveny v tomto souboru.

Stáhněte a uložte oba soubory místně.

### <a name="modify-azuredeployparametersjson"></a>Úprava souboru azuredeploy.parameters.json
Otevření souboru *azuredeploy.parameters.json*

1. Zadejte hodnoty pro **adminUživatelské jméno** a **adminPassword** pro virtuální počítače. Tyto parametry se používají pro vzdálený přístup k virtuálnímu virtuálnímu serveru. Chcete-li se vyhnout zneužití virtuálního počítače, nepoužívejte hodnoty v této šabloně. Boti naskenují na internetu uživatelská jména a hesla ve veřejných úložištích GitHub. Je pravděpodobné, že testování virtuálních ms s těmito výchozími hodnotami.

1. Vytvořte jedinečný název DNS pro virtuální ho.

### <a name="modify-azuredeployjson"></a>Úprava souboru azuredeploy.json

Otevření souboru *azuredeploy.json*

Přidejte ID účtu úložiště do části **proměnných** šablony po vstupu pro **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Přidejte toto rozšíření identity spravované služby (MSI) do šablony v horní části oddílu **prostředků.** Rozšíření zajišťuje, že Azure Monitor přijímá metriky, které jsou emitovány.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
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

Přidejte **konfiguraci identity** do prostředku virtuálního počítače, abyste zajistili, že Azure přiřadí systémové identitě k rozšíření MSI. Tento krok zajišťuje, že virtuální počítač můžete vyzařovat metriky hosta o sobě azure monitoru.

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

Přidejte následující konfiguraci, která povolí rozšíření Diagnostika na virtuálním počítači se systémem Windows. Pro jednoduchý virtuální počítač založený na Správci prostředků můžeme přidat konfiguraci rozšíření do pole prostředků pro virtuální počítač. Řádek "dřezy"&mdash; "AzMonSink" a odpovídající "SinksConfig"&mdash;dále v části povolit rozšíření vyzařovat metriky přímo do Azure Monitor. Podle potřeby můžete přidat nebo odebrat čítače výkonu.


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
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
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


## <a name="deploy-the-resource-manager-template"></a>Nasazení šablony Správce prostředků

> [!NOTE]
> Musíte spustit rozšíření Diagnostika Azure verze 1.5 nebo vyšší a mají **autoUpgradeMinorVersion**: vlastnost nastavena na 'true' v šabloně Správce prostředků. Azure pak načte správné rozšíření při spuštění virtuálního počítače. Pokud tato nastavení v šabloně nemáte, změňte je a šablonu znovu nasaďte.


K nasazení šablony Správce prostředků využíváme Azure PowerShell.

1. Spusťte PowerShell.
1. Přihlaste se `Login-AzAccount`do Azure pomocí .
1. Získejte seznam předplatných `Get-AzSubscription`pomocí aplikace .
1. Nastavte předplatné, které používáte k vytvoření nebo aktualizaci virtuálního počítače v:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Chcete-li vytvořit novou skupinu prostředků pro virtuální hod, který se nasazuje, spusťte následující příkaz:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Nezapomeňte [použít oblast Azure, která je povolená pro vlastní metriky](metrics-custom-overview.md).

1. Spusťte následující příkazy k nasazení virtuálního počítače pomocí šablony Správce prostředků.
   > [!NOTE]
   > Pokud chcete aktualizovat existující virtuální hod, jednoduše přidejte *přírůstkové -Mode* na konec následujícího příkazu.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Po úspěšném nasazení by měl být virtuální počítač na webu Azure Portal a vypouštět metriky do Azure Monitoru.

   > [!NOTE]
   > Můžete narazit na chyby kolem vybraného vmSkuSize. Pokud k tomu dojde, vraťte se do souboru azuredeploy.json a aktualizujte výchozí hodnotu parametru vmSkuSize. V tomto případě doporučujeme vyzkoušet "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Graf metriky

1. Přihlaste se k webu Azure Portal.

2. V levé nabídce vyberte **možnost Sledovat**.

3. Na stránce Monitor vyberte **Metriky**.

   ![Stránka Metriky](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Změňte období agregace na **Posledních 30 minut**.

5. V rozevírací nabídce prostředků vyberte virtuální ms, který jste vytvořili. Pokud jste název v šabloně nezměnili, měl by to být *SimpleWinVM2*.

6. V rozevírací nabídce Obory názvů vyberte **azure.vm.windows.guest.**

7. V rozevírací nabídce metrik vyberte **paměť\%potvrzené bajty v použití**.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastních metrikách](metrics-custom-overview.md).

