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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814813"
---
## <a name="before-you-begin"></a>Než začnete

Chcete-li dokončit příklad v tomto článku, musíte mít existující spravované image generalizovaného virtuálního aplikace. Další informace [najdete v tématu Kurz: Vytvoření vlastní image virtuálního počítače Azure s Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Pokud spravovaná bitová kopie obsahuje datový disk, velikost datového disku nesmí být větší než 1 TB.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/bash](https://shell.azure.com/bash)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a použití příkazového příkazového příkazu místně, najdete [v tématu Instalace azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Vytvoření galerie obrázků 

Galerie obrázků je primární prostředek používaný pro povolení sdílení obrázků. Povolené znaky pro název galerie jsou velká nebo malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky.   Názvy galerií musí být v rámci předplatného jedinečné. 

Vytvořte galerii obrázků pomocí [az sig create](/cli/azure/sig#az-sig-create). Následující příklad vytvoří galerii s názvem *myGallery* v *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Vytvoření definice obrázku

Definice obrázků vytvářejí logické seskupení pro obrazy. Používají se ke správě informací o verzích bitových obrázků, které jsou v nich vytvořeny. Názvy definic obrázků mohou být tvořeny velkými nebo velkými písmeny, číslicemi, tečkami, pomlčkami a tečkami. Další informace o hodnotách, které můžete zadat pro definici obrazu, naleznete v [tématu Definice obrázků](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Vytvořte počáteční definici obrazu v galerii pomocí [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

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


## <a name="create-an-image-version"></a>Vytvoření verze obrázku 

Vytvořte verze obrazu podle potřeby pomocí [az galerie obrázků create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Budete muset předat ID spravované bitové kopie, abyste ji použili jako směrný plán pro vytvoření verze bitové kopie. [Pomocí seznamu obrázků az](/cli/azure/image?view#az-image-list) můžete získat informace o obrázcích, které jsou ve skupině prostředků. 

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32bitové celé číslo. Formát: *MajorVersion*. *MinorVersion*. *Záplata*.

V tomto příkladu je verze naší bitové kopie *1.0.0* a vytvoříme 2 repliky v oblasti *USA – západ– střed,* 1 repliku v oblasti *USA – střed a* 1 repliku v oblasti VÝCHODNÍ USA *2* pomocí zónově redundantního úložiště.


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
> Před použitím stejné spravované bitové kopie k vytvoření jiné verze bitové kopie je třeba počkat, až bude verze bitové kopie zcela dokončena.
>
> Všechny repliky verzí bitových bitových žerek `--storage-account-type standard_zrs` můžete také uložit do [zónového redundantního úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) přidáním při vytváření verze bitové kopie.
>

## <a name="share-the-gallery"></a>Sdílet galerii

Doporučujeme sdílet s ostatními uživateli na úrovni galerie. Chcete-li získat ID objektu galerie, použijte [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Použijte ID objektu jako obor spolu s e-mailovou adresou a [přiřazením role az,](/cli/azure/role/assignment#az-role-assignment-create) abyste uživateli poskytli přístup ke sdílené galerii obrázků.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


