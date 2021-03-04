---
title: Shromažďování metrik virtuálních počítačů s Windows v Azure Monitor se šablonou
description: Odeslání metriky hostovaného operačního systému do úložiště databáze metriky Azure Monitor pomocí šablony Správce prostředků pro virtuální počítač s Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: bwren
ms.openlocfilehash: 8e510cf2e6fed9f9ffdec1dcc4dacf16a866d66b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049011"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine"></a>Odeslání metriky hostovaného operačního systému do úložiště metriky Azure Monitor pomocí šablony Azure Resource Manager pro virtuální počítač s Windows
Údaje o výkonu z hostovaného operačního systému virtuálních počítačů Azure se neshromažďují automaticky jako jiné [metriky platforem](./monitor-azure-resource.md#monitoring-data). Nainstalujte rozšíření Azure Monitor [Diagnostics](../agents/diagnostics-extension-overview.md) , které shromáždí metriky operačního systému hosta do databáze metrik, takže se dá použít se všemi funkcemi Azure monitor metrik, včetně výstrah v reálném čase, vytváření grafů, směrování a přístupu z REST API. Tento článek popisuje proces odeslání metrik výkonu hostovaného operačního systému pro virtuální počítač s Windows do databáze metrik pomocí šablony Správce prostředků. 

> [!NOTE]
> Podrobnosti o konfiguraci diagnostického rozšíření pro shromažďování metrik operačního systému hosta pomocí Azure Portal najdete v tématu [instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](../agents/diagnostics-extension-windows-install.md).


Pokud s Správce prostředků šablonou začínáte, přečtěte si o [Nasazení šablon](../../azure-resource-manager/management/overview.md) a jejich struktuře a syntaxi.

## <a name="prerequisites"></a>Požadavky

- Vaše předplatné musí být zaregistrované ve službě [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md).

- Musíte mít nainstalované buď [Azure PowerShell](/powershell/azure) , nebo [Azure Cloud Shell](../../cloud-shell/overview.md) .

- Prostředek virtuálního počítače musí být v [oblasti, která podporuje vlastní metriky](./metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Nastavení Azure Monitor jako datové jímky
Rozšíření Azure Diagnostics používá funkci s názvem "datové jímky" ke směrování metrik a protokolů do různých umístění. Následující kroky ukazují, jak použít šablonu Správce prostředků a PowerShell k nasazení virtuálního počítače pomocí nové jímky dat "Azure Monitor".

## <a name="author-resource-manager-template"></a>Šablona pro vytváření Správce prostředků
V tomto příkladu můžete použít veřejně dostupnou ukázkovou šablonu. Spouští se šablony na adrese https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows .

- **Azuredeploy.jsv** je předkonfigurovaná správce prostředků šablona pro nasazení virtuálního počítače.

- **Azuredeploy.parameters.json** je soubor parametrů, který ukládá informace, jako je třeba uživatelské jméno a heslo, které byste chtěli pro virtuální počítač nastavit. Během nasazování používá šablona Správce prostředků parametry, které jsou nastaveny v tomto souboru.

Stáhněte a uložte oba soubory místně.

### <a name="modify-azuredeployparametersjson"></a>Upravit azuredeploy.parameters.jsna
Otevřít *azuredeploy.parameters.jsv* souboru

1. Zadejte hodnoty pro **adminUsername** a **adminPassword** pro virtuální počítač. Tyto parametry se používají pro vzdálený přístup k virtuálnímu počítači. Abyste se vyhnuli napadení virtuálního počítače, nepoužívejte hodnoty v této šabloně. Roboty vyhledá v Internetu uživatelská jména a hesla ve veřejných úložištích GitHubu. Pravděpodobně budou testovány virtuální počítače s těmito výchozími hodnotami.

1. Vytvořte jedinečný DnsName pro virtuální počítač.

### <a name="modify-azuredeployjson"></a>Upravit azuredeploy.jsna

Otevřít *azuredeploy.jsv* souboru

Po zadání položky pro StorageAccountName přidejte do části **proměnné** této šablony ID účtu úložiště **.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Přidejte toto rozšíření Identita spravované služby (MSI) do šablony v horní části oddílu **Resources (prostředky** ). Rozšíření zajišťuje, že Azure Monitor akceptuje metriky, které jsou emitovány.

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

Přidejte konfiguraci **identity** do prostředku virtuálního počítače, abyste měli jistotu, že Azure přiřadí k rozšíření MSI identitu systému. Tento krok zajistí, že virtuální počítač může vygenerovat metriky hostů o sobě samý Azure Monitor.

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

Přidejte následující konfiguraci, aby bylo možné povolit diagnostické rozšíření na virtuálním počítači s Windows. Pro jednoduchý Správce prostředků virtuální počítač můžeme přidat konfiguraci rozšíření do pole prostředky pro virtuální počítač. Řádek "jímky" " &mdash; AzMonSink" a odpovídající "SinksConfig" dále v části &mdash; umožňují rozšíření generovat metriky přímo do Azure monitor. Podle potřeby můžete přidávat nebo odebírat čítače výkonu.


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


Oba soubory uložte a zavřete.


## <a name="deploy-the-resource-manager-template"></a>Nasazení šablony Správce prostředků

> [!NOTE]
> Musíte používat rozšíření Azure Diagnostics verze 1,5 nebo vyšší a mít vlastnost **autoUpgradeMinorVersion**: nastavenou na hodnotu true ve vaší šabloně správce prostředků. Azure pak při spuštění virtuálního počítače načte správnou příponu. Pokud tato nastavení v šabloně nemáte, změňte je a znovu nasaďte šablonu.


K nasazení šablony Správce prostředků využíváme Azure PowerShell.

1. Spusťte PowerShell.
1. Přihlaste se k Azure pomocí `Login-AzAccount` .
1. Seznam předplatných získáte pomocí `Get-AzSubscription` .
1. Nastavte předplatné, které používáte k vytvoření nebo aktualizaci virtuálního počítače v nástroji:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Pokud chcete vytvořit novou skupinu prostředků pro nasazený virtuální počítač, spusťte následující příkaz:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Nezapomeňte [použít oblast Azure, která je povolená pro vlastní metriky](./metrics-custom-overview.md).

1. Spuštěním následujících příkazů nasaďte virtuální počítač pomocí šablony Správce prostředků.
   > [!NOTE]
   > Pokud chcete aktualizovat existující virtuální počítač, jednoduše přidaný *režim* přidávejte na konec následujícího příkazu.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Po úspěšném nasazení by měl být virtuální počítač ve Azure Portal, což generuje metriky Azure Monitor.

   > [!NOTE]
   > Můžete spustit chybu kolem vybraného vmSkuSize. Pokud k tomu dojde, vraťte se do azuredeploy.jsv souboru a aktualizujte výchozí hodnotu parametru vmSkuSize. V takovém případě doporučujeme vyzkoušet "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Vytvoření grafu metrik

1. Přihlaste se k webu Azure Portal.

2. V nabídce vlevo vyberte **monitor**.

3. Na stránce Monitor vyberte **metriky**.

   ![Stránka metriky](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Změňte období agregace na **posledních 30 minut**.

5. V rozevírací nabídce prostředek vyberte virtuální počítač, který jste vytvořili. Pokud jste název v šabloně nezměnili, měl by být *SimpleWinVM2*.

6. V rozevírací nabídce obory názvů vyberte **Azure. VM. Windows. Host.**

7. V rozevírací nabídce metriky vyberte **paměť \% při použití potvrzených bajtů**.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastních metrikách](./metrics-custom-overview.md).