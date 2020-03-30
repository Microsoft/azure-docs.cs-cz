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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174970"
---
## <a name="update-resources"></a>Aktualizace prostředků

Existují určitá omezení, co lze aktualizovat. Lze aktualizovat následující položky: 

Sdílená galerie obrázků:
- Popis

Definice obrázku:
- Doporučené virtuální procesory
- Doporučená paměť
- Popis
- Datum konce životnosti

Verze obrázku:
- Počet regionálních replik
- Cílové oblasti
- Vyloučení z nejnovějších
- Datum konce životnosti

Pokud plánujete přidat oblasti repliky, neodstraňujte zdrojovou spravovanou bitovou kopii. Zdrojová spravovaná bitová kopie je potřebná pro replikaci verze bitové kopie do dalších oblastí. 

Chcete-li aktualizovat popis galerie, použijte [update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Tento příklad ukazuje, jak pomocí [Update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) aktualizovat datum ukončení životnosti pro naši definici obrázku.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Tento příklad ukazuje, jak použít [Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) vyloučit tuto verzi obrázku z použití jako *nejnovější* obrázek.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Při mazání prostředků je třeba začít s poslední položkou vnořených prostředcích - verzí obrázku. Po odstranění verzí můžete definici obrázku odstranit. Galerii nelze odstranit, dokud nebudou odstraněny všechny prostředky pod ní.

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

