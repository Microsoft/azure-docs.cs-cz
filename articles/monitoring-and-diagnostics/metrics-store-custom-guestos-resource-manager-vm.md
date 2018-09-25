---
title: Odeslání metrik operačního systému hosta metriky Azure Monitor ukládat pomocí šablony Resource Manageru pro virtuální počítač Windows
description: Odeslání metrik operačního systému hosta metriky Azure Monitor ukládat pomocí šablony Resource Manageru pro virtuální počítač Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 4ed911766a14dd35ea662326a5d50df11cf81698
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984060"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Odeslání metrik operačního systému hosta metriky Azure Monitor ukládat pomocí šablony Resource Manageru pro virtuální počítač Windows

Azure Monitor [rozšíření Windows Azure Diagnostics](azure-diagnostics.md) (WAD) umožňuje shromažďovat metriky a protokoly spuštění hostovaný operační systém (guest OS) jako součást clusteru virtuální počítač, Cloudovou službu nebo Service Fabric.  Rozšíření mohla odesílat telemetrii do mnoha různých umístěních uvedených v dříve odkazovaném článku.  

Tento článek popisuje postup na metriky výkonu operačního systému odeslat hosta pro virtuální počítač Windows do úložiště dat Azure Monitor. Spouští se s využitím WAD verze 1.11, můžete napsat metriky přímo k úložišti Azure Monitor metrik, kde již shromažďuje metriky na standard platformy. Ukládání v tomto umístění umožňuje přístup ke stejné akce k dispozici pro platformy metriky.  Akce zahrnují téměř v reálném čase výstrahy, grafů, směrování, přístup k rozhraní REST API a další.  V minulosti rozšíření WAD zapsáno do služby Azure Storage, ale ne úložiště dat Azure Monitor.   

Pokud jste začínáte se šablonami Resource Manageru, přečtěte si o [nasazení šablon](../azure-resource-manager/resource-group-overview.md)a jejich struktury a syntaxe.  

## <a name="pre-requisites"></a>Požadavky

