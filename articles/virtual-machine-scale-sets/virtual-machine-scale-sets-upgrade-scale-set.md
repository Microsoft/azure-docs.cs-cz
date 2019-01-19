---
title: Úprava škálovací sady virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak upravit a aktualizovat virtuálních počítačů Azure škálovací sady pomocí rozhraní REST API, prostředí Azure PowerShell a rozhraní příkazového řádku Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
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
ms.author: manayar
ms.openlocfilehash: ce031b5c0dba96ab1a51532ad771eebeafb5d599
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413256"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Úprava škálovací sady virtuálních počítačů
V průběhu životního cyklu aplikací můžete změnit nebo aktualizovat škálovací sadu virtuálních počítačů. Tyto aktualizace může zahrnovat jak aktualizovat konfiguraci škálovací sady nebo změnit konfiguraci aplikace. Tento článek popisuje, jak změnit existující škálovací sady pomocí rozhraní REST API, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="fundamental-concepts"></a>Základní koncepty

### <a name="the-scale-set-model"></a>Modelu škálovací sady
Škálovací sada měla "modelu škálovací sady", který zachycuje *požadované* stav stupnice nastaven jako celek. K dotazování modelů pro škálovací sadu, můžete použít 

- Rozhraní REST API s [výpočetní/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI s [az vmss show](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo konkrétní jazyk [sady Azure SDK](https://azure.microsoft.com/downloads/).

Přesné prezentace výstup závisí na možnostech, které poskytnete k příkazu. Následující příklad ukazuje zhuštěnému ukázkový výstup z příkazového řádku Azure:

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

Tyto vlastnosti se vztahují do škálovací sady jako celek.


### <a name="the-scale-set-instance-view"></a>Zobrazení instance škálovací sady
Škálovací sady také má "škálovací sada instanci zobrazení", který explicitně zaznamenává aktuální *runtime* stav stupnice nastaven jako celek. K zobrazení instance škálovací sady pro dotazování, můžete použít:

- Rozhraní REST API s [výpočetní/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI s [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo konkrétní jazyk [sady Azure SDK](https://azure.microsoft.com/downloads/)

Přesné prezentace výstup závisí na možnostech, které poskytnete k příkazu. Následující příklad ukazuje zhuštěnému ukázkový výstup z příkazového řádku Azure:

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

Tyto vlastnosti nabízí souhrnné informace o aktuální stav modulu runtime virtuálních počítačů ve škálovací sadě, jako je stav rozšíření u škálovací sady.


### <a name="the-scale-set-vm-model-view"></a>Škálovací sady virtuálních počítačů modelu zobrazení
Podobně jako u jak škálovací sada obsahovat zobrazení modelu, všechny instance virtuálních počítačů ve škálovací sadě má vlastní zobrazení modelu. Dotaz ze zobrazení modelu pro konkrétní instanci virtuálního počítače ve škálovací sadě, můžete použít:

- Rozhraní REST API s [výpočetní/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI s [az vmss show](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/).

Přesné prezentace výstup závisí na možnostech, které poskytnete k příkazu. Následující příklad ukazuje zhuštěnému ukázkový výstup z příkazového řádku Azure:

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

Tyto vlastnosti popis konfigurace instance virtuálního počítače ve škálovací sadě, není konfigurace škálovací sady jako celek. Například modelu škálovací sady má `overprovision` jako vlastnost, ale nikoli model pro instance virtuálních počítačů ve škálovací sadě. Tento rozdíl je, protože předimenzování je vlastnost škálovací sady jako celá, ne u jednotlivých instancí virtuálních počítačů ve škálovací sadě (Další informace o předimenzování najdete v tématu [aspekty návrhu pro škálovací sady](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Zobrazení instance virtuálního počítače škálovací sady
Podobně jako u jak škálovací sady má zobrazení instance, všechny instance virtuálních počítačů ve škálovací sadě má svůj vlastní zobrazení instance. Dotaz na zobrazení instance pro konkrétní instanci virtuálního počítače ve škálovací sadě, můžete použít:

- Rozhraní REST API s [výpočetní/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) následujícím způsobem:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI s [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/)

Přesné prezentace výstup závisí na možnostech, které poskytnete k příkazu. Následující příklad ukazuje zhuštěnému ukázkový výstup z příkazového řádku Azure:

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

Tyto vlastnosti popisují aktuální stav běhu instance virtuálního počítače ve škálovací sadě, která zahrnuje všechna rozšíření u škálovací sady.


## <a name="how-to-update-global-scale-set-properties"></a>Jak aktualizovat globální škálování nastavit vlastnosti
Pokud chcete aktualizovat vlastnosti globálních škálovací sady, je nutné aktualizovat vlastnost v modelu škálovací sady. Tato aktualizace prostřednictvím můžete provést:

- Rozhraní REST API s [výpočetní/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) následujícím způsobem:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Můžete nasadit šablonu Resource Manageru s vlastnostmi z rozhraní REST API k aktualizaci vlastností globální škálovací sady.

- Prostředí Azure PowerShell s [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss):

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI s [az vmss update](/cli/azure/vmss#az_vmss_update):
    - Chcete-li změnit vlastnosti:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Přidání objektu do seznamu vlastností ve škálovací sadě: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Chcete-li odebrat objekt ze seznamu vlastností ve škálovací sadě: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Pokud jste předtím nasadili škálovací sady `az vmss create` příkazu, můžete spustit `az vmss create` příkaz znovu a aktualizujte škálovací sady. Ujistěte se, že všechny vlastnosti v `az vmss create` příkaz shodují stejně jako předtím, s výjimkou vlastnosti, které chcete upravit.

- Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/).

Jakmile dojde k aktualizaci modelu škálovací sady, nová konfigurace platí pro všechny nové virtuální počítače vytvořené ve škálovací sadě. Ale modely pro stávající virtuální počítače ve škálovací sadě musí stále vnášet naskočíte nejnovější celkové modelu škálovací sady. V modelu pro každý virtuální počítač je logická vlastnost s názvem `latestModelApplied` , která označuje, zda je aktualizován nejnovější celkové modelu škálovací sady virtuálního počítače (`true` znamená, že virtuální počítač je aktualizován na nejnovější model).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Zajištění naskočíte na nejnovější model škálovací sady virtuálních počítačů
Škálovací sady obsahovat "upgrade zásady", které definují, jak nabíhají naskočíte na nejnovější model škálovací sady virtuálních počítačů. Tři režimy pro zásady upgradu jsou:

- **Automatické** – v tomto režimu se škálovací sada neposkytuje žádnou záruku o pořadí se načtou virtuálních počítačů. Škálovací sada může trvat dolů všechny virtuální počítače ve stejnou dobu. 
- **Se zajištěním provozu** – v tomto režimu se škálovací sada zavádí postupně v dávkách, volitelné pozastavení doba mezi listy.
- **Ruční** – v tomto režimu se při aktualizaci modelu škálovací sady, nic se nestane na existující virtuální počítače.
 
Pokud chcete aktualizovat stávající virtuální počítače, musíte udělat "ruční upgrade" každý existujícího virtuálního počítače. Je-li provést tento ruční upgrade s:

- Rozhraní REST API s [výpočetní/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) následujícím způsobem:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance):
    
    ```powershell
    Update-AzureRmVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI s [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Můžete také použít konkrétní jazyk [sady Azure SDK](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Clustery Service Fabric můžete použít jenom *automatické* režim, ale aktualizace je zpracována jiným způsobem. Další informace najdete v tématu [ upgrady aplikací Service Fabric](../service-fabric/service-fabric-application-upgrade.md).

Existuje jeden typ změny nastavení vlastností globální škálování, které nedodržuje zásady upgradu. Změny na škálovací sadu, profil operačního systému (například uživatelské jméno admin a heslo) lze změnit pouze ve verzi rozhraní API *2017-12-01* nebo novější. Tyto změny platí jenom pro virtuální počítače vytvořené po změnu v hodnotě stupnice nastavení modelu. Pokud chcete přenést aktuální stávající virtuální počítače, musíte udělat "obnovit" každý existujícího virtuálního počítače. Můžete provést toto obnovení z Image prostřednictvím:

- Rozhraní REST API s [výpočetní/virtualmachinescalesets/obnovit](/rest/api/compute/virtualmachinescalesets/reimage) následujícím způsobem:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Prostředí Azure PowerShell s [Set-AzureRmVmssVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm):

    ```powershell
    Set-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI s [az vmss reimage](https://docs.microsoft.com/cli/azure/vmss#az_vmss_reimage):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Můžete také použít konkrétní jazyk [sady Azure SDK](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>Vlastnosti s omezením na úpravy

### <a name="create-time-properties"></a>Čas vytvoření vlastnosti
Některé vlastnosti lze nastavit pouze při vytváření škálovací sady. Tyto vlastnosti patří:

- Zóny dostupnosti
- Referenční dokumentace vydavatel Image
- Nabídka referenční bitové kopie
- Spravované typ účtu úložiště pro disk operačního systému

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Vlastnosti, které lze změnit pouze na základě aktuální hodnoty
Některé vlastnosti mohou být změněny, s výjimkami v závislosti na aktuální hodnotu. Tyto vlastnosti patří:

- **singlePlacementGroup** – Pokud singlePlacementGroup má hodnotu true, může být změněna na hodnotu false. Nicméně, pokud má hodnotu false, singlePlacementGroup ho **nemusí** změnit na hodnotu true.
- **podsíť** – podsítě škálovací sada může být změněno tak dlouho, dokud původní podsítě a nové podsítě jsou ve stejné virtuální síti.

### <a name="properties-that-require-deallocation-to-change"></a>Vlastnosti, které vyžadují zrušení přidělení, chcete-li změnit
Některé vlastnosti může změnit na konkrétní hodnoty pouze v případě virtuálních počítačů ve škálovací sadě se uvolní. Tyto vlastnosti patří:

- **Název skladové položky**– Pokud novou skladovou Položku virtuálního počítače není podporovaná na hardwaru škálovací sada je nyní, budete muset uvolnit virtuálních počítačů ve škálovací sadě předtím, než změníte název skladové položky. Další informace najdete v tématu [postupu při změně velikosti virtuálního počítače Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Aktualizace specifických pro virtuální počítače
Některé změny se můžou vztahovat na konkrétní virtuální počítače namísto nastavení vlastností globální škálování. V současné době pouze k připojení a odpojení datových disků do a z virtuálních počítačů ve škálovací sadě je specifické pro virtuální počítače aktualizace, která je podporována. Tato funkce je ve verzi Preview. Další informace najdete v tématu [ve verzi preview dokumentaci](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scénáře

### <a name="application-updates"></a>Aktualizace aplikace
Pokud je aplikace nasazená do škálovací sady pomocí rozšíření, aktualizaci konfigurace rozšíření způsobí, že aplikace aktualizace v souladu s zásad upgradu. Například pokud máte novou verzi skript ke spuštění v rozšíření vlastních skriptů, může aktualizujete *fileUris* vlastnost tak, aby odkazovala na nový skript. V některých případech můžete chtít vynutit aktualizaci, i když je beze změny konfigurace rozšíření (například jste aktualizovali skript beze změn na identifikátor URI skriptu). V těchto případech můžete upravit *forceUpdateTag* chcete vynutit aktualizaci. Platforma Azure neinterpretuje tuto vlastnost. Pokud změníte hodnotu, neexistuje žádný vliv na vykonávání rozšíření. Změna jednoduše vynutí rozšíření spustit znovu. Další informace o *forceUpdateTag*, najdete v článku [dokumentace k rozhraní REST API pro rozšíření](/rest/api/compute/virtualmachineextensions/createorupdate). Všimněte si, *forceUpdateTag* jde použít s všechna rozšíření, nejen pomocí rozšíření vlastních skriptů.

Také je běžné, že aplikace má být nasazen do vlastní image. Tento scénář je popsané v následující části.

### <a name="os-updates"></a>Aktualizace operačního systému
Pokud používáte Image platformy Azure, můžete aktualizovat image tak, že upravíte *imageReference* (Další informace najdete v článku [dokumentace k rozhraní REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Pomocí Image platformy je běžné nastavit "nejnovější" pro odkaz na verzi image. Při vytváření, škálování a obnovení z Image, virtuální počítače se vytvoří s nejnovější dostupnou verzi. Ale to **nemá** znamená, že image operačního systému se automaticky aktualizuje časem jak se vydávají nové verze image. Samostatné funkce je momentálně ve verzi preview, která poskytuje automatické upgrady operačního systému. Další informace najdete v tématu [automatické upgrady operačního systému dokumentaci](virtual-machine-scale-sets-automatic-upgrade.md).

Pokud používáte vlastní Image, můžete aktualizovat image aktualizací *imageReference* ID (Další informace najdete v článku [dokumentace k rozhraní REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Příklady

### <a name="update-the-os-image-for-your-scale-set"></a>Aktualizace image operačního systému pro škálovací sady
Může mít škálovací sady, na kterém běží stará verze Ubuntu LTS 16.04. Chcete ji aktualizovat na novější verze Ubuntu LTS 16.04, jako je například verze *16.04.201801090*. Vlastnosti verze image odkaz není součástí seznamu, proto mohou přímo upravit tyto vlastnosti s jednou z následujících příkazů:

- Prostředí Azure PowerShell s [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) následujícím způsobem:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI s [az vmss update](/cli/azure/vmss#az_vmss_update_instances):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Alternativně můžete změnit obrázek, vaše škálovací sada používá. Například můžete aktualizovat nebo změnit vlastní bitovou kopii používanou škálovací sadou. Obrázek, vaše škálovací sada používá aktualizací vlastnost ID bitové kopie referenčního můžete změnit. Vlastnost ID obrázku odkaz není součástí seznamu, proto mohou přímo upravit tuto vlastnost s jedním z následujících příkazů:

- Prostředí Azure PowerShell s [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) následujícím způsobem:

    ```powershell
    Update-AzureRmVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI s [az vmss update](/cli/azure/vmss#az_vmss_update_instances):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Aktualizujte nástroj pro vyrovnávání zatížení pro škálovací sady
Řekněme, že máte škálovací sady s pomocí služby Azure Load Balancer a mají být nahrazeny nástroje pro vyrovnávání zatížení Azure s využitím služby Azure Application Gateway. Vlastnosti nástroje pro vyrovnávání zatížení a služba Application Gateway pro škálovací sady jsou součástí seznamu, proto můžete pomocí příkazů odeberte nebo přidejte prvky seznamu namísto přímo úpravou vlastností:

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

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Těchto příkazů se předpokládá, že existuje pouze jeden IP konfigurace a zatížení nástroj pro vyrovnávání ve škálovací sadě. Pokud je více, budete muset použít jiné než index seznamu *0*.


## <a name="next-steps"></a>Další postup
Můžete také provádět běžné úlohy správy do škálovací sady s [rozhraní příkazového řádku Azure](virtual-machine-scale-sets-manage-cli.md) nebo [prostředí Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
