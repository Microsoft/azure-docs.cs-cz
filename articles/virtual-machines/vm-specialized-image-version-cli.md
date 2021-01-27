---
title: Vytvoření virtuálního počítače ze specializované verze Image pomocí Azure CLI
description: Vytvořte virtuální počítač pomocí specializované verze image v galerii sdílených imagí pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: dedefcd18a2860bbcae9a0ac6b5b07550d9cbf3f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881936"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Vytvoření virtuálního počítače s využitím specializované verze Image pomocí Azure CLI

Vytvořte virtuální počítač ze [specializované verze image](./shared-image-galleries.md#generalized-and-specialized-images) uložené v galerii sdílených imagí. Pokud chcete vytvořit virtuální počítač s použitím generalizované image, přečtěte si téma [Vytvoření virtuálního počítače z generalizované verze image](vm-generalized-image-version-cli.md).

V tomto příkladu nahraďte názvy prostředků podle potřeby. 

V galerii můžete zobrazit definice obrázků pomocí [AZ SIG image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) , abyste viděli název a ID definic.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Vytvořte virtuální počítač pomocí příkazového typu [AZ VM Create](/cli/azure/vm#az-vm-create) pomocí parametru--specializované, který označuje, že se jedná o specializovanou image. 

Použijte ID definice image pro `--image` k vytvoření virtuálního počítače z nejnovější verze image, která je k dispozici. Virtuální počítač můžete vytvořit také z konkrétní verze zadáním ID verze image pro `--image` . 

V tomto příkladu vytváříme virtuální počítač z nejnovější verze *myImageDefinition* image.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Další kroky
[Azure image Builder (Preview)](./image-builder-overview.md) může přispět k automatizaci vytváření verzí image, můžete ji dokonce použít k aktualizaci a [Vytvoření nové verze image z existující verze image](./linux/image-builder-gallery-update-image-version.md). 

Pomocí šablon můžete také vytvořit prostředek Galerie sdílených imagí. K dispozici je několik šablon rychlého startu Azure: 

- [Vytvoření služby Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definici image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z verze image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)