---
title: Odeslání metrik hostovaného operačního systému k úložišti Azure Monitor metriky s použitím šablony Azure Resource Manageru pro škálovací sadu virtuálních počítačů s Windows
description: Odeslání metrik hostovaného operačního systému k úložišti Azure Monitor metriku pomocí šablony Resource Manageru pro škálovací sadu virtuálních počítačů s Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 52d82e20b2156b503de3dc24ea6e01ecd7088d08
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037478"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Odeslání metrik hostovaného operačního systému k úložišti Azure Monitor metriky s použitím šablony Azure Resource Manageru pro škálovací sadu virtuálních počítačů s Windows

Pomocí Azure monitoru [rozšíření Windows Azure Diagnostics (WAD)](azure-diagnostics.md), můžete shromažďovat metriky a protokoly z hostovaný operační systém (guest OS), které se spouští jako součást virtuální počítač, cloudovou službu nebo clusteru Azure Service Fabric. Rozšíření mohla odesílat telemetrii do mnoha různých umístěních uvedených v dříve odkazovaném článku.  

Tento článek popisuje postup odesílání hostovaného operačního systému metriky výkonu pro Windows škálovací sady virtuálních počítačů k úložišti Azure Monitor. Od verze Windows Azure Diagnostics verze 1.11, můžete psát, že se metriky přímo k metrikám monitorování Azure storu, kde již shromažďuje metriky na standard platformy. Podle jejich ukládáním do tohoto umístění, můžete přístup ke stejné akce, které jsou k dispozici pro platformy metriky. Akce zahrnují téměř v reálném čase výstrahy, grafů, směrování, přístup k rozhraní REST API a další. V minulosti rozšíření Windows Azure Diagnostics zapsáno do služby Azure Storage, ale ne úložiště dat Azure Monitor.  

Pokud jste začínáte se šablonami Resource Manageru, přečtěte si o [nasazení šablon](../azure-resource-manager/resource-group-overview.md) a jejich struktury a syntaxe.  

## <a name="prerequisites"></a>Požadavky

- Předplatné musí být zaregistrovaná s [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal). 

- Musíte mít [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) nainstalovaná, nebo můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Nastavení jako datová jímka Azure monitoru 
Rozšíření Azure Diagnostics používá funkci s názvem **datových jímek** trasy metriky a protokoly do různých umístění. Následující kroky ukazují, jak pomocí šablony Resource Manageru a Powershellu k nasazení virtuálního počítače s použitím nového datová jímka Azure monitoru. 

## <a name="author-a-resource-manager-template"></a>Autor šablony Resource Manageru 
V tomto příkladu můžete použít veřejně dostupné [Ukázková šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale):  

- **Azuredeploy.JSON** je předem nakonfigurované šablony Resource Manageru pro nasazení škálovací sady virtuálních počítačů.

- **Azuredeploy.Parameters.JSON** je soubor parametrů, která ukládá informace, například jaké uživatelské jméno a heslo, kterou chcete nastavit pro váš virtuální počítač. Během nasazování šablony Resource Manageru používá parametry v tomto souboru. 

Stáhněte a uložte místně oba soubory. 

###  <a name="modify-azuredeployparametersjson"></a>Upravit azuredeploy.parameters.json
Otevřít **azuredeploy.parameters.json** souboru:  
 
- Zadejte **vmSKU** chcete nasadit. Doporučujeme, abyste Standard_D2_v3. 
- Zadejte **windowsOSVersion** chcete použít pro škálovací sadu virtuálních počítačů. Doporučujeme, abyste 2016 Datacenter. 
- Název virtuálního počítače škálovací sady prostředek, který se dá nasadit pomocí **vmssName** vlastnost. Příkladem je **VMSS-WAD-TEST**.    
- Zadejte počet virtuálních počítačů, které chcete spustit na škálovací sadu s použitím virtuálních počítačů **instanceCount** vlastnost.
- Zadejte hodnoty pro **adminUsername** a **adminPassword** škálování virtuálního počítače nastavte. Tyto parametry se používají pro vzdálený přístup k virtuálním počítačům ve škálovací sadě. Abyste se vyhnuli nutnosti váš virtuální počítač zachycena, **nejsou** použít dotazy v této šabloně. Roboti prohledávání Internetu uživatelských jmen a hesel ve veřejných úložištích GitHub. Jsou to pravděpodobně testovat virtuální počítače s tyto výchozí hodnoty. 


###  <a name="modify-azuredeployjson"></a>Upravit azuredeploy.json
Otevřít **azuredeploy.json** souboru. 

