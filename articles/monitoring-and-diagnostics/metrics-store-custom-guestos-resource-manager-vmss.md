---
title: Odeslání metrik hosta operačního systému do Azure monitoru metriky úložiště pro škálovací sadu virtuálních počítačů s Windows pomocí šablony Resource Manageru
description: Odeslání metrik hosta operačního systému do Azure monitoru metriky úložiště pro škálovací sadu virtuálních počítačů s Windows pomocí šablony Resource Manageru
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 7b600bd699ce7f9e4a6c7cba1a41b6bdece16bf0
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343720"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Odeslání metrik hosta operačního systému do Azure monitoru metriky úložiště pro škálovací sadu virtuálních počítačů s Windows pomocí šablony Resource Manageru

Azure Monitor [rozšíření Windows Azure Diagnostics](azure-diagnostics.md) (WAD) umožňuje shromažďovat metriky a protokoly spuštění hostovaný operační systém (guest OS) jako součást clusteru virtuální počítač, Cloudovou službu nebo Service Fabric.  Rozšíření mohla odesílat telemetrii do mnoha různých umístěních uvedených v dříve odkazovaném článku.  

Tento článek popisuje postup odesílání hostovaného operačního systému metriky výkonu pro Windows škálovací sady virtuálních počítačů k úložišti Azure Monitor. Spouští se s využitím WAD verze 1.11, můžete napsat metriky přímo k úložišti Azure Monitor metrik, kde již shromažďuje metriky na standard platformy. Ukládání v tomto umístění umožňuje přístup ke stejné akce k dispozici pro platformy metriky.  Akce zahrnují téměř v reálném čase výstrahy, grafů, směrování, přístup k rozhraní REST API a další.  V minulosti rozšíření WAD zapsáno do služby Azure Storage, ale ne úložiště dat Azure Monitor.  

Pokud jste ještě na šablony Resource Manageru, přečtěte si o [nasazení šablon](../azure-resource-manager/resource-group-overview.md)a jejich struktury a syntaxe.  

## <a name="pre-requisites"></a>Požadavky

