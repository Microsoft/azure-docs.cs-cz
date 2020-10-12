---
title: Shromažďovat metriky Windows škálované sady v Azure Monitor se šablonou
description: Odeslání metriky hostovaného operačního systému do úložiště metriky Azure Monitor pomocí šablony Správce prostředků pro sadu škálování virtuálního počítače s Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 76f73df01b34cb20be48aefa3b5b3a6392a35b8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87045192"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Odeslání metriky hostovaného operačního systému do úložiště metriky Azure Monitor pomocí šablony Azure Resource Manager pro sadu škálování virtuálního počítače s Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pomocí [rozšíření Azure monitor Windows Azure Diagnostics (WAD)](diagnostics-extension-overview.md)můžete shromažďovat metriky a protokoly z hostovaného operačního systému (hostovaného operačního systému), který běží jako součást virtuálního počítače, cloudové služby nebo clusteru Azure Service Fabric. Rozšíření může odesílat telemetrii do mnoha různých umístění uvedených v dříve propojeném článku.  

Tento článek popisuje proces odeslání metrik výkonu hostovaného operačního systému pro sadu škálování virtuálního počítače s Windows do úložiště dat Azure Monitor. Počínaje systémem Windows Azure Diagnostics verze 1,11 můžete zapisovat metriky přímo do úložiště metrik Azure Monitor, kde jsou již shromažďovány standardní metriky platforem. Jejich uložením do tohoto umístění můžete získat přístup ke stejným akcím, které jsou k dispozici pro metriky platforem. Mezi akce patří výstrahy týkající se upozorňování v reálném čase, vytváření grafů, směrování, přístup z REST API a další. V minulosti rozšíření Azure Diagnostics Windows zapsalo, aby Azure Storage, ale ne úložiště dat Azure Monitor.  

Pokud s Správce prostředků šablonou začínáte, přečtěte si o [Nasazení šablon](../../azure-resource-manager/management/overview.md) a jejich struktuře a syntaxi.  

## <a name="prerequisites"></a>Požadavky

