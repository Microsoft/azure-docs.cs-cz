---
title: Vytvoření sady škálování ze specializované verze Image pomocí Azure CLI
description: Pomocí Azure CLI vytvoříte sadu škálování s použitím specializované verze image v galerii sdílených imagí.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: f0f9b79e193c06b6492cfffcbf25811edb5c40d3
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967017"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Vytvoření sady škálování s použitím specializované verze Image pomocí Azure CLI

Vytvořte sadu škálování ze [specializované verze image](../virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images) uložené v galerii sdílených imagí. Pokud chcete vytvořit sadu škálování pomocí generalizované verze bitové kopie, přečtěte si téma [Vytvoření sady škálování z generalizované image](instance-generalized-image-version-cli.md).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.4.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

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

Vytvořte sadu škálování pomocí [`az vmss create`](/cli/azure/vmss#az-vmss-create) `--specialized` parametru pomocí parametru, který označuje, že se jedná o specializovanou image.

Pomocí ID definice image pro `--image` můžete vytvořit instance sady škálování z nejnovější verze image, která je k dispozici. Můžete také vytvořit instance sady škálování z konkrétní verze, a to zadáním ID verze image pro `--image` . Uvědomte si, že použití konkrétní verze image znamená, že automatizace může selhat, pokud není dostupná konkrétní verze image, protože se odstranila nebo odebrala z oblasti. Pro vytvoření nového virtuálního počítače doporučujeme použít ID definice image, pokud není potřeba konkrétní verze image.

V tomto příkladu vytváříme instance z nejnovější verze *myImageDefinition* image.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```


## <a name="next-steps"></a>Další kroky
[Azure image Builder (Preview)](../virtual-machines/linux/image-builder-overview.md) může přispět k automatizaci vytváření verzí image, můžete ji dokonce použít k aktualizaci a [Vytvoření nové verze image z existující verze image](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Pomocí šablon můžete také vytvořit prostředek Galerie sdílených imagí. K dispozici je několik šablon rychlého startu Azure: 

- [Vytvoření služby Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definici image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
