---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241208"
---
## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="get-the-managed-image"></a>Získání spravované bitové kopie

Můžete zobrazit seznam obrázků, které jsou k dispozici ve skupině prostředků pomocí [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Jakmile znáte název obrázku a jakou skupinu `Get-AzImage` prostředků se nachází, můžete znovu použít k získání objektu obrázku a jeho uložení do proměnné, která bude později použít. Tento příklad získá obrázek s názvem *myImage* ze skupiny prostředků "myResourceGroup" a přiřadí jej proměnné *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Vytvoření galerie obrázků 

Galerie obrázků je primární prostředek používaný pro povolení sdílení obrázků. Povolené znaky pro název galerie jsou velká nebo malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky. Názvy galerií musí být v rámci předplatného jedinečné. 

Vytvořte galerii obrázků pomocí [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). Následující příklad vytvoří galerii s názvem *myGallery* ve skupině prostředků *myGalleryRG.*

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Vytvoření definice obrázku 

Definice obrázků vytvářejí logické seskupení pro obrazy. Používají se ke správě informací o verzích bitových obrázků, které jsou v nich vytvořeny. Názvy definic obrázků mohou být tvořeny velkými nebo velkými písmeny, číslicemi, tečkami, pomlčkami a tečkami. Další informace o hodnotách, které můžete zadat pro definici obrazu, naleznete v [tématu Definice obrázků](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Vytvořte definici obrazu pomocí [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). V tomto příkladu je obrázek galerie pojmenován *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Vytvoření verze obrázku

Vytvořte verzi bitové kopie ze spravované bitové kopie pomocí [new-azgalleryimageversion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32bitové celé číslo. Formát: *MajorVersion*. *MinorVersion*. *Záplata*.

V tomto příkladu je verze bitové kopie *1.0.0* a je replikována do datových center *– střed USA* – západ a střed *usa.* Při výběru cílových oblastí pro replikaci nezapomeňte, že je také třeba zahrnout *zdrojovou* oblast jako cíl pro replikaci.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

Replikace obrázku do všech cílových oblastí může chvíli trvat, takže jsme vytvořili úlohu, abychom mohli sledovat průběh. Chcete-li zobrazit průběh úlohy, zadejte . `$job.State`

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Před použitím stejné spravované bitové kopie k vytvoření jiné verze bitové kopie je třeba počkat, až bude verze bitové kopie zcela dokončena. 
>
> Verzi bitové kopie můžete také uložit `-StorageAccountType Standard_ZRS` do [zónového redundantního úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) přidáním při vytváření verze bitové kopie.
>


## <a name="share-the-gallery"></a>Sdílet galerii

Doporučujeme sdílet přístup na úrovni galerie obrázků. Pomocí e-mailové adresy a rutiny [Get-AzADUser](/powershell/module/az.resources/get-azaduser) získáte ID objektu pro uživatele a pak jim udělte přístup do galerie pomocí [funkce New-AzRoleAssignment.](/powershell/module/Az.Resources/New-AzRoleAssignment) Nahraďte ukázkový e-mail alinne_montes@contoso.com v tomto příkladu vlastními informacemi.

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