---
title: Shromažďování metrik škálovací sady Windows v Azure Monitoru pomocí šablony
description: Odeslání metrik hostovaného operačního systému do úložiště metrik Azure Monitor pomocí šablony Správce prostředků pro škálovací sadu virtuálních strojů s Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 24f83e4f6285d045e67bdaef431ebcff2345ef84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663888"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Odeslání metrik hostovaného operačního systému do úložiště metrik Azure Monitor pomocí šablony Azure Resource Manager pro škálovací sadu virtuálních strojů windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pomocí rozšíření Azure Monitor [Windows Azure Diagnostics (WAD)](diagnostics-extension-overview.md)můžete shromažďovat metriky a protokoly z hostovaného operačního systému (hostovaný operační systém), který běží jako součást virtuálního počítače, cloudové služby nebo clusteru Azure Service Fabric. Rozšíření můžete odeslat telemetrie do mnoha různých umístění uvedených v dříve propojeném článku.  

Tento článek popisuje proces odesílání metrik výkonu hostovaného operačního systému pro škálovací sadu virtuálních strojů Windows do úložiště dat Azure Monitor. Počínaje Windows Azure Diagnostics verze 1.11, můžete psát metriky přímo do úložiště metrik Azure Monitor, kde se už shromažďují standardní metriky platformy. Uložením je v tomto umístění, můžete přistupovat ke stejným akcím, které jsou k dispozici pro metriky platformy. Akce zahrnují téměř v reálném čase upozorňování, vytváření grafů, směrování, přístup z rozhraní REST API a další. V minulosti rozšíření Diagnostika Windows Azure zapisovalo do Azure Storage, ale ne do úložiště dat Azure Monitor.  

Pokud s šablonami Správce prostředků tečte, přečtěte si informace o [nasazeních šablon](../../azure-resource-manager/management/overview.md) a jejich struktuře a syntaxi.  

## <a name="prerequisites"></a>Požadavky