- Předplatné musí být zaregistrovaná s [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Musíte mít buď [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) nainstalovaná, nebo můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

 
## <a name="set-up-azure-monitor-as-a-data-sink"></a>Nastavení jako datová jímka Azure monitoru 
Rozšíření Azure Diagnostics používá funkci s názvem "dat jímky" trasy metriky a protokoly do různých umístění.  Následující kroky ukazují, jak pomocí šablony Resource Manageru a Powershellu k nasazení virtuálního počítače pomocí nového datová jímka "Azure Monitor". 

## <a name="author-resource-manager-template"></a>Šablony Resource Manageru autora 
V tomto příkladu můžete použít veřejně dostupné Ukázková šablona. Výchozí šablony jsou v https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows 

- **Azuredeploy.JSON** je předem nakonfigurované šablony Resource Manageru pro nasazení virtuálního počítače. 

- **Azuredeploy.Parameters.JSON** je soubor parametrů, která ukládá informace, například jaké uživatelské jméno a heslo, které byste rádi nastavení pro váš virtuální počítač. Během nasazování šablony Resource Manageru používá parametry v tomto souboru. 

Stáhněte a uložte místně oba soubory. 

###  <a name="modify-azuredeployparametersjson"></a>Upravit azuredeploy.parameters.json
Otevřít *azuredeploy.parameters.json* souboru 

1. Zadejte hodnoty pro *adminUsername* a *adminPassword* pro virtuální počítač. Tyto parametry se používají pro vzdálený přístup k virtuálnímu počítači. Abyste se vyhnuli nutnosti váš virtuální počítač zachycena nepoužívejte v této šabloně. Roboti prohledávání Internetu uživatelských jmen a hesel ve veřejných úložištích Github. Jsou pravděpodobně testovat virtuální počítače s tyto výchozí hodnoty.  

1. Vytvořte jedinečný dnsname pro virtuální počítač.  

### <a name="modify-azuredeployjson"></a>Upravit azuredeploy.json

Otevřít *azuredeploy.json* souboru 

Přidat ID účtu úložiště do **proměnné** část šablony po vstupu pro **storageAccountName**.  

```json
// Find these lines 
"variables": { 
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]", 

// Add this line directly below.  
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]", 
```

Toto rozšíření Identity spravované služby (MSI) přidejte do šablony v horní část "resources".  Rozšíření zajistí, že Azure Monitor přijímá metriky probíhá emitovány.  

```json
//Find this code 
"resources": [
// Add this code directly below
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

Přidáte konfiguraci "identity" k prostředku virtuálního počítače zajistit Azure přiřadí rozšíření MSI systému identit. Tento krok zajistí, že virtuální počítač můžete vygenerovat metrik hosta o samotné do Azure monitoru 

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

Přidejte následující konfigurace pro povolení diagnostického rozšíření na virtuální počítač Windows.  Jednoduchý využívající Resource Manager virtuální počítač můžeme přidat konfiguraci rozšíření k poli prostředky pro virtuální počítač. Řádek "jímky": "AzMonSink" a odpovídající "SinksConfig" dále v části Povolit rozšíření generovat metriky přímo do Azure monitoru. Můžete přidat nebo odebrat čítače výkonu podle potřeby.  


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
            "typeHandlerVersion": "1.4", 
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


Uložte a zavřete oba soubory 
 

## <a name="deploy-the-resource-manager-template"></a>Nasazení šablony Resource Manageru 

> [!NOTE]
> Musíte používat Azure Diagnostics rozšíření verze 1.5 nebo novější a mají "verzi autoUpgradeMinorVersion": vlastnost na hodnotu "true" v šabloně Resource Manageru.  Azure pak načte správné rozšíření při spuštění virtuálního počítače. Pokud nemáte tato nastavení v šabloně, je změnit a znovu nasaďte šablonu. 


Pokud chcete nasadit šablonu Resource Manageru jsme bude využívat Azure Powershellu.  

1. Spuštění PowerShellu 
1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`
1. Získat seznam vašich předplatných pomocí `Get-AzureRmSubscription`
1. Nastavte předplatné, které vám bude možné vytváření nebo aktualizaci virtuálního počítače 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Vytvořte novou skupinu prostředků pro virtuální počítač, nasazení, spouštění následující příkaz 

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>" 
   ```
   > [!NOTE] 
   > Nezapomeňte [použijte oblast Azure, který je povolený pro vlastní metriky](metrics-custom-overview.md). 
 
1. Spuštěním následujících příkazů nasaďte virtuální počítač se  
   > [!NOTE] 
   > Pokud chcete aktualizovat existující virtuální počítač, stačí přidat *-režim přírůstkového* na konec příkazu. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>" 
   ```
  
1. Po úspěšném nasazení byste měli najít virtuální počítač na webu Azure Portal a by měl být generování metrik do Azure monitoru. 

   > [!NOTE] 
   > Pravděpodobně dojde k chybám kolem vybrané vmSkuSize. Pokud k tomu dojde, vraťte se do souboru azuredeploy.json a aktualizujte výchozí hodnotu parametru vmSkuSize. V takovém případě doporučujeme vyzkoušet "Standard_DS1_v2"). 

## <a name="chart-your-metrics"></a>Graf metrik 

1. Přihlaste se k webu Azure Portal 

1. V nabídce vlevo klikněte na tlačítko **monitorování** 

1. Klikněte na stránce monitorování **metriky**. 

   ![Stránka metriky](./media/metrics-store-custom-rest-api/metrics.png) 

1. Na období agregace změnit **posledních 30 minut**.  

1. V rozevíracím seznamu prostředků vyberte virtuální počítač právě vytvořili. Pokud jste nezměnili název v šabloně, měla by být *SimpleWinVM2*.  

1. V oborech názvů rozevíracího seznamu vyberte **azure.vm.windows.guest** 

1. Metriky rozevírací seznam, zaškrtněte **paměti\%používané svěřené bajty použití**.  
 

## <a name="next-steps"></a>Další postup
- Další informace o [vlastní metriky](metrics-custom-overview.md).