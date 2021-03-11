---
title: Klonování spravované image na verzi Image pomocí Azure CLI
description: Přečtěte si, jak naklonovat spravovanou bitovou kopii na verzi image v galerii sdílených imagí pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: cae82072785838d410453b2eb83685905b0ba04e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553778"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Klonování spravované image na verzi Image pomocí Azure CLI
Pokud máte existující spravovanou bitovou kopii, kterou byste chtěli klonovat do galerie sdílených imagí, můžete vytvořit image galerie sdílených imagí přímo ze spravované image. Jakmile otestujete novou bitovou kopii, můžete zdrojovou spravovanou bitovou kopii odstranit. Můžete také migrovat ze spravované image do galerie sdílených imagí pomocí [prostředí PowerShell](image-version-managed-image-powershell.md).

Obrázky v galerii obrázků mají dvě komponenty, které vytvoříme v tomto příkladu:
- **Definice obrázku** obsahuje informace o imagi a požadavcích na jejich použití. To zahrnuje, zda se jedná o obrázek Windows nebo Linux, specializované nebo zobecněné, poznámky k verzi a minimální a maximální požadavky na paměť. Je definicí typu obrázku. 
- **Verze image** je ta, která se používá k vytvoření virtuálního počítače při použití Galerie sdílených imagí. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Když vytvoříte virtuální počítač, použije se k vytvoření nového disku pro virtuální počítač verze image. Verze bitové kopie lze použít několikrát.


## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku musíte mít existující [galerii sdílených imagí](shared-images-cli.md). 

K dokončení příkladu v tomto článku musíte mít existující spravovanou image zobecněného virtuálního počítače. Další informace najdete v tématu [zachycení spravované image](./linux/capture-image.md). Pokud spravovaná bitová kopie obsahuje datový disk, velikost datového disku nemůže být větší než 1 TB.

Při práci s tímto článkem nahraďte názvy skupin prostředků a virtuálních počítačů tam, kde je to potřeba.



## <a name="create-an-image-definition"></a>Vytvoření definice obrázku

Vzhledem k tomu, že jsou spravované image vždycky generalizované, vytvoří se definice image s použitím `--os-state generalized` generalizované image.

Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. 

Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](./shared-image-galleries.md#image-definitions).

Vytvořte definici obrázku v galerii pomocí [AZ SIG image-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

V tomto příkladu se definice image jmenuje *myImageDefinition* a je určena pro [zobecněnou](./shared-image-galleries.md#generalized-and-specialized-images) bitovou kopii operačního systému Linux. Pokud chcete vytvořit definici imagí pomocí operačního systému Windows, použijte `--os-type Windows` . 

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state generalized
```


## <a name="create-the-image-version"></a>Vytvoření verze image

Vytvořte verze pomocí [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create). Abyste mohli použít jako základ pro vytvoření verze image, budete muset předat ID spravované image. K získání ID imagí můžete použít [seznam AZ image list](/cli/azure/image?view#az-image-list) . 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu je verze naší image *1.0.0* a my vytvoříme jednu repliku v *střed USA – jih* oblasti a 1 repliku v *východní USA 2* oblasti pomocí redundantního úložiště zóny. Při výběru cílových oblastí pro replikaci nezapomeňte, že je také nutné zahrnout *zdrojovou* oblast jako cíl pro replikaci.

Předejte ID spravované image do `--managed-image` parametru.


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> Aby bylo možné použít stejnou spravovanou bitovou kopii k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image.
>
> Můžete také uložit všechny repliky verze image v [zóně redundantního úložiště](../storage/common/storage-redundancy.md) přidáním `--storage-account-type standard_zrs` při vytváření verze image.
>

## <a name="next-steps"></a>Další kroky

Vytvořte virtuální počítač z [generalizované verze image](vm-generalized-image-version-cli.md).

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).