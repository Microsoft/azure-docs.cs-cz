---
title: PowerShell – vytvoření image ze snímku nebo spravovaného disku v galerii sdílených imagí
description: Naučte se, jak vytvořit image ze snímku nebo spravovaného disku v galerii sdílených imagí pomocí PowerShellu.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f4ca28efce28933eed9be5cca7bd412f2d9505aa
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679530"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>Vytvoření image ze spravovaného disku nebo snímku v galerii sdílených imagí pomocí PowerShellu

Pokud máte existující snímek nebo spravovaný disk, který byste chtěli migrovat do sdílené Galerie imagí, můžete vytvořit image galerie sdílených imagí přímo ze spravovaného disku nebo snímku. Po otestování nové image můžete zdrojový spravovaný disk nebo snímek odstranit. Můžete také vytvořit image ze spravovaného disku nebo snímku v galerii sdílených imagí pomocí [Azure CLI](image-version-snapshot-cli.md).

Obrázky v galerii obrázků mají dvě komponenty, které vytvoříme v tomto příkladu:
- **Definice obrázku** obsahuje informace o imagi a požadavcích na jejich použití. To zahrnuje, zda se jedná o obrázek Windows nebo Linux, specializované nebo zobecněné, poznámky k verzi a minimální a maximální požadavky na paměť. Je definicí typu obrázku. 
- **Verze image** je ta, která se používá k vytvoření virtuálního počítače při použití Galerie sdílených imagí. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Když vytvoříte virtuální počítač, použije se k vytvoření nového disku pro virtuální počítač verze image. Verze bitové kopie lze použít několikrát.


## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku musíte mít snímek nebo spravovaný disk. 

Pokud chcete zahrnout datový disk, velikost datového disku nemůže být větší než 1 TB.

Při práci s tímto článkem nahraďte názvy prostředků tam, kde je to potřeba.


## <a name="get-the-snapshot-or-managed-disk"></a>Získání snímku nebo spravovaného disku

Seznam snímků, které jsou k dispozici ve skupině prostředků, můžete zobrazit pomocí [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot). 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

Jakmile znáte název snímku a jeho skupinu prostředků, můžete `Get-AzSnapshot` znovu použít k získání objektu snímku a jeho uložení do proměnné pro pozdější použití. Tento příklad načte snímek s názvem *mySnapshot* ze skupiny prostředků "myResourceGroup" a přiřadí ho k proměnné *$source*. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

Místo snímku můžete použít také spravovaný disk. K získání spravovaného disku použijte [příkaz Get-AzDisk](/powershell/module/az.compute/get-azdisk). 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

Pak Získejte spravovaný disk a přiřaďte ho k `$source` proměnné.

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

Stejné rutiny můžete použít k získání všech datových disků, které chcete zahrnout do image. Přiřaďte je proměnným a pak tyto proměnné použijte později při vytváření verze image.


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

Při vytváření definice obrázku se ujistěte, že jsou všechny správné informace. V tomto příkladu předpokládáme, že je snímek nebo spravovaný disk z virtuálního počítače, který se používá, a zatím není zobecněný. Pokud se spravovaný disk nebo snímek převzal na zobecněný operační systém (po spuštění nástroje Sysprep pro Windows nebo [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` nebo `-deprovision+user` Linux), změňte na `-OsState` `generalized` . 

Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](./shared-image-galleries.md#image-definitions).

Vytvořte definici Image pomocí [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). V tomto příkladu je definice bitové kopie pojmenována *myImageDefinition* a je určena pro specializované operační systémy Windows. Pokud chcete vytvořit definici imagí pomocí operačního systému Linux, použijte `-OsType Linux` . 

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

### <a name="purchase-plan-information"></a>Informace o plánu nákupu

V některých případech je nutné při vytváření virtuálního počítače z image, která byla založena na imagi Azure Marketplace, předat informace o plánu nákupu. V těchto případech doporučujeme do definice obrázku zahrnout informace o plánu nákupu. V tomto případě si přečtěte [informace o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).


## <a name="create-an-image-version"></a>Vytvoření verze image

Vytvořte z snímku verzi Image pomocí [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

Pokud chcete, aby bitová kopie obsahovala datový disk, kromě disku s operačním systémem, přidejte `-DataDiskImage` parametr a nastavte ho na ID snímku datového disku nebo spravovaného disku.

V tomto příkladu je verze image *1.0.0* a replikuje se do datových center *středozápadní USA* i *střed USA – jih* . Při výběru cílových oblastí pro replikaci nezapomeňte, že je také nutné zahrnout *zdrojovou* oblast jako cíl pro replikaci.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

Replikace obrázku do všech cílových oblastí může chvíli trvat, proto jsme vytvořili úlohu, abychom mohli sledovat průběh. Chcete-li zobrazit průběh úlohy, zadejte `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Aby bylo možné použít stejný snímek k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image. 
>
> Verzi image můžete také uložit v [zóně redundantního úložiště](../storage/common/storage-redundancy.md) přidáním `-StorageAccountType Standard_ZRS` při vytváření verze image.
>

## <a name="delete-the-source"></a>Odstranit zdroj

Jakmile ověříte, že nová verze image pracuje správně, můžete zdroj bitové kopie odstranit buď pomocí [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) , nebo [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk).


## <a name="next-steps"></a>Další kroky

Jakmile ověříte, že je replikace dokončená, můžete z [specializované image](vm-specialized-image-version-powershell.md)vytvořit virtuální počítač.

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).