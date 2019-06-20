---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174970"
---
## <a name="update-resources"></a>Aktualizace prostředků

Existuje nějaké omezení toho, co se aktualizovalo. Je možné aktualizovat následující položky: 

Galerie sdílené bitové kopie:
- Popis

definice bitové kopie:
- Doporučené virtuálních procesorů
- Doporučená velikost paměti
- Popis
- Životnost datum ukončení

Verze Image:
- Počet replik místní
- Cílové oblasti
- Vyloučení z nejnovější
- Životnost datum ukončení

Pokud plánujete přidání oblasti repliky, neodstraňujte spravované image zdroje. Spravované image zdroje je potřeba se verze image se replikuje do dalších oblastí. 

Chcete-li aktualizovat popis galerii, použijte [aktualizace AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Tento příklad ukazuje způsob použití [aktualizace AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) aktualizovat datum ukončení životnosti technologie naše definici image.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Tento příklad ukazuje způsob použití [aktualizace AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) vyloučit tuto verzi image z používán jako *nejnovější* bitové kopie.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Při odstraňování prostředků, je nutné začít s poslední položky v vnořených prostředků – verze image. Jakmile se verze odstraní, můžete odstranit k definici image. Galerie nelze odstranit, dokud se odstraní všechny prostředky pod ním.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

