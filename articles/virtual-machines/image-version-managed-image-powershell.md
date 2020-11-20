---
title: Migrace spravované image do galerie sdílených imagí
description: Naučte se používat Azure PowerShell k migraci spravované image do verze image v galerii sdílených imagí.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1c57d9d283714da6905335fd3167c4f8a69292f8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964877"
---
# <a name="migrate-from-a-managed-image-to-a-shared-image-gallery-image"></a>Migrace ze spravované image do image galerie sdílených imagí

Pokud máte existující spravovanou bitovou kopii, kterou byste chtěli migrovat do sdílené Galerie imagí, můžete vytvořit image galerie sdílených imagí přímo ze spravované image. Jakmile otestujete novou bitovou kopii, můžete zdrojovou spravovanou bitovou kopii odstranit. Můžete také migrovat ze spravované image do galerie sdílených imagí pomocí [Azure CLI](image-version-managed-image-cli.md).

Obrázky v galerii obrázků mají dvě komponenty, které vytvoříme v tomto příkladu:
- **Definice obrázku** obsahuje informace o imagi a požadavcích na jejich použití. To zahrnuje, zda se jedná o obrázek Windows nebo Linux, specializované nebo zobecněné, poznámky k verzi a minimální a maximální požadavky na paměť. Je definicí typu obrázku. 
- **Verze image** je ta, která se používá k vytvoření virtuálního počítače při použití Galerie sdílených imagí. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Když vytvoříte virtuální počítač, použije se k vytvoření nového disku pro virtuální počítač verze image. Verze bitové kopie lze použít několikrát.


## <a name="before-you-begin"></a>Než začnete

Chcete-li dokončit tento článek, je nutné mít existující spravovanou bitovou kopii. Pokud spravovaná bitová kopie obsahuje datový disk, velikost datového disku nemůže být větší než 1 TB.

Při práci s tímto článkem nahraďte názvy skupin prostředků a virtuálních počítačů tam, kde je to potřeba.

## <a name="get-the-gallery"></a>Získat galerii

Můžete vypsat všechny galerie a definice imagí podle názvu. Výsledky jsou ve formátu `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Po nalezení správné galerie Vytvořte proměnnou pro pozdější použití. Tento příklad načte galerii s názvem *myGallery* ve skupině prostředků *myResourceGroup* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Vytvoření definice obrázku 

Definice obrázků vytvoří logické seskupení obrázků. Používají se ke správě informací o imagi. Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. 

Při vytváření definice obrázku se ujistěte, že jsou všechny správné informace. Vzhledem k tomu, že se spravované image vždycky zobecněny, měli byste nastavit `-OsState generalized` . 

Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](./windows/shared-image-galleries.md#image-definitions).

Vytvořte definici Image pomocí [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). V tomto příkladu má definice image název *myImageDefinition* a je určena pro zobecněný operační systém Windows. Pokud chcete vytvořit definici imagí pomocí operačního systému Linux, použijte `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>Získat spravovanou bitovou kopii

Seznam imagí, které jsou k dispozici ve skupině prostředků, můžete zobrazit pomocí [Get-AzImage](/powershell/module/az.compute/get-azimage). Jakmile znáte název bitové kopie a její skupinu prostředků, můžete `Get-AzImage` znovu použít k získání objektu image a uložit ho do proměnné pro pozdější použití. Tento příklad načte image s názvem *myImage* ze skupiny prostředků "myResourceGroup" a přiřadí ji k proměnné *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>Vytvoření verze image

Pomocí [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)vytvořte z spravované image verzi image. 

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu je verze image *1.0.0* a replikuje se do datových center *středozápadní USA* i *střed USA – jih* . Při výběru cílových oblastí pro replikaci nezapomeňte, že je také nutné zahrnout *zdrojovou* oblast jako cíl pro replikaci. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Replikace obrázku do všech cílových oblastí může chvíli trvat, proto jsme vytvořili úlohu, abychom mohli sledovat průběh. Chcete-li zobrazit průběh, zadejte `$job.State` .

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> Aby bylo možné použít stejnou spravovanou bitovou kopii k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image. 
>
> Image můžete ukládat do služby Premium Storage tím, že přidáte `-StorageAccountType Premium_LRS` nebo zadáte [redundantní úložiště do zóny](../storage/common/storage-redundancy.md) , a to přidáním, `-StorageAccountType Standard_ZRS` Když vytvoříte verzi image.
>

## <a name="delete-the-managed-image"></a>Odstranit spravovanou bitovou kopii

Jakmile ověříte, že nová verze image funguje správně, můžete ji odstranit.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

Jakmile ověříte, že je replikace dokončená, můžete z [generalizované image](vm-generalized-image-version-powershell.md)vytvořit virtuální počítač.

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).
