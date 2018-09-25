---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 830deb7569772b610b7e6abde649830b7ad67a92
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047265"
---
## <a name="before-you-begin"></a>Než začnete

K dokončení příkladu v tomto článku, musíte mít existující spravované image zobecněného virtuálního počítače. Další informace najdete v tématu [kurz: vytvoření vlastní image virtuálního počítače Azure pomocí rozhraní příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

## <a name="preview-register-the-feature"></a>Ve verzi Preview: Zaregistrovat funkci

Sdílené Galerie obrázků je ve verzi preview, ale budete muset zaregistrovat funkci, než budete moct použít. Postup pro registraci Galerie obrázků sdílené funkce:

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

Může trvat několik minut, než zaregistrovat funkci. Můžete zkontrolovat průběh pomocí:

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>Vytvoření galerie obrázků 

Galerie obrázků je primární prostředek, který používá k povolení sdílení imagí. Galerie názvy musí být jedinečné v rámci vašeho předplatného. Vytvoření galerie obrázků s využitím [az sig vytvořit](/cli/azure/sig#az-sig-create). Následující příklad vytvoří galerii s názvem *myGallery* v *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Vytvoření definice bitové kopie

Vytvoření definice počáteční image v galerii pomocí [vytvoření sig az image definice](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>Vytvoření image verze 
 
Vytvoření verze Image podle potřeby pomocí [az image Galerie vytvořit image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Je potřeba předat ID spravované image se má použít jako základ pro vytvoření verze image. Můžete použít [az image list](/cli/azure/image?view#az-image-list) zobrazíte informace o imagích, které jsou ve skupině prostředků. V tomto příkladu verzi naše image je *1.0.0* a budeme vytvářet 5 celkový počet replik v *střed USA – západ*, *střed USA – jih* a východní USA 2 * oblastech.

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

