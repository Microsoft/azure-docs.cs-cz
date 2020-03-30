---
title: Náhled náhledu skupin umístění bezkontaktních umístění pro škálovací sady virtuálních strojů
description: Přečtěte si o vytváření a používání skupin umístění bezkontaktní komunikace pro škálovací sady virtuálních strojů s Windows v Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273625"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Náhled: Vytváření a používání skupin umístění bezkontaktní komunikace pomocí PowerShellu

Chcete-li získat virtuální počítače co nejblíže, dosažení nejnižší možnou latenci, měli byste nasadit škálovací sadu v rámci [skupiny umístění bez kontaktní místo](co-location.md#preview-proximity-placement-groups).

Skupina umístění bezkontaktní komunikace je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěny blízko sebe. Skupiny umístění bezkontaktní komunikace jsou užitečné pro úlohy, kde je požadavek nízké latence.

> [!IMPORTANT]
> Skupiny umístění bezkontaktní komunikace jsou aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Skupiny umístění bezkontaktního umístění nejsou v těchto oblastech během předběžné verze k dispozici: **Japonsko – východ**, Austrálie – **východ** a Indie **– střed**.


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


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Při vytváření škálovací sady vytvořte měřítko ve skupině umístění bezkontaktní [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) komunikace pomocí `-ProximityPlacementGroup $ppg.Id` odkazu na ID skupiny umístění bez kontaktní ch ustavování.

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

Instanci můžete zobrazit ve skupině umístění pomocí [get-azproximityplacementgroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Další kroky

Můžete také použít [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) k vytvoření skupiny umístění bezkontaktní.
