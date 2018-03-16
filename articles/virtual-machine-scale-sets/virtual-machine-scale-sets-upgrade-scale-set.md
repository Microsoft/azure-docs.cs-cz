---
title: "Upravit sadu škálování virtuálního počítače Azure | Microsoft Docs"
description: "Upravit sadu škálování virtuálního počítače Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: fcca912a8120a51d2f0a454ef0a6341cd5882015
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Upravit škálovací sadu virtuálních počítačů
Tento článek popisuje, jak upravit existující sady škálování virtuálního počítače. Úkoly patří změna konfigurace měřítka nastavit, jak změnit konfiguraci aplikací běžících na rozsahu sady, Správa dostupnosti a další.

## <a name="fundamental-concepts"></a>základní koncepty

### <a name="scale-set-model"></a>Sady škálování modelu

Sada škálování má model, který zachycuje *požadované* stav měřítka nastavený jako celek. K dotazování modelů pro sadu škálování, můžete použít:

* ROZHRANÍ REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
   
  Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get).

* PowerShell:

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`
   
  Další informace najdete v tématu [prostředí PowerShell dokumentaci](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Další informace najdete v tématu [dokumentaci k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Můžete také použít [Průzkumníka prostředků Azure (Preview)](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/) k dotazování modelů pro sadu škálování.

Přesný prezentace výstupu závisí na možnosti, které zadáte do příkazu. Tady je ukázkový výstup z příkazového řádku Azure:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Jak můžete vidět, tyto vlastnosti se vztahují ke stupnici nastavit jako celek.



### <a name="scale-set-instance-view"></a>Zobrazení instance škálovací sady

Nastavit také škálování má zobrazení instance, který zachycuje aktuální *runtime* stav měřítka nastavený jako celek. K zobrazení instance škálovací sady dotazu, můžete použít:

* ROZHRANÍ REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` 
   
  Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` 
  
  Další informace najdete v tématu [prostředí PowerShell dokumentaci](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Další informace najdete v tématu [dokumentaci k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Můžete také použít [Průzkumníka prostředků Azure (Preview)](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/) k dotazu na zobrazení instance škálovací sady.

Přesný prezentace výstupu závisí na možnosti, které zadáte do příkazu. Tady je ukázkový výstup z příkazového řádku Azure:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
  .
  .
  .
}
```

Jak vidíte, zadejte tyto vlastnosti souhrnné informace o aktuální stav modulu runtime v sadě škálování virtuálních počítačů. Souhrn obsahuje stav rozšíření u sad (není uveden jako stručný výtah) škálování.



### <a name="scale-set-vm-model-view"></a>Sady škálování virtuálního počítače modelu zobrazení

Podobně jako jak sadu škálování má modelu zobrazení, má každý virtuální počítač ve škálovací sadě vlastní zobrazení modelu. K dotazu na zobrazení model pro sadu škálování, můžete použít:

* ROZHRANÍ REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` 
  
  Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
  
  Další informace najdete v tématu [prostředí PowerShell dokumentaci](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Další informace najdete v tématu [dokumentaci k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Můžete také použít [Průzkumníka prostředků Azure (Preview)](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/) k dotazování modelu pro virtuální počítač ve škálovací sadě.

Přesný prezentace výstupu závisí na možnosti, které zadáte do příkazu. Tady je ukázkový výstup z příkazového řádku Azure:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Jak vidíte, popisují tyto vlastnosti konfigurace virtuálního počítače, není konfigurace měřítka nastavit jako celek. Například model sada škálování má `overprovision` jako vlastnost, že nastavení modelu pro virtuální počítač v škálování nemá. Důvody, proč tento rozdíl je, že předimenzování je vlastnost sad jako celé, ne jednotlivé virtuální počítače ve škálovací sadě škálování. (Další informace o předimenzování najdete v tématu [aspekty návrhu pro sady škálování](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning).)



### <a name="scale-set-vm-instance-view"></a>Zobrazení instance virtuálního počítače sady škálování

Podobně jako jak sadu škálování má zobrazení instance, má každý virtuální počítač ve škálovací sadě vlastní zobrazení instance. K zobrazení instance škálovací sady dotazu, můžete použít:

* ROZHRANÍ REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` 
 
  Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` 
  
  Další informace najdete v tématu [prostředí PowerShell dokumentaci](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Další informace najdete v tématu [dokumentaci k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Můžete také použít [Průzkumníka prostředků Azure (Preview)](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/) k dotazu na zobrazení instance virtuálního počítače ve škálovací sadě.

Přesný prezentace výstupu závisí na možnosti, které zadáte do příkazu. Tady je ukázkový výstup z příkazového řádku Azure:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
  .
  .
  .
}
```

