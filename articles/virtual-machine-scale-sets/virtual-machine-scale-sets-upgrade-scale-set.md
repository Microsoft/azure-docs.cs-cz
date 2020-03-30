---
title: Úprava škálovací sady virtuálních strojů Azure
description: Zjistěte, jak upravit a aktualizovat škálovací sadu virtuálních strojů Azure pomocí api REST, Azure PowerShellu a Azure CLI
author: mimckitt
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimckitt
ms.openlocfilehash: 66fd656b5175547641150a048e57c978dc06d291
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476820"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Úprava škálovací sady virtuálních počítačů

Po celou dobu životnosti aplikací může být nutné upravit nebo aktualizovat škálovací sadu virtuálních strojů. Tyto aktualizace mohou zahrnovat, jak aktualizovat konfiguraci škálovací sady nebo změnit konfiguraci aplikace. Tento článek popisuje, jak upravit existující škálovací sadu pomocí REST API, Azure PowerShell nebo Azure CLI.

## <a name="fundamental-concepts"></a>Základní koncepce

### <a name="the-scale-set-model"></a>Model škálovací sady
Škálovací sada má "model škálovací sady", který zachycuje *požadovaný* stav škálovací sady jako celku. Chcete-li zadat dotaz na model pro škálovací sadu, můžete použít 

