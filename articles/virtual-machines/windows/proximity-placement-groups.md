---
title: 'PowerShell: Použití skupin umístění bezkontaktní komunikace'
description: Přečtěte si o vytváření a používání skupin umístění bezkontaktní komunikace pomocí Azure PowerShellu.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208521"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Nasazení virtuálních účtů do skupin umístění bezkontaktní komunikace pomocí Prostředí PowerShell


Chcete-li získat virtuální chod co nejblíže a dosáhnout nejnižší možné latence, měli byste je nasadit v [rámci skupiny umístění bez kontaktní místo](co-location.md#proximity-placement-groups).

Skupina umístění bezkontaktní komunikace je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěny blízko sebe. Skupiny umístění bezkontaktní komunikace jsou užitečné pro úlohy, kde je požadavek nízké latence.


## <a name="create-a-proximity-placement-group"></a>Vytvoření skupiny umístění bezkontaktní komunikace
Vytvořte skupinu umístění bezkontaktní komunikace pomocí [rutiny New-AzProximityPlacementGroup.](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Seznam skupin umístění bezkontaktní komunikace

Můžete vypsat všechny skupiny umístění bezkontaktní umístění pomocí [rutiny Get-AzProximityPlacementGroup.](/powershell/module/az.compute/get-azproximityplacementgroup)

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální ho ve skupině `-ProximityPlacementGroup $ppg.Id` umístění bezkontaktní umístění pomocí odkazovat na id skupiny umístění bezkontaktní při použití [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) k vytvoření virtuálního mísy.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Virtuální hotel můžete zobrazit ve skupině umístění pomocí [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Přesunutí existujícího virtuálního virtuálního virtuálního pracovního bodu do skupiny umístění bezkontaktní

Můžete také přidat existující virtuální hod do skupiny umístění bezkontaktní komunikace. Musíte nejprve zastavit\navrátit virtuální počítač, pak aktualizovat virtuální počítač a restartovat.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Přesunutí existujícího virtuálního virtuálního pracovního virtuálního hotelu mimo skupinu umístění bez kontaktní ch odst.

Pokud chcete odebrat virtuální počítač ze skupiny umístění bezkontaktní, musíte nejprve zastavit\navrátit virtuální počítač, pak aktualizovat virtuální počítač a restartovat.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Skupiny dostupnosti
Můžete také vytvořit sadu dostupnosti ve skupině umístění bezkontaktní chod. Použijte stejný `-ProximityPlacementGroup` parametr s rutinou [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) k vytvoření skupiny dostupnosti a všechny virtuální chody vytvořené v sadě dostupnosti budou také vytvořeny ve stejné skupině umístění bezkontaktní komunikace.

Chcete-li přidat nebo odebrat existující sadu dostupnosti do skupiny umístění bezkontaktní komunikace, musíte nejprve zastavit všechny virtuální chody v sadě dostupnosti. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Přesunutí existující skupiny dostupnosti do skupiny umístění bezkontaktní

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Přesunutí existující sady dostupnosti ze skupiny umístění bezkontaktní komunikace

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Škálovací sady

Můžete také vytvořit škálovací sadu ve skupině umístění bezkontaktní chod. Stejný `-ProximityPlacementGroup` parametr použijte u [nového AzVmssu](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) k vytvoření škálovací sady a všechny instance budou vytvořeny ve stejné skupině umístění bezkontaktní komunikace.


Chcete-li přidat nebo odebrat existující sadu měřítka do skupiny umístění bezkontaktní, musíte nejprve zastavit škálovací sadu. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Přesunutí existující škálovací sady do skupiny umístění bezkontaktní

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Přesunutí existující sady škálování ze skupiny umístění bezkontaktní

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

Můžete také použít [Azure CLI](../linux/proximity-placement-groups.md) k vytvoření skupiny umístění bezkontaktní.
