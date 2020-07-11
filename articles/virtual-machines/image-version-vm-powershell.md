---
title: Vytvoření image z virtuálního počítače (Preview)
description: Naučte se používat Azure PowerShell k vytvoření image v galerii sdílených imagí z existujícího virtuálního počítače v Azure.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: caa8e928a10deb3d6d97e601c607074c09e0572e
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223512"
---
# <a name="preview-create-an-image-from-a-vm"></a>Verze Preview: vytvoření image z virtuálního počítače

Pokud máte existující virtuální počítač, který byste chtěli použít k vytvoření několika identických virtuálních počítačů, můžete pomocí tohoto virtuálního počítače vytvořit image v galerii sdílených imagí pomocí Azure PowerShell. Můžete také vytvořit image z virtuálního počítače pomocí rozhraní příkazového [řádku Azure CLI](image-version-vm-cli.md).

Pomocí Azure PowerShell můžete zachytit image z [specializovaných i zobecněných](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) virtuálních počítačů. 

Obrázky v galerii obrázků mají dvě komponenty, které vytvoříme v tomto příkladu:
- **Definice obrázku** obsahuje informace o imagi a požadavcích na jejich použití. To zahrnuje, zda se jedná o obrázek Windows nebo Linux, specializované nebo zobecněné, poznámky k verzi a minimální a maximální požadavky na paměť. Je definicí typu obrázku. 
- **Verze image** je ta, která se používá k vytvoření virtuálního počítače při použití Galerie sdílených imagí. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Když vytvoříte virtuální počítač, použije se k vytvoření nového disku pro virtuální počítač verze image. Verze bitové kopie lze použít několikrát.


## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku musíte mít existující galerii sdílených imagí a stávající virtuální počítač v Azure, který se použije jako zdroj. 

Pokud má virtuální počítač připojený datový disk, velikost datového disku nemůže být větší než 1 TB.

Při práci s tímto článkem nahraďte názvy prostředků tam, kde je to potřeba.


## <a name="get-the-gallery"></a>Získat galerii

Můžete vypsat všechny galerie a definice imagí podle názvu. Výsledky jsou ve formátu `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Jakmile najdete správnou galerii a definice obrázků, vytvořte proměnné, které se použijí později. Tento příklad načte galerii s názvem *myGallery* ve skupině prostředků *myResourceGroup* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>Získání virtuálního počítače

Seznam virtuálních počítačů, které jsou k dispozici ve skupině prostředků, můžete zobrazit pomocí [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Jakmile znáte název virtuálního počítače a skupinu prostředků, ve které se nachází, můžete znovu použít `Get-AzVM` k získání objektu virtuálního počítače a jeho uložení do proměnné pro pozdější použití. Tento příklad načte virtuální počítač s názvem *sourceVM* ze skupiny prostředků "myResourceGroup" a přiřadí ho k proměnné *$sourceVm*. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

Je osvědčeným postupem, jak virtuální počítač stop\deallocate před vytvořením Image pomocí [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Vytvoření definice obrázku 

Definice obrázků vytvoří logické seskupení obrázků. Používají se ke správě informací o imagi. Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. 

Při vytváření definice obrázku se ujistěte, že jsou všechny správné informace. Pokud jste virtuální počítač zobecněni (pomocí nástroje Sysprep pro Windows nebo waagent-disvisioning pro Linux), měli byste vytvořit definici Image pomocí `-OsState generalized` . Pokud jste virtuální počítač nezobecněni, vytvořte definici Image pomocí nástroje `-OsState specialized` .

Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Vytvořte definici Image pomocí [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

V tomto příkladu má definice image název *myImageDefinition*a je pro specializovaný virtuální počítač s Windows. Pokud chcete vytvořit definici imagí pomocí systému Linux, použijte `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Vytvoření verze image

Vytvořte verzi Image pomocí [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu je verze image *1.0.0* a replikuje se do datových center *středozápadní USA* i *střed USA – jih* . Při výběru cílových oblastí pro replikaci nezapomeňte, že je také nutné zahrnout *zdrojovou* oblast jako cíl pro replikaci.

Pokud chcete vytvořit verzi image z virtuálního počítače, použijte `$vm.Id.ToString()` pro `-Source` .

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Replikace obrázku do všech cílových oblastí může chvíli trvat, proto jsme vytvořili úlohu, abychom mohli sledovat průběh. Chcete-li zobrazit průběh úlohy, zadejte `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Aby bylo možné použít stejnou spravovanou bitovou kopii k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image.
>
> Image můžete ukládat do úložiště Premiun přidáním `-StorageAccountType Premium_LRS` nebo [redundantním úložištěm zóny](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) přidáním `-StorageAccountType Standard_ZRS` při vytváření verze image.
>

## <a name="next-steps"></a>Další kroky

Jakmile ověříte, že nová verze image funguje správně, můžete vytvořit virtuální počítač. Vytvořte virtuální počítač ze [specializované verze bitové kopie](vm-specialized-image-version-powershell.md) nebo [verze generalizované image](vm-generalized-image-version-powershell.md).

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).
