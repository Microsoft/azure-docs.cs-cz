---
title: Vytvoření spravovaného disku z verze image
description: Vytvoří spravovaný disk z verze image v galerii sdílených imagí.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 35edcfb4bdb0715245f4a3190fb22638b1162429
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370980"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Vytvoření spravovaného disku z verze image

Pokud potřebujete, můžete vytvořit spravovaný disk z verze Image uložené v galerii sdílených imagí.


## <a name="cli"></a>Rozhraní příkazového řádku

Nastavte `source` proměnnou na ID verze image a pak pomocí [AZ disk Create](/cli/azure/disk#az_disk_create) vytvořte spravovaný disk. 


Verze obrázků v seznamu můžete zobrazit pomocí [seznamu AZ SIG Image-Version list](/cli/azure/sig/image-version#az_sig_image_version_list). V tomto příkladu hledáme všechny verze imagí, které jsou součástí definice image *myImageDefinition* v galerii imagí *myGallery* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


V tomto příkladu vytvoříme spravovaný disk s názvem *myManagedDisk*v oblasti *EastUS* ve skupině prostředků s názvem *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

Pokud je disk datovým diskem, přidejte `--gallery-image-reference-lun` ho a zadejte logickou jednotku (LUN).

## <a name="powershell"></a>PowerShell

Všechny verze imagí můžete vypsat pomocí [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```



Jakmile budete mít všechny potřebné informace, můžete použít [příkaz Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) k získání verze zdrojového obrázku, kterou chcete použít, a přiřadit ji k proměnné. V tomto příkladu získáváme `1.0.0` verzi image z `myImageDefinition` definice v `myGallery` galerii zdrojů ve `myResourceGroup` skupině prostředků.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Nastavte některé proměnné pro informace o disku.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

Vytvořte konfiguraci disku a pak disk s použitím ID verze zdrojového obrázku. V případě je `-GalleryImageReference` logická jednotka (LUN) nutná pouze v případě, že zdrojem je datový disk.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

Vytvořte disk.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>Další kroky

Můžete také vytvořit verzi image ze spravovaného disku pomocí [Azure CLI](image-version-managed-image-cli.md) nebo [PowerShellu](image-version-managed-image-powershell.md).