Jak vidíte, tyto vlastnosti popisují aktuální stav modulu runtime virtuální počítač. Stav zahrnuje všechna rozšíření u sad (není uveden jako stručný výtah) škálování.




## <a name="techniques-for-updating-global-scale-set-properties"></a>Techniky aktualizace globálním měřítku pro nastavení vlastností

Chcete-li aktualizovat nastavte vlastnost globálním měřítku, je nutné aktualizovat vlastnost v modelu sady škálování. Tato aktualizace prostřednictvím můžete provést:

* ROZHRANÍ REST API: 

  `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
  
  Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

  Případně můžete nasadit šablonu Azure Resource Manager pomocí vlastnosti z rozhraní API REST aktualizovat vlastnosti sady globálním měřítku.

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` 
  
  Další informace najdete v tématu [prostředí PowerShell dokumentaci](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss).

* Azure CLI:

  * Jestliže chcete upravit: `az vmss update --set {propertyPath}={value}` 
  
  * Přidání objektu do seznamu vlastností v sadě škálování: `az vmss update --add {propertyPath} {JSONObjectToAdd}` 
  
  * Chcete-li odebrat objekt ze seznamu vlastností v sadě škálování: `az vmss update --remove {propertyPath} {indexToRemove}` 
  
  Další informace najdete v tématu [dokumentaci k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update). 
  
  Případně pokud jste předtím nasadili pomocí sad škálování `az vmss create` příkazů, můžete spustit `az vmss create` příkaz znovu a aktualizovat sadu škálování. Chcete-li to provést, zajistěte, aby všechny vlastnosti v `az vmss create` příkaz identická jako předtím, s výjimkou vlastnosti, které chcete upravit.



Můžete také použít [Průzkumníka prostředků Azure (Preview)](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/) aktualizovat měřítka nastavit modelu.

Po aktualizaci modelu sady škálování nové konfigurace platí pro všechny nové virtuální počítače vytvořené v sadě škálování. Ale modely pro stávající virtuální počítače ve škálovací sadě musí stále uvést do režimu aktuální nejnovější celkové škálování sadu modelu. Ve model pro každý virtuální počítač, vlastnost typu Boolean s názvem `latestModelApplied` označuje, zda virtuální počítač je aktuální nejnovější celkové škálování sadu modelu. (Hodnota `true` znamená virtuálního počítače je aktuální. nejnovější modelu.)




## <a name="techniques-for-bringing-vms-up-to-date-with-the-latest-scale-set-model"></a>Techniky pro převedení aktuální s modelem nejnovější sady škálování virtuálních počítačů

Sady škálování mít *zásad upgradu* který určuje, jak nabíhají aktuální s modelem nejnovější sady škálování virtuálních počítačů. Jsou tři režimy pro zásady upgradu:

