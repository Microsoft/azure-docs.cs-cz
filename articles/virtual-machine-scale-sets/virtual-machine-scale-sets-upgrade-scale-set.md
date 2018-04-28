---
title: Upravit sadu škálování virtuálního počítače Azure | Microsoft Docs
description: Zjistěte, jak upravit a aktualizaci sad rozhraní REST API, Azure PowerShell a 2.0 rozhraní příkazového řádku Azure škálování virtuálního počítače Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: 662cea7ac47e411b127540faf5cab8b3c4d8964a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Upravit škálovací sadu virtuálních počítačů
V průběhu životního cyklu aplikací musíte upravit nebo aktualizovat vaše škálovací sadu virtuálních počítačů. Tyto aktualizace může zahrnovat jak aktualizovat konfiguraci sady škálování nebo změnit konfiguraci aplikace. Tento článek popisuje, jak upravit existující měřítku nastavit pomocí rozhraní REST API, prostředí Azure PowerShell nebo Azure CLI 2.0.

## <a name="fundamental-concepts"></a>základní koncepty

### <a name="the-scale-set-model"></a>Model sada škálování
Sada škálování má "škálování sadu model" shromažďuje *požadované* stav měřítka nastavený jako celek. Chcete-li prohledávat model pro sadu škálování, můžete použít 

