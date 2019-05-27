---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/30/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 9647cdd584b53f581f46f728ca2d08f9a113ce92
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156144"
---
## <a name="before-you-begin"></a>Než začnete

K dokončení příkladu v tomto článku, musíte mít existující spravované image zobecněného virtuálního počítače. Další informace najdete v tématu [kurzu: Vytvoření vlastní image virtuálního počítače Azure pomocí rozhraní příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Pokud spravované image obsahuje datový disk, velikost datového disku nemůže být větší než 1 TB.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/bash](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="create-an-image-gallery"></a>Vytvoření galerie obrázků 

Galerie obrázků je primární prostředek, který používá k povolení sdílení imagí. Povolené znaky pro název galerie jsou malá a velká písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky.   Galerie názvy musí být jedinečné v rámci vašeho předplatného. 

Vytvoření galerie obrázků s využitím [az sig vytvořit](/cli/azure/sig#az-sig-create). Následující příklad vytvoří galerii s názvem *myGallery* v *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Vytvoření definice bitové kopie

Definice Image vytvořte logické seskupení pro bitové kopie. Používají se ke správě informace o verzích bitové kopie, které jsou vytvořeny v nich. Názvy imagí definice mohou být tvořené malá a velká písmena, číslice, tečky, pomlčky a tečky. Další informace o hodnotách, můžete použít definici image, najdete v části [obrázku definice](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

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

Vytvoření verze Image podle potřeby pomocí [az image Galerie vytvořit image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Je potřeba předat ID spravované image se má použít jako základ pro vytvoření verze image. Můžete použít [az image list](/cli/azure/image?view#az-image-list) zobrazíte informace o imagích, které jsou ve skupině prostředků. 

Povolené znaky pro verze image jsou čísla a tečky. Čísla musí být v rozsahu 32bitového celého čísla. Formát: *Hlavní verze*. *Podverze*. *Oprava*.

V tomto příkladu verzi naše image je *1.0.0* a budeme vytvářet 2 repliky v *střed USA – západ* oblast, 1 repliky v *střed USA – jih* oblasti a 1 repliky v *USA – východ 2* oblasti.


```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> Budete muset počkat na verzi image, aby zcela dokončit právě vytvořené a replikované před stejné spravované image můžete vytvořit jinou verzi image.
>
> Můžete také ukládat vaše verze image v [Zónově redundantní úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) přidáním `--storage-account-type standard_zrs` při vytváření verze image.
>