- **Automatické**: V tomto režimu díky sadě škálování žádné záruky o pořadí virtuálních počítačů, které jsou snížila. Sada škálování může vypnout všechny virtuální počítače ve stejnou dobu. 
- **Vrácení**: V tomto režimu byly sadou škálování zavede aktualizace v dávkách, volitelné pozastavení doba mezi dávky.
- **Ruční**: V tomto režimu při aktualizaci modelu sady škálování, nedojde k žádné akci do existujících virtuálních počítačů. Chcete-li aktualizovat stávající virtuální počítače, je nutné ručně upgradovat každé z nich. Můžete provést ruční upgrade prostřednictvím:

  - ROZHRANÍ REST API: 
  
    `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` 
    
    Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances).

  - PowerShell: 
  
    `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
    
    Další informace najdete v tématu [prostředí PowerShell dokumentaci](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

  - Azure CLI: 
  
    `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` 
    
    Další informace najdete v tématu [dokumentaci k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances).

  Můžete také [sady Azure SDK](https://azure.microsoft.com/downloads/) ručně upgradovat virtuální počítač ve škálovací sadě.

>[!NOTE]
> Azure Service Fabric clustery můžete použít pouze automatické režim, ale aktualizace se proto liší. Další informace o aktualizacích Service Fabric najdete v tématu [Service Fabric dokumentaci](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

Jeden typ změny globálním měřítku umožňuje nastavit vlastnosti nedodrží zásady upgradu: změny měřítka nastavit profil operačního systému. (Příklady jsou jméno a heslo správce). Tyto vlastnosti lze změnit pouze ve verzi rozhraní API 2017-12-01 nebo novější. Tyto změny platí pouze pro virtuální počítače vytvořené po změnu v hodnotě stupnice nastavit modelu. Aby aktuální existujících virtuálních počítačů musí obnovit z Image každý existující virtuální počítač. Obnovení z Image virtuálního počítače prostřednictvím:

* ROZHRANÍ REST API: 

  `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` 
  
  Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage).

* PowerShell: 

  `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` 
  
  Další informace najdete v tématu [prostředí PowerShell dokumentaci](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm).

* Azure CLI: 

  `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` 
  
  Další informace najdete v tématu [dokumentaci k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage).

Můžete také [sady Azure SDK](https://azure.microsoft.com/downloads/) Image virtuálního počítače ve škálovací sadě.




## <a name="properties-with-restrictions-on-modification"></a>Vlastnosti omezení pro úpravy

### <a name="create-time-properties"></a>Čas vytvoření vlastnosti

Některé vlastnosti lze nastavit pouze v případě, že vytváříte původně byly sadou škálování. Tyto vlastnosti:

- Zóny
- Vydavatel referenční bitové kopie
- Nabídka referenční bitové kopie

### <a name="properties-that-can-be-changed-based-on-the-current-value-only"></a>Vlastnosti, které mohou být změněny na základě aktuální hodnoty

Některé vlastnosti lze změnit s výjimkami, v závislosti na aktuální hodnotu. Tyto vlastnosti:

- `singlePlacementGroup`: Pokud `singlePlacementGroup` má hodnotu true, je možné ji upravit na hodnotu false. Ale pokud `singlePlacementGroup` je nastavena hodnota false, ho *nelze* změnit na hodnotu true.
- `subnet`: Podsíť sady škálování se dá změnit, pokud jsou původní podsíť a nové podsítě ve stejné virtuální síti.

### <a name="properties-that-require-deallocation-to-change"></a>Vlastnosti, které vyžadují navrácení změnit

Některé vlastnosti můžete změnit na konkrétní hodnoty pouze v případě, že jsou virtuální počítače ve škálovací sadě navrácena. Tyto vlastnosti:

- `sku name`: Pokud SKU nového virtuálního počítače není podporován na hardwaru, které byly sadou škálování je momentálně v, budete muset zrušit přidělení virtuální počítače v sad před změnou škálování `sku name`. Další informace o změně velikosti virtuálních počítačů najdete v tématu [tento příspěvek blogu Azure](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>Aktualizace specifické pro virtuální počítač

Některé změny je použít pro konkrétní virtuální počítače místo nastavení vlastností globálním měřítku. V současné době pouze aktualizace specifické pro virtuální počítač, která je podporována je připojování nebo odpojování datových disků do nebo z virtuálních počítačů v sadě škálování. Tato funkce je ve verzi preview. Další informace najdete v tématu [náhled dokumentaci](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios"></a>Scénáře

### <a name="application-updates"></a>Aktualizace aplikace

Pokud je aplikace nasazená na měřítko nastavit prostřednictvím rozšíření, aktualizuje se konfigurace rozšíření způsobí, že aplikace aktualizují podle zásad upgradu. Například pokud máte novou verzi skript běžet v rozšíření vlastních skriptů, je může aktualizovat `fileUris` vlastnost tak, aby odkazoval na nový skript. 

V některých případech můžete chtít vynutit aktualizaci, i když konfigurace rozšíření je beze změny. (Například můžete aktualizovat skript beze změny identifikátor URI skriptu.) V těchto případech můžete upravit `forceUpdateTag` chcete vynutit aktualizaci. Platformy Azure nebude interpretovat tuto vlastnost tak jeho změna nemá vliv na způsob spuštění rozšíření. Úprava ho jednoduše vynutí rozšíření znovu spustit. 

Další informace o `forceUpdateTag`, najdete v článku [dokumentace k REST API pro rozšíření](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Také je běžné pro aplikace pro nasazení pomocí vlastní image. Tento scénář je popsaná v následující části.

### <a name="os-updates"></a>Aktualizace operačního systému

Pokud používáte Image platformy, můžete aktualizovat Image úpravou `imageReference`. Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

>[!NOTE]
> S Image platformy je běžné zadejte "nejnovější" pro verzi referenční bitové kopie. To znamená, že při vytváření sad škálování škálovaný a přeinstalovanou, virtuální počítače jsou vytvořeny s na nejnovější dostupnou verzi. Ale ho *nemá* znamená, že bitovou kopii operačního systému bude automaticky aktualizován časem jako jsou vydávány nové verze bitové kopie. Jedná se o samostatnou funkci, momentálně ve verzi preview. Další informace najdete v tématu [automatické operační systém upgraduje](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Pokud používáte vlastní Image, Image můžete aktualizovat aktualizací `imageReference` ID. Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="examples"></a>Příklady

### <a name="update-the-os-image-for-your-scale-set"></a>Aktualizovat škálovací sadu pro bitovou kopii operačního systému

Řekněme, že máte nastavit spuštěna stará verze Ubuntu LTS 16.04 škálování. Chcete-li aktualizovat na novější verzi 16.04 LTS Ubuntu (například verze 16.04.201801090). Vlastnost verze referenční bitové kopie není součástí seznamu, takže tyto vlastnosti můžete upravovat přímo pomocí těchto příkazů:

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

* Azure CLI: 

  `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="update-the-load-balancer-for-your-scale-set"></a>Aktualizace nástroje pro vyrovnávání zatížení pro škálovací sadu

Řekněme, že máte škálování nastavit s nástrojem pro vyrovnávání zatížení Azure a budete chtít nahradit nástroje pro vyrovnávání zatížení pomocí služby Azure application gateway. Vlastnosti zatížení vyrovnávání a aplikace brány pro sadu škálování jsou v seznamu. Ano můžete použít příkazy pro odebrání a přidávání seznamu elementů místo přímém upravování vlastnosti.

PowerShell:
```
# Get the current model of the scale set and store it in a local PowerShell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure load balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local PowerShell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

Azure CLI:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # Remove the load balancer back-end pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # Remove the load balancer back-end pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # Add the application gateway back-end pool to the scale set model
```

>[!NOTE]
> Těchto příkazů se předpokládá, že je pouze jeden vyrovnávání IP konfigurace a zatížení na škálovací sadu. Pokud existuje více možná budete muset použít index seznamů než 0.