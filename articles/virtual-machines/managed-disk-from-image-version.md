---
title: Vytvoření spravovaného disku z verze image
description: Vytvoří spravovaný disk z verze image v galerii sdílených imagí.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 359816bbf3fb93a4ec2bfa3092adbb2236ae2f07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562550"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Vytvoření spravovaného disku z verze image

V případě potřeby můžete exportovat operační systém nebo jeden datový disk z verze Image jako spravovaný disk z verze Image uložené v galerii sdílených imagí.


## <a name="cli"></a>Rozhraní příkazového řádku

Vypište verze imagí v galerii pomocí [seznamu AZ SIG Image-Version list](/cli/azure/sig/image-version#az_sig_image_version_list). V tomto příkladu hledáme všechny verze imagí, které jsou součástí definice image *myImageDefinition* v galerii imagí *myGallery* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Nastavte `source` proměnnou na ID verze image a pak pomocí [AZ disk Create](/cli/azure/disk#az_disk_create) vytvořte spravovaný disk. 

V tomto příkladu exportujeme disk s operačním systémem verze image pro vytvoření spravovaného disku s názvem *myManagedOSDisk* v oblasti *EastUS* ve skupině prostředků s názvem *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Pokud chcete exportovat datový disk z verze image, přidejte, `--gallery-image-reference-lun` abyste určili umístění logické jednotky (LUN) datového disku, který se má exportovat. 

V tomto příkladu exportujeme datový disk umístěný na logické adrese 0 verze image k vytvoření spravovaného disku s názvem *myManagedDataDisk* v oblasti *EastUS* ve skupině prostředků s názvem *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

Vypíše verze imagí v galerii pomocí [Get-AzResource](/powershell/module/az.resources/get-azresource). 

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

Po nastavení `source` proměnné na ID verze image použijte [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) k vytvoření konfigurace disku a [New-AzDisk](/powershell/module/az.compute/new-azdisk) k vytvoření disku. 

V tomto příkladu exportujeme disk s operačním systémem verze image pro vytvoření spravovaného disku s názvem *myManagedOSDisk* v oblasti *EastUS* ve skupině prostředků s názvem *myResourceGroup*. 

Vytvořte konfiguraci disku.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Vytvořte disk.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Pokud chcete exportovat datový disk v imagi verze, přidejte do konfigurace disku ID logické jednotky (LUN), abyste určili umístění logické jednotky (LUN) datového disku, který se má exportovat. 

V tomto příkladu exportujeme datový disk umístěný na logické adrese 0 verze image k vytvoření spravovaného disku s názvem *myManagedDataDisk* v oblasti *EastUS* ve skupině prostředků s názvem *myResourceGroup*. 

Vytvořte konfiguraci disku.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Vytvořte disk.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Další kroky

Můžete také vytvořit verzi image ze spravovaného disku pomocí [Azure CLI](image-version-managed-image-cli.md) nebo [PowerShellu](image-version-managed-image-powershell.md).


