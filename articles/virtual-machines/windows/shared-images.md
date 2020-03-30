---
title: Vytváření sdílených ibi virtuálních počítačů s Azure PowerShellem
description: Přečtěte si, jak pomocí Azure PowerShellu vytvořit sdílenou image virtuálního počítače v Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: db877c96167fc011c1a8bd52cc1d0b63260007c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74066238"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Vytvoření sdílené galerie obrázků pomocí Azure PowerShellu 

[Galerie sdílených obrázků](shared-image-galleries.md) zjednodušuje vlastní sdílení obrázků v celé organizaci. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image lze použít k zavádění úloh nasazení, jako je předběžné načtení aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených obrázků umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci, v rámci nebo napříč oblastmi, v rámci klienta AAD. Zvolte, které obrázky chcete sdílet, ve kterých oblastech je chcete zpřístupnit a s kým je chcete sdílet. Můžete vytvořit více galerií, abyste mohli logicky seskupit sdílené obrázky. 

Galerie je prostředek nejvyšší úrovně, který poskytuje úplné řízení přístupu na základě rolí (RBAC). Image může být verzí a můžete se rozhodnout replikovat každou verzi bitové kopie do jiné sady oblastí Azure. Galerie pracuje pouze se spravovanými obrázky.

Funkce Galerie sdílených obrázků má více typů prostředků. Budeme používat nebo stavební tyto v tomto článku:

| Prostředek | Popis|
|----------|------------|
| **Spravovaná bitová kopie** | Jedná se o základní obrázek, který lze použít samostatně nebo použít k vytvoření **verze obrázku** v galerii obrázků. Spravované bitové kopie se vytvářejí z generalizovaných virtuálních měn. Spravovaná bitová kopie je speciální typ virtuálního pevného disku, který lze použít k vytvoření více virtuálních počítačů a nyní lze použít k vytvoření verzí sdílených bitových obrázků. |
| **Galerie obrázků** | Stejně jako Azure Marketplace, **galerie obrázků** je úložiště pro správu a sdílení bitových kopií, ale máte řízení, kdo má přístup. |
| **Definice obrázku** | Obrázky jsou definovány v galerii a nesou informace o obrázku a požadavky na jeho použití interně. To zahrnuje, zda je bitová kopie Windows nebo Linux, poznámky k verzi a minimální a maximální požadavky na paměť. Jedná se o definici typu obrazu. |
| **Verze obrázku** | **Verze bitové kopie** je to, co používáte k vytvoření virtuálního počítačů při použití galerie. Podle potřeby pro vaše prostředí můžete mít více verzí bitové kopie. Stejně jako spravovaná **image version** bitová kopie, když k vytvoření virtuálního počítače použijete verzi image k vytvoření nových disků pro virtuální počítače. Verze obrázků lze použít vícekrát. |

Pro každých 20 virtuálních počítače, které vytvoříte souběžně, doporučujeme zachovat jednu repliku. Pokud například vytváříte 120 virtuálních počítačů souběžně pomocí stejné bitové kopie v oblasti, doporučujeme zachovat alespoň 6 replik bitové kopie. Další informace naleznete v [tématu Škálování](/azure/virtual-machines/windows/shared-image-galleries#scaling).

## <a name="before-you-begin"></a>Než začnete

Chcete-li dokončit příklad v tomto článku, musíte mít existující spravovanou bitovou kopii. Můžete postupovat [podle kurzu: Vytvořte vlastní image virtuálního počítače Azure s Azure PowerShell](tutorial-custom-images.md) em a v případě potřeby ho vytvořte. Pokud spravovaná bitová kopie obsahuje datový disk, velikost datového disku nesmí být větší než 1 TB.

Při práci v tomto článku nahraďte názvy skupin prostředků a virtuálních počítačů, kde je to potřeba.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>Vytvoření virtuálních obrazců z bitové kopie

Po dokončení verze image můžete vytvořit jeden nebo více nových virtuálních virtuálních discích. Použití rutiny [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 

Tento příklad vytvoří virtuální ho s názvem *myVMfromImage*, v *myResourceGroup* v *jižním centrálním usa* datového centra.


```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Další kroky
[Azure Image Builder (preview)](image-builder-overview.md) může pomoci automatizovat vytváření verzí bitové kopie, můžete ji dokonce použít k aktualizaci a [vytvoření nové verze image z existující verze image](image-builder-gallery-update-image-version.md). 

Prostředek Galerie sdílených obrázků můžete také vytvořit pomocí šablon. K dispozici je několik šablon Azure QuickStart: 

- [Vytvoření galerie sdílených obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice obrazu ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze obrázku ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního virtuálního virtuálního mísy z verze bitové kopie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o sdílených galeriích obrázků naleznete v [tématu Přehled](shared-image-galleries.md). Pokud narazíte na problémy, [přečtěte si článek Poradce při potížích s galeriemi sdílených obrázků](troubleshooting-shared-images.md).