- Vaše předplatné musí být zaregistrováno u [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Musíte mít nainstalovaný [Azure PowerShell,](/powershell/azure) nebo můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 

- Prostředek virtuálního počítače musí být v [oblasti, která podporuje vlastní metriky](metrics-custom-overview.md#supported-regions).

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Nastavení Azure Monitoru jako jímky dat 
Rozšíření Diagnostika Azure používá funkci s názvem **jímky dat** ke směrování metrikami a protokoly do různých umístění. Následující kroky ukazují, jak použít šablonu Správce prostředků a Prostředí PowerShell k nasazení virtuálního počítače pomocí nového jímky dat Azure Monitor. 

## <a name="author-a-resource-manager-template"></a>Vytvoření šablony Správce prostředků 
V tomto příkladu můžete použít veřejně dostupnou [ukázkovou šablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale):  

- **Azuredeploy.json** je předkonfigurovaná šablona Správce prostředků pro nasazení škálovací sady virtuálních strojů.

- **Azuredeploy.parameters.json** je soubor parametrů, který ukládá informace, jako je jaké uživatelské jméno a heslo, které chcete nastavit pro váš virtuální počítač. Během nasazení používá šablona Správce prostředků parametry nastavené v tomto souboru. 

Stáhněte a uložte oba soubory místně. 

###  <a name="modify-azuredeployparametersjson"></a>Úprava souboru azuredeploy.parameters.json
Otevřete soubor **azuredeploy.parameters.json:**  
 
- Zadejte **vmSKU,** který chcete nasadit. Doporučujeme Standard_D2_v3. 
- Zadejte **windowsOSVersion,** kterou chcete pro škálovací sadu virtuálních strojů. Doporučujeme 2016-Datacenter. 
- Pojmenujte prostředek škálovací sady virtuálních strojů, který má být nasazen pomocí vlastnosti **vmssName.** Příkladem je **VMSS-WAD-TEST**.    
- Zadejte počet virtuálních počítačů, které chcete spustit na škálovací sadě virtuálního počítače, pomocí **vlastnosti instanceCount.**
- Zadejte hodnoty pro **adminUživatelské jméno** a **adminPassword** pro škálovací sadu virtuálních strojů. Tyto parametry se používají pro vzdálený přístup k virtuálním mům ve škálovací sadě. Chcete-li se vyhnout únosu virtuálního počítače, **nepoužívejte** ty v této šabloně. Boti naskenují na internetu uživatelská jména a hesla ve veřejných úložištích GitHub. Je pravděpodobné, že testování virtuálních ms s těmito výchozími hodnotami. 


###  <a name="modify-azuredeployjson"></a>Úprava souboru azuredeploy.json
Otevřete soubor **azuredeploy.json.** 

Přidejte proměnnou, která uchovává informace o účtu úložiště v šabloně Správce prostředků. Všechny protokoly nebo čítače výkonu zadané v diagnostickém konfiguračním souboru se zapisují do úložiště metrik Azure Monitor u úložiště i do účtu úložiště, který zde zadáte: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Najděte definici škálovací sady virtuálního počítače v části prostředky a přidejte oddíl **identity** do konfigurace. Toto přidání zajišťuje, že Azure přiřadí systémové identity. Tento krok také zajišťuje, že virtuální počítače ve škálovací sadě můžete vyzařovat metriky hosta o sobě do Azure Monitoru:  

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

V prostředku škálovací sady virtuálních strojů najděte oddíl **virtualMachineProfile.** Přidejte nový profil s názvem **extensionsProfile** pro správu rozšíření.  


V **rozšířeníProfil**přidejte nové rozšíření do šablony, jak je znázorněno v části **rozšíření VMSS-WAD.**  Tato část je spravované identity pro rozšíření prostředků Azure, které zajišťuje metriky emitované jsou přijímány Azure Monitor. Pole **názvu** může obsahovat libovolný název. 

Následující kód z rozšíření MSI také přidá rozšíření diagnostiky a konfiguraci jako prostředek rozšíření k prostředku škálovací sady virtuálních počítačů. Nebojte se podle potřeby přidávat nebo odebírat čítače výkonu: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
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


Přidejte **a dependsOn** pro účet úložiště, abyste zajistili, že se vytvoří ve správném pořadí: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Vytvořte účet úložiště, pokud ještě není v šabloně vytvořený: 

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

## <a name="deploy-the-resource-manager-template"></a>Nasazení šablony Správce prostředků 

> [!NOTE]  
> Musíte spouštět rozšíření Diagnostika Azure verze 1.5 nebo vyšší **a** mít **vlastnost autoUpgradeMinorVersion:** nastavenou na **hodnotu true** v šabloně Správce prostředků. Azure pak načte správné rozšíření při spuštění virtuálního počítače. Pokud tato nastavení v šabloně nemáte, změňte je a šablonu znovu nasaďte. 


Pokud chcete nasadit šablonu Správce prostředků, použijte Azure PowerShell:  

1. Spusťte PowerShell. 
1. Přihlaste se `Login-AzAccount`k Azure pomocí .
1. Získejte seznam předplatných `Get-AzSubscription`pomocí aplikace .
1. Nastavte předplatné, které vytvoříte, nebo aktualizujte virtuální počítač: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Vytvořte novou skupinu prostředků pro naváděný virtuální ms. Spusťte následující příkaz: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Nezapomeňte použít oblast Azure, která je povolená pro vlastní metriky. Nezapomeňte použít [oblast Azure, která je povolená pro vlastní metriky](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Spusťte následující příkazy k nasazení virtuálního virtuálního mísy:  

   > [!NOTE]  
   > Pokud chcete aktualizovat existující škálovací sadu, přidejte **přírůstkové -Mode** na konec příkazu. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Po úspěšném nasazení byste měli najít škálovací sadu virtuálních strojů na webu Azure Portal. Měl by vyzařovat metriky do Azure Monitoru. 

   > [!NOTE]  
   > Můžete narazit na chyby kolem vybraného **vmSkuSize**. V takovém případě se vraťte do souboru **azuredeploy.json** a aktualizujte výchozí hodnotu parametru **vmSkuSize.** Doporučujeme vyzkoušet **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Graf metriky 

1. Přihlaste se k portálu Azure. 

1. V levé nabídce vyberte **monitor .** 

1. Na stránce **Monitor** vyberte **Metriky**. 

   ![Monitor – stránka Metriky](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Změňte období agregace na **Posledních 30 minut**.  

1. V rozevírací nabídce prostředků vyberte vytvořenou škálovací sadu virtuálních strojů.  

1. V rozevírací nabídce Obory názvů vyberte **azure.vm.windows.guest**. 

1. V rozevírací nabídce metriky vyberte **paměť\%potvrzené bajty v použití**.  

Potom můžete také použít dimenze na této metriky k jejich vykreslení pro konkrétní virtuální počítač nebo k vykreslení jednotlivých virtuálních počítačů ve škálovací sadě. 



## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastních metrikách](metrics-custom-overview.md).