- Předplatné musí být zaregistrovaná s [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Musíte mít buď [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) nainstalovaná, nebo můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Nastavení jako datová jímka Azure monitoru 
Rozšíření Azure Diagnostics používá funkci s názvem "dat jímky" trasy metriky a protokoly do různých umístění.  Následující kroky ukazují, jak pomocí šablony Resource Manageru a Powershellu k nasazení virtuálního počítače pomocí nového datová jímka "Azure Monitor". 

## <a name="author-resource-manager-template"></a>Šablony Resource Manageru autora 
V tomto příkladu můžete použít veřejně dostupné Ukázková šablona. Výchozí šablony jsou v https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale  

- **Azuredeploy.JSON** je předem nakonfigurované šablony Resource Manageru pro nasazení škálovací sady virtuálních počítačů

- **Azuredeploy.Parameters.JSON** je soubor parametrů, která ukládá informace, například jaké uživatelské jméno a heslo, které byste rádi nastavení pro váš virtuální počítač. Během nasazování šablony Resource Manageru používá parametry v tomto souboru. 

Stáhněte a uložte místně oba soubory. 

###  <a name="modify-azuredeployparametersjson"></a>Upravit azuredeploy.parameters.json
Otevřít *azuredeploy.parameters.json* souboru 

- Zadejte **vmSKU** byste chtěli nasadit (doporučujeme Standard_D2_v3) 
- Zadejte **windowsOSVersion** byste chtěli pro svou škálovací sadu virtuálních počítačů (doporučujeme 2016-Datacenter) 
- Název virtuálního počítače škálovací sady prostředků k nasazení pomocí **vmssName** vlastnost. Například *VMSS-WAD-TEST*.    
- Zadejte počet virtuálních počítačů, které byste rádi běžet na škálovací sady virtuálních počítačů **instanceCount** vlastnost
- Zadejte hodnoty pro **adminUsername** a **adminPassword** škálování virtuálního počítače nastavte. Tyto parametry se používají pro vzdálený přístup k virtuálním počítačům ve škálovací sadě. Tyto parametry se používají pro vzdálený přístup k virtuálnímu počítači. Abyste se vyhnuli nutnosti váš virtuální počítač zachycena nepoužívejte v této šabloně. Roboti prohledávání Internetu uživatelských jmen a hesel ve veřejných úložištích Github. Jsou pravděpodobně testovat virtuální počítače s tyto výchozí hodnoty. 


###  <a name="modify-azuredeployjson"></a>Upravit azuredeploy.json
Otevřít *azuredeploy.json* souboru 

Přidejte proměnnou pro uchování informací o účtu úložiště v šabloně Resource Manageru. Stále musíte zadat účet úložiště jako součást instalace diagnostického rozšíření. Protokoly a/nebo čítače výkonu zadaný v konfigurační soubor diagnostiky se zapisují do zadaný účet úložiště kromě odeslání do úložiště metrik Azure monitoru. 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
 ```
 
Definice Škálovací sady virtuálních počítačů najdete v části prostředky a přidejte v části "identity" ke konfiguraci. Tím se zajistí, že Azure ho přiřadí systému identit. Tento krok zajistí, že virtuální počítače ve škálovací sadě můžete vygenerovat metrik hosta o samotné do Azure monitoru.  

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


V **extensionProfile**, přidat nové rozšíření do šablony, jak je uvedeno **příponu VMSS WAD části**.  Tato část se spravovaným identitám pro rozšíření prostředků Azure, které zajistí, že probíhá emitovány metriky jsou přijaty Azure Monitor. **Název** pole může obsahovat libovolný název. 

Níže uvedený kód z rozšíření MSI také přidá rozšíření diagnostiky a konfiguraci jako prostředek rozšíření do prostředku virtuálního počítače škálovací sady. Můžete přidat nebo odebrat čítače výkonu podle potřeby. 

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


Přidáte dependsOn pro účet úložiště k zajištění, že se vytvoří ve správném pořadí. 
```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
 ```

Vytvoření účtu úložiště, pokud jeden není už v šabloně.  
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
> Musíte používat Azure Diagnostics rozšíření verze 1.5 nebo novější a mají "verzi autoUpgradeMinorVersion": vlastnost nastavena na hodnotu *true* v šabloně Resource Manageru.  Azure pak načte správné rozšíření při spuštění virtuálního počítače. Pokud nemáte tato nastavení v šabloně, je změnit a znovu nasaďte šablonu. 


Pokud chcete nasadit šablonu Resource Manageru, jsme bude využívat Azure Powershellu.  

1. Spuštění PowerShellu 
1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`
1. Získat seznam vašich předplatných pomocí `Get-AzureRmSubscription`
1. Nastavte předplatné, které vám bude možné vytváření nebo aktualizaci virtuálního počítače 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Vytvořte novou skupinu prostředků pro virtuální počítač, nasazení, spouštění následující příkaz 

   ```PowerShell
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Nezapomeňte použít oblast Azure, který je povolený pro vlastní metriky. Přečtěte si: 
 
1. Spuštěním následujících příkazů nasaďte virtuální počítač se  
   > [!NOTE] 
   > Pokud chcete aktualizovat existující škálovací sadě, jednoduše přidejte *-režim přírůstkového* na konec příkazu. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Po úspěšném nasazení byste měli najít virtuálního počítače škálovací sady na webu Azure Portal a by měl být generování metrik do Azure monitoru. 

   > [!NOTE] 
   > Pravděpodobně dojde k chybám kolem vybrané vmSkuSize. Pokud k tomu dojde, vraťte se do souboru azuredeploy.json a aktualizujte výchozí hodnotu parametru vmSkuSize. V takovém případě doporučujeme vyzkoušet "Standard_DS1_v2"). 


## <a name="chart-your-metrics"></a>Graf metrik 

1. Přihlášení k webu Azure Portal 

1. V nabídce vlevo klikněte na tlačítko **monitorování** 

1. Klikněte na stránce monitorování **metriky**. 

   ![Stránka metriky](./media/metrics-store-custom-rest-api/metrics.png) 

1. Na období agregace změnit **posledních 30 minut**.  

1. V prostředku rozevíracího seznamu vyberte škálovací sadu virtuálních počítačů, které jste právě vytvořili.  

1. V oborech názvů rozevíracího seznamu vyberte **azure.vm.windows.guest** 

1. Metriky rozevírací seznam, zaškrtněte **paměti\%používané svěřené bajty použití**.  

Potom můžete také pomocí dimenzí na tuto metriku do grafu tuto metriku pro konkrétní virtuální počítač ve škálovací sadě, nebo k vykreslení každého virtuálního počítače ve škálovací sadě. 



## <a name="next-steps"></a>Další postup
- Další informace o [vlastní metriky](metrics-custom-overview.md).

