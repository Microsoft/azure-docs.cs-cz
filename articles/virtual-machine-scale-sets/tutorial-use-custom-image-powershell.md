---
title: Kurz – použití vlastní image virtuálního počítače v sadě škálování s Azure PowerShell
description: Zjistěte, jak pomocí Azure PowerShellu vytvořit vlastní image virtuálního počítače, kterou můžete použít k nasazení škálovací sady virtuálních počítačů.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 803bbf485d783fc160d1f2644a56e1339f73690e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678301"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Kurz: Vytvoření a použití vlastní image pro škálovací sady virtuálních počítačů pomocí Azure PowerShellu

Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. Pokud chcete snížit počet úloh po nasazení instancí virtuálních počítačů, můžete použít vlastní image virtuálního počítače. Tato vlastní image virtuálního počítače obsahuje instalace a konfigurace všech požadovaných aplikací. Všechny instance virtuálních počítačů vytvořené ve škálovací sadě používají vlastní image virtuálního počítače a jsou připravené k obsluze provozu aplikace. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření služby Shared Image Gallery
> * Vytvoření definice obrázku
> * Vytvoření verze image
> * Vytvoření sady škálování z obrázku 
> * Sdílení Galerie imagí

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Než začnete

Následující kroky podrobně popisují, jak převzít existující virtuální počítač a převeďte ho na znovu použitelnou vlastní image, kterou můžete použít k vytvoření sady škálování.

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby si můžete zobrazit [rychlý Start PowerShellu](quick-create-powershell.md) a vytvořit si virtuální počítač, který chcete pro tento kurz použít. Při práci v kurzu nahraďte názvy prostředků tam, kde je to potřeba.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="get-the-vm"></a>Získání virtuálního počítače

Seznam virtuálních počítačů, které jsou k dispozici ve skupině prostředků, můžete zobrazit pomocí [Get-AzVM](/powershell/module/az.compute/get-azvm). Jakmile znáte název virtuálního počítače a skupinu prostředků, můžete `Get-AzVM` znovu použít k získání objektu virtuálního počítače a jeho uložení do proměnné pro pozdější použití. Tento příklad načte virtuální počítač s názvem *sourceVM* ze skupiny prostředků "myResourceGroup" a přiřadí ho k proměnné *$VM*. 

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

Definice obrázků vytvoří logické seskupení obrázků. Slouží ke správě informací o verzích imagí, které jsou v nich vytvořeny. Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](../virtual-machines/shared-image-galleries.md#image-definitions).

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

## <a name="create-a-scale-set-from-the-image"></a>Vytvoření sady škálování z Image
Teď vytvořte sadu škálování pomocí [New-AzVmss](/powershell/module/az.compute/new-azvmss) , která pomocí `-ImageName` parametru definuje vlastní image virtuálního počítače vytvořenou v předchozím kroku. Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80, stejně jako provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace PowerShellu na portu TCP 5985. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

```azurepowershell-interactive
# Define variables for the scale set
$resourceGroupName = "myVMSSRG3"
$scaleSetName = "myScaleSet3"
$location = "East US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $galleryImage.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig 

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.


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

# Delete the scale set resource group
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure také nabízí službu, která je založená na balíčku, [tvůrci imagí virtuálních počítačů Azure](../virtual-machines/image-builder-overview.md). Jednoduše popište vlastní nastavení v šabloně a zpracuje se tím vytváření obrázků. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak vytvořit a použít vlastní image virtuálního počítače pro škálovací sady pomocí Azure PowerShellu:

> [!div class="checklist"]
> * Vytvoření služby Shared Image Gallery
> * Vytvoření definice obrázku
> * Vytvoření verze image
> * Vytvoření sady škálování z obrázku 
> * Sdílení Galerie imagí

V dalším kurzu se dozvíte, jak do škálovací sady nasadit aplikace.

> [!div class="nextstepaction"]
> [Nasazování aplikací do škálovacích sad](tutorial-install-apps-powershell.md)