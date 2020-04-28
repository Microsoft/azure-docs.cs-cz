---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "66814813"
---
## <a name="before-you-begin"></a>Před zahájením

K dokončení příkladu v tomto článku musíte mít existující spravovanou image zobecněného virtuálního počítače. Další informace najdete v tématu [kurz: Vytvoření vlastní image virtuálního počítače Azure pomocí Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Pokud spravovaná bitová kopie obsahuje datový disk, velikost datového disku nemůže být větší než 1 TB.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete [https://shell.azure.com/bash](https://shell.azure.com/bash)na. Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Vytvoření galerie imagí 

Galerie imagí je primární prostředek, který slouží k povolení sdílení obrázků. Povolenými znaky pro název galerie jsou velká a malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky.   Názvy galerií musí být v rámci vašeho předplatného jedinečné. 

Pomocí [AZ SIG Create](/cli/azure/sig#az-sig-create)vytvořte galerii imagí. Následující příklad vytvoří galerii s názvem *myGallery* v *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Vytvoření definice obrázku

Definice obrázků vytvoří logické seskupení obrázků. Slouží ke správě informací o verzích imagí, které jsou v nich vytvořeny. Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Vytvořte počáteční definici obrázku v galerii pomocí [AZ SIG image-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>Vytvoření verze image 

Podle potřeby vytvořte verze Image pomocí [AZ Image Galerie vytvořit-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create). Abyste mohli použít jako základ pro vytvoření verze image, budete muset předat ID spravované image. Pomocí [AZ image list](/cli/azure/image?view#az-image-list) můžete získat informace o imagích, které jsou ve skupině prostředků. 

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu je verze naší image *1.0.0* a v oblasti *středozápadní USA* se vytvoří 2 repliky, 1 replika v *střed USA – jih* oblasti a 1 replika v oblasti *východní USA 2* s použitím redundantního úložiště v zóně.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> Aby bylo možné použít stejnou spravovanou bitovou kopii k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image.
>
> Můžete také uložit všechny repliky verze image v [zóně redundantního úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) přidáním `--storage-account-type standard_zrs` při vytváření verze image.
>

## <a name="share-the-gallery"></a>Sdílení galerie

Doporučujeme sdílet s ostatními uživateli na úrovni galerie. Chcete-li získat ID objektu galerie, použijte příkaz [AZ SIG show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Pomocí ID objektu jako oboru společně s e-mailovou adresou a [AZ role Assignment vytvořit](/cli/azure/role/assignment#az-role-assignment-create) poskytněte uživateli přístup k galerii sdílených imagí.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