- Vaše předplatné musí být zaregistrované ve službě [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Musíte mít nainstalovaný [Azure PowerShell](/powershell/azure) , nebo můžete použít [Azure Cloud Shell](../../cloud-shell/overview.md). 

- Prostředek virtuálního počítače musí být v [oblasti, která podporuje vlastní metriky](metrics-custom-overview.md#supported-regions).

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Nastavení Azure Monitor jako datové jímky 
Rozšíření Azure Diagnostics používá funkci nazvanou **datové jímky** k směrování metrik a protokolů do různých umístění. Následující kroky ukazují, jak použít Správce prostředků šablonu a PowerShell k nasazení virtuálního počítače pomocí nové jímky dat Azure Monitor. 

## <a name="author-a-resource-manager-template"></a>Vytvořit šablonu Správce prostředků 
V tomto příkladu můžete použít veřejně dostupnou [ukázkovou šablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale):  

- **Azuredeploy.json** je předkonfigurovaná správce prostředků šablona pro nasazení sady škálování virtuálního počítače.

- **Azuredeploy.parameters.json** je soubor parametrů, který ukládá informace, jako je třeba uživatelské jméno a heslo, které chcete nastavit pro virtuální počítač. Během nasazování používá šablona Správce prostředků parametry nastavené v tomto souboru. 

Stáhněte a uložte oba soubory místně. 

###  <a name="modify-azuredeployparametersjson"></a>Upravit azuredeploy.parameters.jsna
Otevřete **azuredeploy.parameters.jsv** souboru:  
 
- Zadejte **vmSKU** , který chcete nasadit. Doporučujeme Standard_D2_v3. 
- Zadejte **windowsOSVersion** , který chcete použít pro sadu škálování virtuálního počítače. Doporučujeme 2016 – Datacenter. 
- Pojmenujte prostředek sady škálování virtuálního počítače, který se má nasadit, pomocí vlastnosti **vmssName** . Příkladem je **VMSS-wad-test**.    
- Zadejte počet virtuálních počítačů, které chcete spustit v sadě škálování virtuálního počítače pomocí vlastnosti **instanceCount** .
- Zadejte hodnoty pro **adminUsername** a **adminPassword** pro sadu škálování virtuálního počítače. Tyto parametry se používají pro vzdálený přístup k virtuálním počítačům v sadě škálování. Abyste se vyhnuli napadení virtuálního **počítače, nepoužívejte ty** v této šabloně. Roboty prověřování Internetu pro uživatelská jména a hesla ve veřejných úložištích GitHubu. Budou pravděpodobně testovány virtuální počítače s těmito výchozími hodnotami. 


###  <a name="modify-azuredeployjson"></a>Upravit azuredeploy.jsna
Otevřete **azuredeploy.jsv** souboru. 

Přidejte proměnnou, která bude uchovávat informace o účtu úložiště v šabloně Správce prostředků. Všechny protokoly nebo čítače výkonu zadané v konfiguračním souboru diagnostiky se zapisují do úložiště metriky Azure Monitor a účtu úložiště, který zadáte tady: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
V části Resources (prostředky) vyhledejte definici sady škálování virtuálních počítačů a přidejte do ní oddíl **identity** . Tím se zajistí, že Azure přiřadí identitu systému. Tento krok také zajišťuje, že virtuální počítače v sadě škálování můžou vysílat metriky hostů o samy sebe, aby Azure Monitor:  

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

V prostředku sady škálování virtuálního počítače vyhledejte část **virtualMachineProfile** . Pro správu rozšíření přidejte nový profil s názvem **extensionsProfile** .  


Do **extensionProfile**přidejte nové rozšíření, jak je znázorněno v části **VMSS-wad-Extension** .  Tato část je spravovaná identita pro rozšíření prostředků Azure, která zajišťuje, že se generují metriky, které jsou přijímány Azure Monitor. Pole **název** může obsahovat libovolný název. 

Následující kód z rozšíření MSI taky přidá rozšíření a konfiguraci diagnostiky jako prostředek rozšíření do prostředku sady škálování virtuálního počítače. Podle potřeby můžete přidávat nebo odebírat čítače výkonu: 

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


Přidejte **dependsOn** pro účet úložiště, abyste se ujistili, že je vytvořený ve správném pořadí: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Pokud se v šabloně ještě nevytvoří účet úložiště, vytvořte ho. 

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

Oba soubory uložte a zavřete. 

## <a name="deploy-the-resource-manager-template"></a>Nasazení šablony Správce prostředků 

> [!NOTE]  
> Musíte používat rozšíření Azure Diagnostics verze 1,5 nebo vyšší **a** mít vlastnost **autoUpgradeMinorVersion:** nastavenou na **hodnotu true** v šabloně správce prostředků. Azure pak při spuštění virtuálního počítače načte správnou příponu. Pokud tato nastavení v šabloně nemáte, změňte je a znovu nasaďte šablonu. 


Pokud chcete nasadit šablonu Správce prostředků, použijte Azure PowerShell:  

1. Spusťte PowerShell. 
1. Přihlaste se k Azure pomocí `Login-AzAccount` .
1. Seznam předplatných získáte pomocí `Get-AzSubscription` .
1. Nastavte odběr, který vytvoříte, nebo aktualizujte virtuální počítač: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Vytvořte novou skupinu prostředků pro nasazený virtuální počítač. Spusťte následující příkaz: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Nezapomeňte použít oblast Azure, která je povolená pro vlastní metriky. Nezapomeňte použít [oblast Azure, která je povolená pro vlastní metriky](./metrics-custom-overview.md#supported-regions).
 
1. Spuštěním následujících příkazů nasaďte virtuální počítač:  

   > [!NOTE]  
   > Pokud chcete aktualizovat existující sadu škálování, přidejte do konce příkazu **přírůstkový režim** . 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Po úspěšném nasazení byste měli v Azure Portal najít sadu škálování virtuálního počítače. Mělo by vygenerovat metriky pro Azure Monitor. 

   > [!NOTE]  
   > Můžete spustit chybu kolem vybraného **vmSkuSize**. V takovém případě se vraťte k vašemu **azuredeploy.jsv** souboru a aktualizujte výchozí hodnotu parametru **vmSkuSize** . Doporučujeme, abyste si vyzkoušeli **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Vytvoření grafu metrik 

1. Přihlaste se k portálu Azure. 

1. V nabídce na levé straně vyberte **monitor**. 

1. Na stránce **monitor** vyberte **metriky**. 

   ![Monitor – stránka metriky](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Změňte období agregace na **posledních 30 minut**.  

1. V rozevírací nabídce prostředek vyberte sadu škálování virtuálního počítače, kterou jste vytvořili.  

1. V rozevírací nabídce obory názvů vyberte **Azure. VM. Windows. Host**. 

1. V rozevírací nabídce metriky vyberte paměť, která ** \% se používá při použití svěřených bajtů**.  

Pak můžete zvolit, aby se dimenze v této metrikě použily k vytvoření grafu pro konkrétní virtuální počítač, nebo aby se každý virtuální počítač vynesl v sadě škálování. 



## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastních metrikách](metrics-custom-overview.md).
