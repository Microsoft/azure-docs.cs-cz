---
title: Vytvoření virtuálního počítače z generalizované Image pomocí Azure CLI
description: Vytvořte virtuální počítač z zobecněné verze Image pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2bcaf85f61a4d8cf4d23c9c5be7f46d765d77dbb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551038"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>Vytvoření virtuálního počítače z generalizované verze Image pomocí rozhraní příkazového řádku

Vytvořte virtuální počítač z [zobecněné verze image](./shared-image-galleries.md#generalized-and-specialized-images) uložené v galerii sdílených imagí. Pokud chcete vytvořit virtuální počítač pomocí specializované image, přečtěte si téma [Vytvoření virtuálního počítače z specializované image](vm-specialized-image-version-powershell.md). 


## <a name="get-the-image-id"></a>Získat ID image

V galerii můžete zobrazit definice obrázků pomocí [AZ SIG image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) , abyste viděli název a ID definic.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create). Chcete-li použít nejnovější verzi obrázku, nastavte `--image` na ID definice obrázku. 

V tomto příkladu nahraďte názvy prostředků podle potřeby. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Můžete také použít konkrétní verzi pomocí ID verze image pro `--image` parametr. Například pro použití image verze *1.0.0* typ: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Další kroky

[Azure image Builder (Preview)](./image-builder-overview.md) může přispět k automatizaci vytváření verzí image, můžete ji dokonce použít k aktualizaci a [Vytvoření nové verze image z existující verze image](./linux/image-builder-gallery-update-image-version.md).