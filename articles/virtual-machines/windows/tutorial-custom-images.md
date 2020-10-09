---
title: Kurz – vytváření vlastních imagí virtuálních počítačů pomocí Azure PowerShell
description: V tomto kurzu se naučíte, jak pomocí Azure PowerShell vytvořit vlastní image virtuálního počítače s Windows uloženou v galerii sdílených imagí Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: cf8fc9916384c9eef24c4c50f7647632c0e6b7a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87077467"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Kurz: vytvoření imagí virtuálních počítačů s Windows pomocí Azure PowerShell

Image se dají použít ke spuštění nasazení a zajištění konzistence napříč několika virtuálními počítači. V tomto kurzu vytvoříte vlastní specializovanou image virtuálního počítače Azure pomocí PowerShellu a uložíte ji do galerie sdílených imagí. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření služby Shared Image Gallery
> * Vytvoření definice obrázku
> * Vytvoření verze image
> * Vytvoření virtuálního počítače z Image 
> * Sdílení Galerie imagí



## <a name="before-you-begin"></a>Než začnete

Následující kroky podrobně popisují, jak převzít existující virtuální počítač a převeďte ho na znovu použitelnou vlastní image, kterou můžete použít k vytvoření nových virtuálních počítačů.

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby si můžete zobrazit [rychlý Start PowerShellu](quick-create-powershell.md) a vytvořit si virtuální počítač, který chcete pro tento kurz použít. Při práci v kurzu nahraďte názvy prostředků tam, kde je to potřeba.

## <a name="overview"></a>Přehled

[Galerie sdílených imagí](shared-image-galleries.md) zjednodušuje sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených imagí umožňuje sdílet vlastní image virtuálních počítačů s ostatními. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. 

Funkce Galerie sdílených imagí má více typů prostředků:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="get-the-vm"></a>Získání virtuálního počítače

Seznam virtuálních počítačů, které jsou k dispozici ve skupině prostředků, můžete zobrazit pomocí [Get-AzVM](/powershell/module/az.compute/get-azvm). Jakmile znáte název virtuálního počítače a skupinu prostředků, můžete `Get-AzVM` znovu použít k získání objektu virtuálního počítače a jeho uložení do proměnné pro pozdější použití. Tento příklad načte virtuální počítač s názvem *sourceVM* ze skupiny prostředků "myResourceGroup" a přiřadí ho k proměnné *$sourceVM*. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. V následujícím příkladu se vytvoří skupina prostředků s názvem *myGalleryRG* v oblasti *EastUS* :

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Vytvoření galerie imagí 

Galerie imagí je primární prostředek, který slouží k povolení sdílení obrázků. Povolenými znaky pro název galerie jsou velká a malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky. Názvy galerií musí být v rámci vašeho předplatného jedinečné. 

Vytvořte galerii imagí pomocí [New-AzGallery](/powershell/module/az.compute/new-azgallery). Následující příklad vytvoří galerii s názvem *myGallery* ve skupině prostředků *myGalleryRG* .

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Vytvoření definice obrázku 

Definice obrázků vytvoří logické seskupení obrázků. Slouží ke správě informací o verzích imagí, které jsou v nich vytvořeny. Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](./shared-image-galleries.md#image-definitions).

Vytvořte definici Image pomocí [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). V tomto příkladu se image galerie jmenuje *myGalleryImage* a vytvoří se pro specializovanou image. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Vytvoření verze image

Vytvořte verzi image z virtuálního počítače pomocí [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu je verze image *1.0.0* a replikuje se do datových center *východní USA* i *střed USA – jih* . Při výběru cílových oblastí pro replikaci musíte zahrnout *zdrojovou* oblast jako cíl pro replikaci.

Pokud chcete vytvořit verzi image z virtuálního počítače, použijte `$vm.Id.ToString()` pro `-Source` .

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

Replikace obrázku do všech cílových oblastí může chvíli trvat.


## <a name="create-a-vm"></a>Vytvoření virtuálního počítače 

Jakmile budete mít specializovanou image, můžete vytvořit jeden nebo více nových virtuálních počítačů. Pomocí rutiny [New-AzVM](/powershell/module/az.compute/new-azvm) . Pokud chcete použít image, použijte `Set-AzVMSourceImage` a nastavte na `-Id` ID definice image ($GalleryImage. ID v tomto případě), abyste vždycky používali nejnovější verzi image. 

V tomto příkladu nahraďte názvy prostředků podle potřeby. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
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
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


## <a name="share-the-gallery"></a>Sdílení galerie

Doporučujeme sdílet přístup na úrovni Galerie imagí. Pomocí e-mailové adresy a rutiny [Get-AzADUser](/powershell/module/az.resources/get-azaduser) Získejte ID objektu pro uživatele a pak pomocí [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) udělte přístup k galerii. V tomto příkladu nahraďte příklad e-mailu alinne_montes@contoso.com vlastními informacemi.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```
   
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít rutinu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure také nabízí službu, která je založená na balíčku, [tvůrci imagí virtuálních počítačů Azure](./image-builder-overview.md). Jednoduše popište vlastní nastavení v šabloně a zpracuje se tím vytváření obrázků. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili specializovanou image virtuálního počítače. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření služby Shared Image Gallery
> * Vytvoření definice obrázku
> * Vytvoření verze image
> * Vytvoření virtuálního počítače z Image 
> * Sdílení Galerie imagí

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit vysoce dostupné virtuální počítače.

> [!div class="nextstepaction"]
> [Vytvoření vysoce dostupných virtuálních počítačů](tutorial-availability-sets.md)
