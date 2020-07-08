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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174970"
---
## <a name="update-resources"></a>Aktualizace prostředků

Existují určitá omezení, která je možné aktualizovat. Následující položky lze aktualizovat: 

Galerie sdílených imagí:
- Description

Definice Image:
- Doporučené vCPU
- Doporučená paměť
- Description
- Datum konce životnosti

Verze Image:
- Počet místních replik
- Cílové oblasti
- Vyloučení z nejnovějšího
- Datum konce životnosti

Pokud plánujete přidání oblastí repliky, neodstraňujte zdrojovou spravovanou bitovou kopii. Zdrojová image spravovaná při replikaci verze image do dalších oblastí je nutná. 

Pokud chcete aktualizovat popis galerie, použijte [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Tento příklad ukazuje, jak použít [Update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) k aktualizaci data ukončení životnosti pro naši definici obrázku.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Tento příklad ukazuje, jak použít [Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) k vyloučení této verze image z použití jako *posledního* obrázku.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Při odstraňování prostředků musíte začít s poslední položkou ve vnořených prostředcích – verze image. Po odstranění verzí můžete definici image odstranit. Galerii nejde odstranit, dokud se neodstraní všechny prostředky pod ní.

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