- REST API s [výpočetní nebo virtualmachinescalesets nebo získat](/rest/api/compute/virtualmachinescalesets/get) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI 2.0 s [az vmss zobrazit](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo konkrétní jazyk [sady Azure SDK](https://azure.microsoft.com/downloads/).

Přesný prezentace výstupu závisí na možnosti, které poskytnete příkaz. Následující příklad ukazuje zhuštěné ukázkový výstup z Azure CLI 2.0:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
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
}
```

Tyto vlastnosti se vztahují ke stupnici nastavit jako celek.


### <a name="the-scale-set-instance-view"></a>Zobrazení instance škálovací sady
Nastavit také škálování má "škálování sadu instanci zobrazit" shromažďuje aktuální *runtime* stav měřítka nastavený jako celek. K zobrazení instance škálovací sady dotazu, můžete použít:

- REST API s [výpočetní/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI 2.0 s [az vmss get--zobrazení instance](/cli/azure/vmss#az_vmss_get_instance_view):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo konkrétní jazyk [SDK služby Azure](https://azure.microsoft.com/downloads/)

Přesný prezentace výstupu závisí na možnosti, které poskytnete příkaz. Následující příklad ukazuje zhuštěné ukázkový výstup z Azure CLI 2.0:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
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
}
```

Tyto vlastnosti zadejte souhrnné informace o aktuální stav modulu runtime v sadě škálování, jako je například stav rozšíření u škálovací sadu virtuálních počítačů.


### <a name="the-scale-set-vm-model-view"></a>Škálovací sady virtuálních počítačů modelu zobrazení
Podobně jako jak sadu škálování má modelu zobrazení, má každý virtuální počítač ve škálovací sadě vlastní zobrazení modelu. K dotazu na zobrazení model pro sadu škálování, můžete použít:

- REST API s [výpočetní nebo virtualmachinescalesetvms nebo získat](/rest/api/compute/virtualmachinescalesetvms/get) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI 2.0 s [az vmss zobrazit](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/).

Přesný prezentace výstupu závisí na možnosti, které poskytnete příkaz. Následující příklad ukazuje zhuštěné ukázkový výstup z Azure CLI 2.0:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Tyto vlastnosti popisují konfiguraci virtuální počítač, není konfigurace měřítka nastavit jako celek. Například model sada škálování má `overprovision` jako vlastnost, zatímco modelu pro virtuální počítač ve škálovací sadě neexistuje. Tento rozdíl je, protože předimenzování je vlastnost sad jako celé, ne jednotlivé virtuální počítače ve škálovací sadě škálování (Další informace o předimenzování najdete v tématu [aspekty návrhu pro sady škálování](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Měřítko nastavit zobrazení instance virtuálního počítače
Podobně jako jak sadu škálování má zobrazení instance, má každý virtuální počítač ve škálovací sadě vlastní zobrazení instance. K zobrazení instance škálovací sady dotazu, můžete použít:

- REST API s [výpočetní/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI 2.0 s [az vmss get instanci – zobrazení](/cli/azure/vmss#az_vmss_get_instance_view)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [SDK služby Azure](https://azure.microsoft.com/downloads/)

Přesný prezentace výstupu závisí na možnosti, které poskytnete příkaz. Následující příklad ukazuje zhuštěné ukázkový výstup z Azure CLI 2.0:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
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
}
```

Tyto vlastnosti popisují aktuální stav modulu runtime virtuálního počítače, obsahující libovolná rozšíření, použije k sadě škálování.


## <a name="how-to-update-global-scale-set-properties"></a>Postup aktualizace globálním měřítku nastavit vlastnosti
Chcete-li aktualizovat nastavte vlastnost globálním měřítku, je nutné aktualizovat vlastnost v modelu sady škálování. Tato aktualizace prostřednictvím můžete provést:

- REST API s [výpočetní/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) následujícím způsobem:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Můžete nasadit s vlastnostmi z rozhraní API REST k aktualizaci globálním měřítku umožňuje nastavit vlastnosti šablony Resource Manageru.

- Prostředí Azure PowerShell s [aktualizace AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss):

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI 2.0 s [aktualizovat az vmss](/cli/azure/vmss#az_vmss_update):
    - Jestliže chcete upravit:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Přidání objektu do seznamu vlastností v sadě škálování: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Chcete-li odebrat objekt ze seznamu vlastností v sadě škálování: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Pokud jste předtím nasadili pomocí sad škálování `az vmss create` příkazů, můžete spustit `az vmss create` příkaz znovu a aktualizovat sadu škálování. Ujistěte se, že všechny vlastnosti v `az vmss create` příkaz identická jako předtím, s výjimkou vlastnosti, které chcete upravit.

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/).

Jakmile dojde k aktualizaci modelu sady škálování, nová konfigurace platí pro všechny nové virtuální počítače vytvořené v sadě škálování. Ale modely pro stávající virtuální počítače ve škálovací sadě musí stále uvést do režimu aktuální nejnovější celkové škálování sadu modelu. Ve model pro každý virtuální počítač je vlastnost typu boolean s názvem `latestModelApplied` určující, zda virtuální počítač je aktuální nejnovější celkové škálování sadu modelu (`true` znamená virtuálního počítače je aktuální pomocí nejnovější modelu).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Zajištění aktuálnosti s modelem nejnovější sady škálování virtuálních počítačů
Sady škálování mít "upgradu zásady" určující, jak nabíhají aktuální s modelem nejnovější sady škálování virtuálních počítačů. Jsou tři režimy pro zásady upgradu:

- **Automatické** – v tomto režimu byly sadou škálování díky žádné záruky o pořadí se snížila virtuálních počítačů. Sada škálování může vypnout všechny virtuální počítače ve stejnou dobu. 
- **Vrácení** – v tomto režimu byly sadou škálování mezi listy zavede aktualizace v dávkách s volitelné pozastavení času.
- **Ruční** – v tomto režimu, když se aktualizace modelu sady škálování, nedojde k žádné do existujících virtuálních počítačů.
 
Pokud chcete aktualizovat existující virtuální počítače, je potřeba udělat "ruční upgrade" každý existující virtuální počítač. Můžete provést ruční upgrade pomocí:

- REST API s [výpočetní/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) následujícím způsobem:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [aktualizace AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance):
    
    ```powershell
    Update-AzureRmVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI 2.0 s [az vmss aktualizace instance](/cli/azure/vmss#az_vmss_update_instances)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Můžete také použít konkrétní jazyk [sady Azure SDK](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Clusterů Service Fabric lze použít pouze *automatické* režimu, ale aktualizace se proto liší. Další informace najdete v tématu [ upgradů aplikací Service Fabric](../service-fabric/service-fabric-application-upgrade.md).

Neexistuje jeden typ změny globálním měřítku umožňuje nastavit vlastnosti, která neodpovídá pravidlům pro zásady upgradu. Změny byly sadou škálování profil operačního systému (například uživatelské jméno správce a heslo) lze změnit pouze ve verzi rozhraní API *2017-12-01* nebo novější. Tyto změny se uplatní jenom na virtuální počítače vytvořené po změnu v hodnotě stupnice nastavit modelu. Chcete-li převést stávající virtuální počítače aktuální, musíte udělat "obnovení z Image" každý existujícího virtuálního počítače. Toto obnovení z Image prostřednictvím můžete provést:

- REST API s [obnovení z výpočetní/virtualmachinescalesets/image](/rest/api/compute/virtualmachinescalesets/reimage) následujícím způsobem:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Set-AzureRmVmssVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm):

    ```powershell
    Set-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI 2.0 s [az vmss obnovení z image](https://docs.microsoft.com/cli/azure/vmss#az_vmss_reimage):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít konkrétní jazyk [sady Azure SDK](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>Vlastnosti omezení pro úpravy

### <a name="create-time-properties"></a>Čas vytvoření vlastnosti
Některé vlastnosti lze nastavit pouze při vytváření sady škálování. Tyto vlastnosti:

- Zóny dostupnosti
- Vydavatel referenční bitové kopie
- Nabídka referenční bitové kopie
- Spravovaný typ účtu úložiště disku operačního systému

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Vlastnosti, které lze změnit pouze na základě aktuální hodnoty
S výjimkami v závislosti na aktuální hodnota může změnit některé vlastnosti. Tyto vlastnosti:

- **singlePlacementGroup** – Pokud singlePlacementGroup má hodnotu true, může být změněna na hodnotu false. Nicméně, pokud je nastavena hodnota false, singlePlacementGroup ho **nemusí** změnit na hodnotu true.
- **podsíť** – podsíť sady škálování může být změněno tak dlouho, dokud původní podsítě a nové podsítě jsou ve stejné virtuální síti.

### <a name="properties-that-require-deallocation-to-change"></a>Vlastnosti, které vyžadují navrácení změnit
Některé vlastnosti může být změněn na určité hodnoty pouze, pokud jsou virtuální počítače ve škálovací sadě navrácena. Tyto vlastnosti:

- **Název SKU**– Pokud SKU nového virtuálního počítače není podporován na hardwaru byly sadou škálování je aktuálně, budete muset zrušit přidělení virtuální počítače v sad před změnou názvu SKU škálování. Další informace najdete v tématu [změnou velikosti virtuálního počítače Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Aktualizace specifické pro virtuální počítač
Některé změny může použít pro konkrétní virtuální počítače místo nastavení vlastností globálním měřítku. V současné době pouze má připojení a odpojení datových disků do nebo z virtuálních počítačů v sadě škálování virtuálního počítače konkrétní aktualizace, která je podporována. Tato funkce je ve verzi preview. Další informace najdete v tématu [náhled dokumentaci](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scénáře

### <a name="application-updates"></a>Aktualizace aplikace
Pokud je aplikace nasazená na měřítko nastavit prostřednictvím rozšíření, aktualizaci konfigurace rozšíření způsobí, že aplikace aktualizovat v souladu s zásad upgradu. Například pokud máte novou verzi skript běžet v rozšíření vlastních skriptů, je může aktualizovat *fileUris* vlastnost tak, aby odkazoval na nový skript. V některých případech můžete chtít vynutit aktualizaci, i když konfigurace rozšíření je beze změny (například aktualizaci skript bez změny na identifikátor URI skriptu). V těchto případech můžete upravit *forceUpdateTag* chcete vynutit aktualizaci. Tato vlastnost nebude interpretovat platformy Azure. Pokud změníte hodnotu, neexistuje žádný vliv na způsob spuštění rozšíření. Ke změně jednoduše vynutí rozšíření znovu spustit. Další informace o *forceUpdateTag*, najdete v článku [dokumentace k REST API pro rozšíření](/rest/api/compute/virtualmachineextensions/createorupdate). Všimněte si, že *forceUpdateTag* lze použít s všechna rozšíření, ne jenom rozšíření vlastních skriptů.

Také je běžné pro aplikace pro nasazení pomocí vlastní image. Tento scénář je popsaná v následující části.

### <a name="os-updates"></a>Aktualizace operačního systému
Pokud používáte Image platformy Azure, můžete aktualizovat image změnou *elementu imageReference* (Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> S Image platformy je běžné zadejte "nejnovější" pro verzi referenční bitové kopie. Když vytvoříte, škálovat a obnovení z Image, virtuální počítače jsou vytvořeny pomocí na nejnovější dostupnou verzi. Ale ho **nemá** znamená, že bitovou kopii operačního systému se automaticky aktualizuje časem jako jsou vydávány nové verze bitové kopie. Samostatné funkce je aktuálně ve verzi preview, která poskytuje automatické upgrady operačního systému. Další informace najdete v tématu [automatické upgrady operačního systému dokumentaci](virtual-machine-scale-sets-automatic-upgrade.md).

Pokud používáte vlastní Image, image můžete aktualizovat aktualizací *elementu imageReference* ID (Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Příklady

### <a name="update-the-os-image-for-your-scale-set"></a>Aktualizovat škálovací sadu pro bitovou kopii operačního systému
Můžete mít sada škálování, které je spuštěna stará verze Ubuntu LTS 16.04. Chcete aktualizovat na novější verzi Ubuntu LTS 16.04, jako je například verze *16.04.201801090*. Vlastnost verze referenční bitové kopie není v seznamu, tak můžete přímo upravit tyto vlastnosti s jednou z následujících příkazů:

- Prostředí Azure PowerShell s [aktualizace AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) následujícím způsobem:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI 2.0 s [aktualizovat az vmss](/cli/azure/vmss#az_vmss_update_instances):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Aktualizace nástroje pro vyrovnávání zatížení pro škálovací sadu
Řekněme, že máte škálování nastavit s nástrojem pro vyrovnávání zatížení Azure a budete chtít nahradit nástroje pro vyrovnávání zatížení Azure pomocí služby Azure Application Gateway. Nástroj pro vyrovnávání zatížení a vlastnosti aplikační brány pro sadu škálování jsou v seznamu, abyste mohli používat příkazy pro odeberte nebo přidejte seznam elementů místo přímém upravování vlastnosti:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the referencerence to the application gateway
    $ipconf = New-AzureRmVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI 2.0:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Těchto příkazů se předpokládá, že je pouze jeden vyrovnávání IP konfigurace a zatížení na škálovací sadu. Pokud existuje více budete muset použít jiné než index seznamu *0*.


## <a name="next-steps"></a>Další postup
Můžete také provádět běžné úlohy správy na sady škálování s [Azure CLI 2.0](virtual-machine-scale-sets-manage-cli.md) nebo [prostředí Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