- REST API s [výpočetními/virtualmachinescalesets/získejte](/rest/api/compute/virtualmachinescalesets/get) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell s [get-azvmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI s [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo sady [Azure SDK](https://azure.microsoft.com/downloads/)specifické pro jazyk .

Přesná prezentace výstupu závisí na možnostech, které zadáte příkazu. Následující příklad ukazuje zkrácený ukázkový výstup z azure CLI:

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

Tyto vlastnosti platí pro škálovací sadu jako celek.


### <a name="the-scale-set-instance-view"></a>Zobrazení instance škálovací sady
Škálovací sada má také "zobrazení instance škálovací sady", které zachycuje aktuální stav *běhu* škálovací sady jako celku. Chcete-li zadat dotaz na zobrazení instance pro škálovací sadu, můžete použít:

- ROZHRANÍ REST API s [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) takto:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell s [get-azvmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI s [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo sady [Azure SDK](https://azure.microsoft.com/downloads/) specifické pro daný jazyk.

Přesná prezentace výstupu závisí na možnostech, které zadáte příkazu. Následující příklad ukazuje zkrácený ukázkový výstup z azure CLI:

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

Tyto vlastnosti poskytují souhrn aktuálního stavu runtime virtuálních počítače ve škálovací sadě, jako je například stav rozšíření použitých pro škálovací sadu.


### <a name="the-scale-set-vm-model-view"></a>Zobrazení modelu škálovací sady virtuálních můek
Podobně jako má škálovací sada pohled modelu, každá instance virtuálního virtuálního virtuálního virtuálního virtuálního serveru v škálovací sadě má svůj vlastní pohled modelu. Chcete-li zadat dotaz na zobrazení modelu pro konkrétní instanci virtuálního aplikace ve škálovací sadě, můžete použít:

- REST API s [výpočetními/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell s [get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI s [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/).

Přesná prezentace výstupu závisí na možnostech, které zadáte příkazu. Následující příklad ukazuje zkrácený ukázkový výstup z azure CLI:

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

Tyto vlastnosti popisují konfiguraci instance virtuálního počítače v rámci škálovací sady, nikoli konfiguraci škálovací sady jako celku. Například model škálovací `overprovision` sady má jako vlastnost, zatímco model pro instanci virtuálního zařízení v rámci škálovací sady nemá. Tento rozdíl je, protože overprovisioning je vlastnost pro škálovací sadu jako celek, nikoli jednotlivé instance virtuálních zařízení v škálovací sadě (další informace o nadměrném zřizování, viz [Návrh aspekty pro škálovací sady](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Zobrazení instance škálovací sady virtuálních můek
Podobně jako má škálovací sada zobrazení instance, každá instance virtuálního aplikace v škálovací sadě má své vlastní zobrazení instance. Chcete-li zadat dotaz na zobrazení instance pro konkrétní instanci virtuálního aplikace v rámci škálovací sady, můžete použít:

- ROZHRANÍ REST API s [výpočetními/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell s [get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI s [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/)

Přesná prezentace výstupu závisí na možnostech, které zadáte příkazu. Následující příklad ukazuje zkrácený ukázkový výstup z azure CLI:

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

Tyto vlastnosti popisují aktuální runtime stav instance virtuálního počítače v rámci škálovací sady, která zahrnuje všechna rozšíření použitá pro škálovací sadu.


## <a name="how-to-update-global-scale-set-properties"></a>Jak aktualizovat vlastnosti globální škálovací sady
Chcete-li aktualizovat vlastnost globální škálovací sady, musíte aktualizovat vlastnost v modelu škálovací sady. Tuto aktualizaci můžete provést prostřednictvím:

- ROZHRANÍ REST API s [výpočetními/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) následujícím způsobem:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Můžete nasadit šablonu Správce prostředků s vlastnostmi z rozhraní REST API pro aktualizaci vlastností globální škálovací sady.

- Azure PowerShell s [aktualizací AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI s [aktualizací az vmss](/cli/azure/vmss):
    - Chcete-li upravit vlastnost:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Přidání objektu do vlastnosti seznamu v škálovací sadě: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Odebrání objektu z vlastnosti seznamu v škálovací sadě: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Pokud jste dříve nasadili škálovací sadu pomocí `az vmss create` `az vmss create` příkazu, můžete příkaz spustit znovu a aktualizovat škálovací sadu. Ujistěte se, že `az vmss create` všechny vlastnosti v příkazu jsou stejné jako dříve, s výjimkou vlastností, které chcete upravit.

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/).

Po aktualizaci modelu škálovací sady se nová konfigurace vztahuje na všechny nové virtuální počítače vytvořené ve škálovací sadě. Modely pro existující virtuální chody ve škálovací sadě však musí být stále aktualizovány nejnovějším modelem celkové škálovací sady. V modelu pro každý virtuální virtuální ms je logická vlastnost s názvem, `latestModelApplied` která označuje, zda je`true` virtuální ms aktuální s nejnovějším modelem celkové škálovací sady (znamená, že virtuální hod je aktuální s nejnovějším modelem).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Jak aktualizovat virtuální virtuální soudy s nejnovějším modelem škálovací sady
Škálovací sady mají "zásady upgradu", které určují, jak jsou virtuální mích aktualizovány s nejnovějším modelem škálovací sady. Tři režimy pro zásady upgradu jsou:

- **Automatické** – v tomto režimu škálovací sada neposkytuje žádné záruky o pořadí virtuálních zařízení, které jsou svrženy. Škálovací sada může sundat všechny virtuální ho disponál současně. 
- **Rolling** – V tomto režimu škálovací sada rozbalí aktualizaci v dávkách s volitelným časem pozastavení mezi dávkami.
- **Ruční** – v tomto režimu při aktualizaci modelu škálovací sady se s existujícími virtuálními zařízeními nic nestane.
 
Chcete-li aktualizovat existující virtuální chod, musíte provést "ruční upgrade" každého existujícího virtuálního zařízení. Tento ruční upgrade můžete provést pomocí:

- ROZHRANÍ REST API s [výpočetními/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) následujícím způsobem:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell s [aktualizací AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI s [instancemi aktualizací az vmss](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Můžete také použít sady [Azure SDK](https://azure.microsoft.com/downloads/)specifické pro jazyk .

>[!NOTE]
> Clustery Service Fabric mohou používat pouze *automatický* režim, ale aktualizace je zpracována odlišně. Další informace naleznete v tématu [Service Fabric inovace aplikací](../service-fabric/service-fabric-application-upgrade.md).

Existuje jeden typ změny vlastností globální škálovací sady, který nedodržuje zásady upgradu. Změny v škálovací sadě profilu operačního systému a datového disku (například uživatelské jméno správce a heslo) lze změnit pouze v rozhraní API verze *2017-12-01* nebo novější. Tyto změny platí jenom pro virtuální chody vytvořené po změně modelu škálovací sady. Chcete-li aktualizovat existující virtuální hody, musíte provést "reimage" každého existujícího virtuálního jevu. Můžete to udělat reimage přes:

- ROZHRANÍ REST API s [výpočetními/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage) následujícím způsobem:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell se [sadou AzVmssVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI s [az vmss reimage](https://docs.microsoft.com/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít sady [Azure SDK](https://azure.microsoft.com/downloads/)specifické pro jazyk .


## <a name="properties-with-restrictions-on-modification"></a>Vlastnosti s omezeními modifikace

### <a name="create-time-properties"></a>Vlastnosti vytvořit čas
Některé vlastnosti lze nastavit pouze při vytváření škálovací sady. Mezi tyto vlastnosti patří:

- Zóny dostupnosti
- Vydavatel odkazu na obrázek
- Nabídka odkazu na obrázek
- Odkaz na obrázek sku
- Typ účtu spravovaného úložiště disku operačního systému

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Vlastnosti, které lze změnit pouze na základě aktuální hodnoty
Některé vlastnosti mohou být změněny, s výjimkami v závislosti na aktuální hodnotě. Mezi tyto vlastnosti patří:

- **singlePlacementGroup** - Pokud singlePlacementGroup je true, může být upravena na false. Však pokud singlePlacementGroup je false, **nemusí** být změněn na true.
- **podsíť** – podsíť škálovací sady může být změněna, pokud jsou původní podsíť a nová podsíť ve stejné virtuální síti.

### <a name="properties-that-require-deallocation-to-change"></a>Vlastnosti, které vyžadují přemístění k změně
Některé vlastnosti lze změnit pouze na určité hodnoty, pokud virtuální počítače ve škálovací sadě jsou přiděleny. Mezi tyto vlastnosti patří:

- **Název skladové položky**– pokud nová skladová položka virtuálního počítače není podporována na hardwaru, na který je aktuálně škálovací sada, musíte před úpravou názvu skladové položky navrátit virtuální masy ve škálovací sadě. Další informace najdete v [tématu, jak změnit velikost virtuálního počítače Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Aktualizace specifické pro virtuální montovana
Některé změny lze použít na konkrétní virtuální počítače namísto vlastnosti globální škálovací sady. V současné době je jedinou aktualizací specifickou pro virtuální počítače, která je podporovaná, připojení/odpojení datových disků k virtuálním počítačům nebo z nich ve škálovací sadě. Tato funkce je ve verzi Preview. Další informace naleznete v [dokumentaci k náhledu](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scénáře

### <a name="application-updates"></a>Aktualizace aplikací
Pokud je aplikace nasazena do škálovací sady prostřednictvím rozšíření, aktualizace konfigurace rozšíření způsobí, že se aplikace aktualizuje v souladu se zásadami upgradu. Například pokud máte novou verzi skriptu ke spuštění v rozšíření vlastního skriptu, můžete aktualizovat *vlastnost fileUris* tak, aby ukazovala na nový skript. V některých případech můžete chtít vynutit aktualizaci, i když konfigurace rozšíření je beze změny (například jste aktualizovali skript bez změny uri skriptu). V těchto případech můžete upravit *forceUpdateTag* vynutit aktualizaci. Platforma Azure tuto vlastnost neinterpretuje. Pokud změníte hodnotu, neexistuje žádný vliv na způsob spuštění rozšíření. Změna jednoduše vynutí prodloužení znovu spustit. Další informace o *forceUpdateTag*naleznete v [dokumentaci rozhraní REST API pro rozšíření](/rest/api/compute/virtualmachineextensions/createorupdate). Všimněte si, že *forceUpdateTag* lze použít se všemi rozšířeními, nikoli pouze s rozšířením vlastního skriptu.

Je také běžné, že aplikace se nasazují prostřednictvím vlastní image. Tento scénář je popsán v následující části.

### <a name="os-updates"></a>Aktualizace operačního systému
Pokud používáte image platformy Azure, můžete aktualizovat image úpravou *imageReference* (další informace naleznete v [dokumentaci k rozhraní REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> S image platformy, je běžné zadat "nejnovější" pro verzi odkazu na obrázek. Když vytvoříte, horizontální navýšení kapacity a reimage, virtuální chody jsou vytvořeny s nejnovější dostupnou verzí. Neznamená **to** však, že bitová kopie operačního systému je automaticky aktualizována v průběhu času, protože jsou vydány nové verze bitových obrázků. Samostatná funkce je aktuálně ve verzi preview, která poskytuje automatické upgrady operačního systému. Další informace naleznete v [dokumentaci k automatickým inovacím operačního systému](virtual-machine-scale-sets-automatic-upgrade.md).

Pokud používáte vlastní image, můžete aktualizovat image aktualizací *id odkazu na obrázek* (další informace naleznete v [dokumentaci k rozhraní REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Příklady

### <a name="update-the-os-image-for-your-scale-set"></a>Aktualizace bitové kopie operačního systému pro škálovací sadu
Můžete mít škálovací sadu, která spouští starou verzi Ubuntu LTS 16.04. Chcete aktualizovat na novější verzi Ubuntu LTS 16.04, například verze *16.04.201801090*. Vlastnost referenční verze obrázku není součástí seznamu, takže můžete tyto vlastnosti přímo upravit jedním z následujících příkazů:

- Azure PowerShell s [aktualizací AzVmss](/powershell/module/az.compute/update-azvmss) takto:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI s [aktualizací az vmss](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Případně můžete změnit obrázek, který vaše škálovací sada používá. Můžete například aktualizovat nebo změnit vlastní obrázek používaný škálovací sadou. Obrázek, který vaše škálovací sada používá, můžete změnit aktualizací vlastnosti ID odkazu na obrázek. Vlastnost ID odkazu na obrázek není součástí seznamu, takže tuto vlastnost můžete přímo upravit jedním z následujících příkazů:

- Azure PowerShell s [aktualizací AzVmss](/powershell/module/az.compute/update-azvmss) takto:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI s [aktualizací az vmss](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Aktualizace systému vyrovnávání zatížení pro váhovou sadu
Řekněme, že máte škálovací sadu s Azure Balancer a chcete nahradit Azure Balancer s Azure aplikační brány. Vlastnosti pro vyrovnávání zatížení a aplikační brána pro škálovací sadu jsou součástí seznamu, takže pomocí příkazů můžete odebrat nebo přidat prvky seznamu namísto přímé úpravy vlastností:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Cli Azure:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Tyto příkazy předpokládají, že na škálovací sadě existuje pouze jedna konfigurace IP adresy a nástroj pro vyrovnávání zatížení. Pokud existuje více, bude pravděpodobně nutné použít index seznamu jiný než *0*.


## <a name="next-steps"></a>Další kroky
Můžete také provádět běžné úlohy správy na škálovacích sadách pomocí [Azure CLI](virtual-machine-scale-sets-manage-cli.md) nebo [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
