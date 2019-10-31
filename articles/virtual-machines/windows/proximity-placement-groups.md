---
title: Použití skupin umístění blízkosti pro virtuální počítače s Windows
description: Seznamte se s vytvářením a používáním skupin umístění blízkosti pro virtuální počítače s Windows v Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d0c35737151b060dcffba8944f4a1361d36dc14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171219"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Nasazení virtuálních počítačů do skupin umístění Proximity pomocí prostředí PowerShell


Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci [skupiny umístění blízkosti](co-location.md#proximity-placement-groups).

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.


## <a name="create-a-proximity-placement-group"></a>Vytvoření skupiny umístění bezkontaktní komunikace
Pomocí rutiny [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) vytvořte skupinu umístění blízkosti. 

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

Pokud k vytvoření virtuálního počítače použijete [příkaz New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) , ve `-ProximityPlacementGroup $ppg.Id` skupině umístění blízkosti se vytvoří virtuální počítač, který bude odkazovat na ID skupiny umístění blízkosti.

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

## <a name="availability-sets"></a>Skupiny dostupnosti
Ve skupině umístění blízkosti můžete také vytvořit skupinu dostupnosti. Pomocí rutiny [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) použijte stejný parametr `-ProximityPlacementGroup` k vytvoření skupiny dostupnosti a všechny virtuální počítače vytvořené ve skupině dostupnosti se vytvoří i ve stejné skupině umístění blízkosti.

## <a name="scale-sets"></a>Škálovací sady

Ve skupině umístění blízkosti můžete také vytvořit sadu škálování. Použijte stejný parametr `-ProximityPlacementGroup` s [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) k vytvoření sady škálování a všechny instance se vytvoří ve stejné skupině umístění blízkosti.

## <a name="next-steps"></a>Další kroky

Pomocí rozhraní příkazového [řádku Azure](../linux/proximity-placement-groups.md) můžete také vytvořit skupiny umístění pro Proximity.