Přidejte proměnnou pro uchování informací o účtu úložiště v šabloně Resource Manageru. Všechny protokoly a čítače výkonu zadaný v konfigurační soubor diagnostiky se zapisují do úložiště metrik Azure monitoru a účet úložiště, kterou zde zadáte: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Najděte definici virtuálního počítače škálovací sady v části prostředky a přidejte **identity** části ke konfiguraci. Toto přidání zajistí, že Azure přiřadí identitu systému. Tento krok také zajistí, že virtuální počítače ve škálovací sadě můžete generovat metrik hosta o samotné do Azure monitoru:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

Ve virtuálním počítači škálovací sady prostředků, vyhledejte **virtualMachineProfile** oddílu. Přidat nový profil, který volá **extensionsProfile** pro správu rozšíření.  


V **extensionProfile**, přidat nové rozšíření do šablony, jak je znázorněno **příponu VMSS WAD** oddílu.  Tato část se spravovaným identitám pro rozšíření prostředků Azure, které zajistí, že probíhá emitovány metriky jsou přijaty Azure Monitor. **Název** pole může obsahovat libovolný název. 

Následující kód z rozšíření MSI také přidává se diagnostické rozšíření a konfigurací jako prostředek rozšíření k prostředku virtuálního počítače škálovací sady. Můžete bez obav s přidáváním a odebíráním čítače výkonu, podle potřeby: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identites for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Přidat **dependsOn** pro účet úložiště, aby se vytvoří ve správném pořadí: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Vytvoření účtu úložiště, pokud jeden není už v šabloně: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Uložte a zavřete oba soubory. 

## <a name="deploy-the-resource-manager-template"></a>Nasazení šablony Resource Manageru 

> [!NOTE]  
> Musíte používat verzi rozšíření diagnostiky Azure 1.5 nebo vyšší **a** mít **autoUpgradeMinorVersion:** vlastnost nastavena na hodnotu **true** ve vaší službě Správce prostředků Šablona. Azure pak načte správné rozšíření při spuštění virtuálního počítače. Pokud nemáte k dispozici tato nastavení v šabloně, je změnit a znovu nasaďte šablonu. 


Pokud chcete nasadit šablonu Resource Manageru, pomocí Azure Powershellu:  

1. Spusťte PowerShell. 
1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`.
1. Získat seznam vašich předplatných s použitím `Get-AzureRmSubscription`.
1. Nastavení předplatného budete vytvořit nebo aktualizovat virtuální počítač: 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Vytvořte novou skupinu prostředků pro virtuální počítač se nasazuje. Spusťte následující příkaz: 

   ```PowerShell
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Nezapomeňte použít oblast Azure, který je povolený pro vlastní metriky. Nezapomeňte použít [oblast Azure, která je povolená pro vlastní metriky](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Spusťte následující příkazy, které je virtuální počítač nasadit:  

   > [!NOTE]  
   > Pokud chcete aktualizovat existující škálovací sadě, přidejte **-režim přírůstkového** na konec příkazu. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Po úspěšném nasazení, měli byste najít virtuálního počítače škálovací sady na webu Azure Portal. To by měly vydávat metrik do Azure monitoru. 

   > [!NOTE]  
   > Můžete narazit na chyby kolem vybrané **vmSkuSize**. V takovém případě přejděte zpět do vaší **azuredeploy.json** souboru a aktualizujte výchozí hodnota **vmSkuSize** parametr. Doporučujeme vám vyzkoušet **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Graf metrik 

1. Přihlaste se k portálu Azure. 

1. V nabídce vlevo vyberte **monitorování**. 

1. Na **monitorování** stránce **metriky**. 

   ![Monitorování – metrika stránky](media/metrics-store-custom-guestos-resource-manager-vmss/metrics.png) 

1. Na období agregace změnit **posledních 30 minut**.  

1. V rozevírací nabídce prostředků vyberte škálovací sadu virtuálních počítačů, které jste vytvořili.  

1. V rozevírací nabídce obory názvů, vyberte **azure.vm.windows.guest**. 

1. V rozevírací nabídce metrik vyberte **paměti\%používané svěřené bajty použití**.  

Potom můžete také pomocí dimenzí na tuto metriku na graf pro konkrétního virtuálního počítače nebo k vykreslení každého virtuálního počítače ve škálovací sadě. 



## <a name="next-steps"></a>Další postup
- Další informace o [vlastní metriky](metrics-custom-overview.md).

