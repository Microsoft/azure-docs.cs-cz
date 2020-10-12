---
title: Náhled skupin umístění pro Proximity pro Virtual Machine Scale Sets
description: Seznamte se s vytvářením a používáním skupin umístění blízkosti pro Windows Virtual Machine Scale Sets v Azure.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: 1525ef7c1e19aa9821df3f111fdce3518d37afd5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011052"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Preview: vytváření a používání skupin umístění blízkosti pomocí PowerShellu

Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste v rámci [skupiny umístění blízkosti](co-location.md#preview-proximity-placement-groups)nasadit svou sadu škálování.

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.

> [!IMPORTANT]
> Skupiny umístění blízkosti jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Skupiny umístění blízkosti nejsou v těchto oblastech k dispozici ve verzi Preview: **Japonsko – východ**, **Austrálie – východ** a **Indie – střed**.


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


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Pomocí `-ProximityPlacementGroup $ppg.Id` [New-AzVMSS](/powershell/module/az.compute/new-azvmss) pro vytvoření sady škálování vytvořte v skupině umístění blízkosti rozsah, ve kterém se bude vztahovat na ID skupiny umístění blízkosti.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Instanci ve skupině umístění můžete zobrazit pomocí [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Další kroky

Pomocí rozhraní příkazového [řádku Azure](../virtual-machines/linux/proximity-placement-groups.md) můžete také vytvořit skupiny umístění pro Proximity.
