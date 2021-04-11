---
title: Zadání informací o plánu nákupu na webu Marketplace pomocí Azure PowerShell
description: Naučte se při vytváření imagí v galerii sdílených imagí zadat Azure Marketplace podrobnosti plánu nákupu.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9df1f6b02e6572c8f2153016c0142912e24fcfe8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562533"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Při vytváření imagí zadejte Azure Marketplace informace o plánu nákupu.

Pokud vytváříte image ve sdílené galerii pomocí zdroje, který byl původně vytvořen z Azure Marketplace image, možná budete muset sledovat informace o plánu nákupu. Tento článek ukazuje, jak najít informace o plánu nákupu pro virtuální počítač, a pak tyto informace použít při vytváření definice image. Také se používá informace z definice image k jednoduššímu poskytnutí informací o plánu nákupu při vytváření virtuálního počítače pro image.

Další informace o hledání a používání imagí na webu Marketplace najdete v tématu [vyhledání a použití imagí Azure Marketplace](./windows/cli-ps-findimage.md).


## <a name="get-the-source-vm-information"></a>Získat informace o zdrojovém virtuálním počítači
Pokud máte i nadále původní virtuální počítač, můžete z něj získat název plánu, vydavatele a informace o produktu pomocí Get-AzVM. Tento příklad načte virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup* a pak zobrazí informace o plánu nákupu pro virtuální počítač.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>Vytvoření definice image

Získejte galerii imagí, kterou chcete použít k uložení image. Nejprve můžete vypsat všechny galerie.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Pak vytvořte proměnné pro galerii, kterou chcete použít. V tomto příkladu vytváříme proměnnou s názvem `$gallery` pro *myGallery* ve skupině prostředků *myGalleryRG* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

Vytvořte definici Image pomocí  `-PurchasePlanPublisher` `-PurchasePlanProduct` parametrů, a `-PurchasePlanName` .

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Pak vytvořte verzi Image pomocí [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). Můžete vytvořit verzi image z [virtuálního počítače](image-version-vm-powershell.md#create-an-image-version), [spravované image](image-version-managed-image-powershell.md#create-an-image-version), [VHD\snapshot](image-version-snapshot-powershell.md#create-an-image-version)nebo [jiné verze image](image-version-another-gallery-powershell.md#create-the-image-version). 


## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Když přejdete na vytvořit virtuální počítač z image, můžete pomocí informací z definice image předat informace o vydavateli pomocí [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan).


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Další kroky

Další informace o hledání a používání imagí na webu Marketplace najdete v tématu [vyhledání a použití imagí Azure Marketplace](./windows/cli-ps-findimage.md).
