---
title: 'PowerShell: použití skupin umístění blízkosti'
description: Seznamte se s vytvářením a používáním skupin umístění blízkosti pomocí Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 4de71be8c88264d2cfb513a7f0214515058b5185
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878320"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Nasazení virtuálních počítačů do skupin umístění Proximity pomocí prostředí PowerShell


Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci [skupiny umístění blízkosti](../co-location.md#proximity-placement-groups).

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.


## <a name="create-a-proximity-placement-group"></a>Vytvoření skupiny umístění bezkontaktní komunikace
Pomocí rutiny [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) vytvořte skupinu umístění blízkosti. 

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

## <a name="list-proximity-placement-groups"></a>Seznam skupin umístění blízkosti

Pomocí rutiny [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) můžete zobrazit seznam všech skupin umístění blízkosti.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

`-ProximityPlacementGroup $ppg.Id`Pokud k vytvoření virtuálního počítače použijete [příkaz New-AzVM](/powershell/module/az.compute/new-azvm) , ve skupině umístění blízkosti se vytvoří virtuální počítač, který bude odkazovat na ID skupiny umístění blízkosti.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Virtuální počítač ve skupině umístění můžete zobrazit pomocí [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Přesunout existující virtuální počítač do skupiny umístění blízkosti

Existující virtuální počítač můžete také přidat do skupiny umístění blízkosti. Nejdřív musíte virtuální počítač stop\deallocate a pak aktualizovat virtuální počítač a restartovat.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Přesunout existující virtuální počítač ze skupiny umístění do blízkosti

Pokud chcete virtuální počítač odebrat ze skupiny umístění blízkosti, musíte nejdřív virtuální počítač stop\deallocate a pak ho aktualizovat a restartovat.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Skupiny dostupnosti
Ve skupině umístění blízkosti můžete také vytvořit skupinu dostupnosti. Použijte stejný `-ProximityPlacementGroup` parametr s rutinou [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) k vytvoření skupiny dostupnosti a všechny virtuální počítače vytvořené ve skupině dostupnosti se vytvoří i ve stejné skupině umístění blízkosti.

Pokud chcete přidat nebo odebrat existující skupinu dostupnosti do skupiny umístění blízkosti, musíte nejprve zastavit všechny virtuální počítače ve skupině dostupnosti. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Přesunout existující skupinu dostupnosti do skupiny umístění blízkosti

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

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Přesunutí stávající skupiny dostupnosti mimo skupinu umístění blízkosti

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

Ve skupině umístění blízkosti můžete také vytvořit sadu škálování. Použijte stejný `-ProximityPlacementGroup` parametr s [New-AzVmss](/powershell/module/az.compute/new-azvmss) k vytvoření sady škálování a všechny instance se vytvoří ve stejné skupině umístění blízkosti.


Pokud chcete přidat nebo odebrat existující sadu škálování na skupinu umístění blízkosti, musíte nejprve zastavit sadu škálování. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Přesunout existující sadu škálování do skupiny umístění blízkosti

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Přesune stávající škálu, která je nastavená mimo skupinu umístění blízkosti.

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

Pomocí rozhraní příkazového [řádku Azure](../linux/proximity-placement-groups.md) můžete také vytvořit skupiny umístění pro Proximity.