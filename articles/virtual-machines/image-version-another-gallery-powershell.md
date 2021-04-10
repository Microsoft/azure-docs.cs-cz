---
title: Kopírování obrázku z jiné galerie pomocí PowerShellu
description: Kopírování obrázku z jiné galerie pomocí Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d9bbe40e35bdad6fac5c5ccb0b15b909e77b938c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564012"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Kopírování obrázku z jiné galerie pomocí PowerShellu

Pokud máte ve vaší organizaci více galerií, můžete vytvářet image z imagí uložených v jiných galeriích. Můžete mít například galerii pro vývoj a testování pro vytváření a testování nových imagí. Až budou připravené k použití v produkčním prostředí, můžete je pomocí tohoto příkladu zkopírovat do produkční galerie. Můžete také vytvořit image z obrázku v jiné galerii pomocí [Azure CLI](image-version-another-gallery-cli.md).


## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku musíte mít existující galerii zdrojů, definici image a verzi image. Měli byste mít také cílovou galerii. 

Verze zdrojového obrázku se musí replikovat do oblasti, kde se nachází vaše cílová galerie. 

V cílové galerii vytvoříme novou definici image a verzi image.


Při práci s tímto článkem nahraďte názvy prostředků tam, kde je to potřeba.


## <a name="get-the-source-image"></a>Získání zdrojové image 

Budete potřebovat informace z definice zdrojové image, abyste mohli vytvořit její kopii v cílové galerii.

Pomocí rutiny [Get-AzResource](/powershell/module/az.resources/get-azresource) uveďte informace o existujících galeriích, definicích imagí a verzích imagí.

Výsledky jsou ve formátu `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Jakmile budete mít všechny potřebné informace, můžete získat ID verze zdrojového obrázku pomocí [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion). V tomto příkladu získáváme `1.0.0` verzi image z `myImageDefinition` definice v `myGallery` galerii zdrojů ve `myResourceGroup` skupině prostředků.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>Vytvoření definice image 

Musíte vytvořit novou definici image, která bude odpovídat definici obrázku vašeho zdroje. Všechny informace, které potřebujete k opětovnému vytvoření definice image, můžete zobrazit pomocí [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition).

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


Výstup bude vypadat zhruba takto:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Vytvořte novou definici image v cílové galerii pomocí rutiny [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) a informací z výstupu výše.


V tomto příkladu je definice obrázku pojmenována *myDestinationImgDef* v galerii s názvem *myDestinationGallery*.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>Vytvoření verze image

Vytvořte verzi Image pomocí [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). V `--managed-image` parametru pro vytvoření verze image v cílové galerii budete muset předat ID zdrojového obrázku. 

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu má cílová Galerie název *myDestinationGallery* ve skupině prostředků *myDestinationRG* v umístění *západní USA* . Verze našeho obrázku je *1.0.0* a my vytvoříme 1 repliku v oblasti *střed USA – jih* a 2 repliky v oblasti *západní USA* . 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
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
> Image můžete ukládat do úložiště Premiun přidáním `-StorageAccountType Premium_LRS` nebo [redundantním úložištěm zóny](../storage/common/storage-redundancy.md) přidáním `-StorageAccountType Standard_ZRS` při vytváření verze image.
>


## <a name="next-steps"></a>Další kroky

Vytvořte virtuální počítač z [zobecněné](vm-generalized-image-version-powershell.md) nebo [specializované](vm-specialized-image-version-powershell.md) verze image.

[Azure image Builder (Preview)](./image-builder-overview.md) může přispět k automatizaci vytváření verzí image, můžete ji dokonce použít k aktualizaci a [Vytvoření nové verze image z existující verze image](./linux/image-builder-gallery-update-image-version.md). 

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).