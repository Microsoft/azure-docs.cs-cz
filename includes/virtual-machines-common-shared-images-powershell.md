---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 10c2b447a3f174afe93f56084827756d24d982cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82792186"
---
## <a name="create-an-image-gallery"></a>Vytvoření galerie imagí 

Galerie imagí je primární prostředek, který slouží k povolení sdílení obrázků. Povolenými znaky pro název galerie jsou velká a malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky. Názvy galerií musí být v rámci vašeho předplatného jedinečné. 

Vytvořte galerii imagí pomocí [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). Následující příklad vytvoří galerii s názvem *myGallery* ve skupině prostředků *myGalleryRG* .

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

