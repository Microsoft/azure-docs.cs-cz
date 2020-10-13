---
title: Úprava sady škálování virtuálních počítačů Azure
description: Naučte se, jak upravit a aktualizovat sadu škálování virtuálního počítače Azure pomocí rozhraní REST API, Azure PowerShell a Azure CLI.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/10/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f7a61ed039a3d8ed643e3b1b3d79384e35847986
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87029293"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Úprava škálovací sady virtuálních počítačů

V průběhu životního cyklu vašich aplikací možná budete muset změnit nebo aktualizovat sadu škálování virtuálního počítače. Tyto aktualizace mohou zahrnovat způsob aktualizace konfigurace sady škálování nebo změnu konfigurace aplikace. Tento článek popisuje, jak upravit existující sadu škálování pomocí rozhraní REST API, Azure PowerShell nebo Azure CLI.

## <a name="fundamental-concepts"></a>Základní koncepce

### <a name="the-scale-set-model"></a>Model sady škálování
Sada škálování má model "Scale set", který zachycuje *požadovaný* stav sady škálování jako celku. Pro dotazování modelu pro sadu škálování můžete použít 

- REST API pomocí [COMPUTE/virtualmachinescalesets/Get](/rest/api/compute/virtualmachinescalesets/get) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell s [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI pomocí [AZ VMSS show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Můžete také použít [Resources.Azure.com](https://resources.azure.com) nebo sady [Azure SDK](https://azure.microsoft.com/downloads/)pro konkrétní jazyk.

Přesnější prezentace výstupu závisí na možnostech, které zadáte do příkazu. Následující příklad ukazuje zhuštěný ukázkový výstup z Azure CLI:

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

Tyto vlastnosti se vztahují na sadu škálování jako celek.


### <a name="the-scale-set-instance-view"></a>Zobrazení instance sady škálování
Sada škálování má také "zobrazení instance sady škálování", které zachycuje aktuální *běhový* stav sady škálování jako celku. Pro dotazování zobrazení instance pro sadu škálování můžete použít:

- REST API pomocí [COMPUTE/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell s [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI pomocí [AZ VMSS Get-instance-View](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Můžete také použít [Resources.Azure.com](https://resources.azure.com) nebo sady [Azure SDK](https://azure.microsoft.com/downloads/) pro konkrétní jazyk.

Přesnější prezentace výstupu závisí na možnostech, které zadáte do příkazu. Následující příklad ukazuje zhuštěný ukázkový výstup z Azure CLI:

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

Tyto vlastnosti poskytují souhrn aktuálního běhového stavu virtuálních počítačů v sadě škálování, například stav rozšíření použitých pro sadu škálování.


### <a name="the-scale-set-vm-model-view"></a>Zobrazení modelu virtuálního počítače sady škálování
Podobně jako sada škálování má zobrazení modelu, každá instance virtuálního počítače v sadě škálování má vlastní zobrazení modelu. K dotazování zobrazení modelu pro konkrétní instanci virtuálního počítače v sadě škálování můžete použít:

- REST API pomocí [COMPUTE/virtualmachinescalesetvms/Get](/rest/api/compute/virtualmachinescalesetvms/get) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell s [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI pomocí [AZ VMSS show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít [Resources.Azure.com](https://resources.azure.com) nebo sady [SDK Azure](https://azure.microsoft.com/downloads/).

Přesnější prezentace výstupu závisí na možnostech, které zadáte do příkazu. Následující příklad ukazuje zhuštěný ukázkový výstup z Azure CLI:

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

Tyto vlastnosti popisují konfiguraci instance virtuálního počítače v rámci sady škálování, nikoli konfiguraci sady škálování jako celku. Například model sady škálování má `overprovision` jako vlastnost, zatímco model pro instanci virtuálního počítače v rámci sady škálování není. Důvodem je, že přestavování je vlastnost pro sadu škálování jako celek, ne jednotlivé instance virtuálních počítačů v sadě škálování (Další informace o přezřizování najdete v tématu [požadavky na návrh pro sady škálování](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Zobrazení instance virtuálního počítače sady škálování
Podobně jako u sady škálování má zobrazení instance, každá instance virtuálního počítače v sadě škálování má své vlastní zobrazení instance. K dotazování zobrazení instance pro konkrétní instanci virtuálního počítače v rámci sady škálování můžete použít:

- REST API pomocí [COMPUTE/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell s [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- [Rozhraní příkazového řádku Azure pomocí AZ VMSS Get-instance-View](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít [Resources.Azure.com](https://resources.azure.com) nebo sady [SDK Azure](https://azure.microsoft.com/downloads/) .

Přesnější prezentace výstupu závisí na možnostech, které zadáte do příkazu. Následující příklad ukazuje zhuštěný ukázkový výstup z Azure CLI:

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

Tyto vlastnosti popisují aktuální běhový stav instance virtuálního počítače v rámci sady škálování, která zahrnuje všechna rozšíření aplikovaná na sadu škálování.


## <a name="how-to-update-global-scale-set-properties"></a>Postup aktualizace vlastností globální sady škálování
Chcete-li aktualizovat vlastnost globální sady škálování, je nutné aktualizovat vlastnost v modelu sady škálování. Tuto aktualizaci můžete provést prostřednictvím:

- REST API pomocí [COMPUTE/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) následujícím způsobem:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Šablonu Správce prostředků můžete nasadit pomocí vlastností z REST API aktualizace vlastností globální sady škálování.

- Azure PowerShell s [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Rozhraní příkazového řádku Azure pomocí [AZ VMSS Update](/cli/azure/vmss):
    - Úprava vlastnosti:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Přidání objektu do vlastnosti seznamu v sadě škálování: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Odebrání objektu z vlastnosti seznamu v sadě škálování: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Pokud jste dříve nasadili sadu škálování pomocí `az vmss create` příkazu, můžete `az vmss create` znovu spustit příkaz a aktualizovat sadu škálování. Zajistěte, aby všechny vlastnosti v `az vmss create` příkazu byly stejné jako předtím, s výjimkou vlastností, které chcete upravit.

- Můžete také použít [Resources.Azure.com](https://resources.azure.com) nebo sady [SDK Azure](https://azure.microsoft.com/downloads/).

Po aktualizaci modelu sady škálování se nová konfigurace použije pro všechny nové virtuální počítače vytvořené v sadě škálování. Modely pro existující virtuální počítače v sadě škálování ale musí být pořád aktuální s nejnovějším modelem celkových sad škálování. V modelu každého virtuálního počítače je logická vlastnost `latestModelApplied` s názvem, která označuje, jestli je virtuální počítač aktuální s nejnovějším modelem celkových sad škálování ( `true` to znamená, že virtuální počítač je aktuální s nejnovějším modelem).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Jak zajistit aktuálnost virtuálních počítačů pomocí nejnovějšího modelu sady škálování
Sady škálování mají "zásady upgradu", které určují, jak jsou virtuální počítače aktualizované pomocí nejnovějšího modelu sady škálování. Existují tři režimy pro zásady upgradu:

- **Automaticky** – v tomto režimu neposkytuje sada škálování žádné záruky týkající se pořadí vypínání virtuálních počítačů. Sada škálování může současně zabrat všechny virtuální počítače. 
- **Rolling** V tomto režimu se sada škálování zaznamená aktualizace v dávkách s volitelnou dobou pozastavení mezi dávkami.
- **Ruční** – v tomto režimu se při aktualizaci modelu sady škálování nic nestane se stávajícími virtuálními počítači.
 
Pokud chcete aktualizovat stávající virtuální počítače, musíte provést ruční upgrade každého existujícího virtuálního počítače. Tento ruční upgrade můžete provést pomocí:

- REST API pomocí [COMPUTE/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) následujícím způsobem:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell s [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Rozhraní příkazového řádku Azure pomocí [AZ VMSS Update-Instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Můžete také použít sady [Azure SDK](https://azure.microsoft.com/downloads/)pro konkrétní jazyk.

>[!NOTE]
> Clustery Service Fabric můžou používat jenom *Automatický* režim, ale aktualizace se zpracovává jinak. Další informace najdete v tématu [Service Fabric upgrady aplikací](../service-fabric/service-fabric-application-upgrade.md).

Existuje jeden typ úprav vlastností globální sady škálování, který nedodržuje zásady upgradu. Změny v operačním systému a profilu datového disku (například uživatelské jméno a heslo správce) se dají změnit jenom v rozhraní API verze *2017-12-01* nebo novějším. Tyto změny platí jenom pro virtuální počítače vytvořené po změně v modelu sady škálování. Chcete-li stávající virtuální počítače přenést do aktuálního stavu, je nutné provést novou bitovou kopii každého existujícího virtuálního počítače. Tuto rebitovou kopii můžete provést prostřednictvím:

- REST API pomocí [COMPUTE/virtualmachinescalesets/ReImage](/rest/api/compute/virtualmachinescalesets/reimage) následujícím způsobem:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell s [set-AzVmssVm](/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI pomocí [AZ VMSS ReImage](/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít sady [Azure SDK](https://azure.microsoft.com/downloads/)pro konkrétní jazyk.


## <a name="properties-with-restrictions-on-modification"></a>Vlastnosti s omezeními pro úpravy

### <a name="create-time-properties"></a>Vlastnosti doby vytvoření
Některé vlastnosti lze nastavit pouze při vytváření sady škálování. Mezi tyto vlastnosti patří:

- Zóny dostupnosti
- Vydavatel odkazu na obrázek
- Nabídka odkaz na obrázek
- Typ účtu spravovaného disku operačního systému

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Vlastnosti, které se dají změnit jenom na základě aktuální hodnoty
Některé vlastnosti mohou být změněny, s výjimkami v závislosti na aktuální hodnotě. Mezi tyto vlastnosti patří:

- **singlePlacementGroup** – Pokud má singlePlacementGroup hodnotu true, může být změněno na false. Pokud je však singlePlacementGroup false, **nemusí** být upraveno na hodnotu true.
- **podsíť** – podsíť sady škálování může být upravena tak dlouho, dokud je původní podsíť a Nová podsíť ve stejné virtuální síti.

### <a name="properties-that-require-deallocation-to-change"></a>Vlastnosti, které vyžadují změnu navracení
Některé vlastnosti lze změnit pouze na určité hodnoty, pokud jsou virtuální počítače v sadě škálování navráceny. Mezi tyto vlastnosti patří:

- **Název SKU**– Pokud se nová SKU virtuálního počítače nepodporuje na hardwaru, na kterém je sada škálování aktuálně zapnutá, musíte zrušit přidělení virtuálních počítačů v sadě škálování, než UPRAVÍTE název SKU. Další informace najdete v tématu [Změna velikosti virtuálního počítače Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Aktualizace specifické pro virtuální počítače
Některé úpravy se můžou použít na konkrétní virtuální počítače místo vlastností globální sady škálování. V současné době je jedinou aktualizací specifickou pro konkrétní virtuální počítač připojení nebo odpojení datových disků k virtuálním počítačům v sadě škálování nebo k jejich odpojení. Tato funkce je ve verzi Preview. Další informace najdete v dokumentaci k [verzi Preview](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scénáře

### <a name="application-updates"></a>Aktualizace aplikace
Pokud se aplikace nasadí do sady škálování prostřednictvím rozšíření, aktualizace konfigurace rozšíření způsobí, že se aplikace aktualizuje v souladu se zásadami upgradu. Například pokud máte novou verzi skriptu, která se má spustit ve vlastním rozšíření skriptu, můžete vlastnost *identifikátorů URI* aktualizovat tak, aby odkazovala na nový skript. V některých případech můžete chtít vynutit aktualizaci i v případě, že konfigurace rozšíření zůstane beze změny (například jste aktualizovali skript bez změny identifikátoru URI skriptu). V těchto případech můžete upravit *forceUpdateTag* pro vynucení aktualizace. Platforma Azure tuto vlastnost neinterpretuje. Změníte-li hodnotu, nebude to mít žádný vliv na to, jak se rozšíření spouští. Změna jednoduše vynutí opětovné spuštění rozšíření. Další informace o *forceUpdateTag*najdete v [dokumentaci k REST API pro rozšíření](/rest/api/compute/virtualmachineextensions/createorupdate). Všimněte si, že *forceUpdateTag* lze použít se všemi příponami, nikoli pouze s rozšířením vlastních skriptů.

Je taky běžné, že se aplikace nasazují pomocí vlastní image. Tento scénář je popsaný v následující části.

### <a name="os-updates"></a>Aktualizace operačního systému
Pokud používáte image platformy Azure, můžete bitovou kopii aktualizovat úpravou *element imagereference* (Další informace najdete v [dokumentaci k REST API](/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> U imagí platformy je běžné zadání "poslední" pro verzi odkazu image. Při vytváření, škálování a obnovení bitové kopie virtuálních počítačů se vytvoří virtuální počítače s nejnovější dostupnou verzí. **Neznamená to ale** , že bitová kopie operačního systému se v průběhu času automaticky aktualizuje, protože se uvolní nové verze imagí. Samostatná funkce je aktuálně ve verzi Preview, která poskytuje automatické upgrady operačního systému. Další informace najdete v dokumentaci k [automatickým upgradem operačního systému](virtual-machine-scale-sets-automatic-upgrade.md).

Pokud používáte vlastní image, můžete bitovou kopii aktualizovat aktualizací ID *element imagereference* (Další informace najdete v [dokumentaci k REST API](/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Příklady

### <a name="update-the-os-image-for-your-scale-set"></a>Aktualizace image operačního systému pro sadu škálování
Můžete mít sadu škálování, která spouští starou verzi Ubuntu LTS 16,04. Chcete aktualizovat na novější verzi Ubuntu LTS 16,04, jako je například verze *16.04.201801090*. Vlastnost verze odkazu na obrázek není součástí seznamu, takže můžete tyto vlastnosti přímo upravit pomocí jednoho z následujících příkazů:

- Azure PowerShell s [Update-AzVmss](/powershell/module/az.compute/update-azvmss) následujícím způsobem:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Rozhraní příkazového řádku Azure pomocí [AZ VMSS Update](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Alternativně můžete chtít změnit obrázek, který sada škálování používá. Můžete například chtít aktualizovat nebo změnit vlastní image, kterou používá vaše sada škálování. Obrázek, který sada škálování používá, můžete změnit aktualizací vlastnosti ID odkazu na obrázek. Vlastnost ID odkazu na obrázek není součástí seznamu, takže tuto vlastnost můžete přímo upravit jedním z následujících příkazů:

- Azure PowerShell s [Update-AzVmss](/powershell/module/az.compute/update-azvmss) následujícím způsobem:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Rozhraní příkazového řádku Azure pomocí [AZ VMSS Update](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Aktualizace nástroje pro vyrovnávání zatížení pro sadu škálování
Řekněme, že máte sadu škálování s Azure Load Balancer a chcete nahradit Azure Load Balancer pomocí Application Gateway Azure. Vlastnosti nástroje pro vyrovnávání zatížení a Application Gateway pro sadu škálování jsou součástí seznamu, takže můžete použít příkazy k odebrání nebo přidání prvků seznamu místo úprav vlastností přímo:

- Azure PowerShell:

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

- Rozhraní příkazového řádku Azure:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> U těchto příkazů se předpokládá, že je v sadě škálování jenom jedna konfigurace protokolu IP a nástroj pro vyrovnávání zatížení. Pokud existuje více, možná budete muset použít index seznamu jiný než *0*.


## <a name="next-steps"></a>Další kroky
Můžete také provádět běžné úlohy správy pro sady škálování pomocí [Azure CLI](virtual-machine-scale-sets-manage-cli.md) nebo [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
