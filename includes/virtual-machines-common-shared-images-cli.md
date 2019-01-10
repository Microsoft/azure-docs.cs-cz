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
ms.openlocfilehash: 547e0b08b2a57b4b9192f11907f77160142d4466
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192121"
---
## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/bash](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="before-you-begin"></a>Před zahájením

K dokončení příkladu v tomto článku, musíte mít existující spravované image zobecněného virtuálního počítače. Další informace najdete v tématu [kurzu: Vytvoření vlastní image virtuálního počítače Azure pomocí rozhraní příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

## <a name="preview-register-the-feature"></a>Verze Preview: Zaregistrovat funkci

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

