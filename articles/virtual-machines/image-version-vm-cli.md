---
title: Vytvoření image z virtuálního počítače pomocí Azure CLI
description: Naučte se vytvořit image v galerii sdílených imagí z virtuálního počítače v Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: ca354ee662ebad0cd514d4822794b056ee6f9850
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805373"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Vytvoření verze image z virtuálního počítače v Azure pomocí rozhraní příkazového řádku Azure

Pokud máte existující virtuální počítač, který byste chtěli použít k vytvoření několika identických virtuálních počítačů, můžete pomocí tohoto virtuálního počítače vytvořit image v galerii sdílených imagí pomocí Azure CLI. Můžete také vytvořit image z virtuálního počítače pomocí [Azure PowerShell](image-version-vm-powershell.md).

**Verze image** je to, co použijete k vytvoření virtuálního počítače při použití Galerie sdílených imagí. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Když použijete verzi image k vytvoření virtuálního počítače, verze image se použije k vytvoření disků pro nový virtuální počítač. Verze bitové kopie lze použít několikrát.


## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku musíte mít existující galerii sdílených imagí. 

Musíte mít taky existující virtuální počítač v Azure, ve stejné oblasti jako vaše galerie. 

Pokud má virtuální počítač připojený datový disk, velikost datového disku nemůže být větší než 1 TB.

Při práci s tímto článkem nahraďte názvy prostředků tam, kde je to potřeba.

## <a name="get-information-about-the-vm"></a>Získat informace o virtuálním počítači

Seznam virtuálních počítačů, které jsou k dispozici, můžete zobrazit pomocí [seznamu AZ VM list](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Jakmile budete znát název virtuálního počítače a k čemu se v něm skupina prostředků, Získejte ID virtuálního počítače pomocí [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Vytvoření definice obrázku

Definice obrázků vytvoří logické seskupení obrázků. Slouží ke správě informací o verzích imagí, které jsou v nich vytvořeny. 

Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. 

Ujistěte se, že je vaše definice image správným typem. Pokud jste virtuální počítač zobecněni (pomocí nástroje Sysprep pro Windows nebo waagent-devisioning pro Linux), měli byste vytvořit zobecněnou definici Image pomocí `--os-state generalized` . Pokud chcete virtuální počítač použít bez odebrání stávajících uživatelských účtů, vytvořte pomocí nástroje specializovanou definici image `--os-state specialized` .

Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](./shared-image-galleries.md#image-definitions).

Vytvořte definici obrázku v galerii pomocí [AZ SIG image-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

V tomto příkladu se definice image jmenuje *myImageDefinition* a je určena pro [specializovanou](./shared-image-galleries.md#generalized-and-specialized-images) image operačního systému Linux. Pokud chcete vytvořit definici imagí pomocí operačního systému Windows, použijte `--os-type Windows` . 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Vytvoření verze image

Z virtuálního počítače vytvořte verzi Image pomocí [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu je verze naší image *1.0.0* a v oblasti *středozápadní USA* se vytvoří 2 repliky, 1 replika v *střed USA – jih* oblasti a 1 replika v oblasti *východní USA 2* s použitím redundantního úložiště v zóně. Oblasti replikace musí zahrnovat oblast, ve které je umístěný zdrojový virtuální počítač.

`--managed-image`V tomto příkladu nahraďte hodnotu ID virtuálního počítače z předchozího kroku.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Aby bylo možné použít stejnou spravovanou bitovou kopii k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image.
>
> Image můžete ukládat do služby Premium Storage tím, že přidáte `--storage-account-type  premium_lrs` nebo zadáte [redundantní úložiště do zóny](../storage/common/storage-redundancy.md) , a to přidáním, `--storage-account-type  standard_zrs` Když vytvoříte verzi image.
>

## <a name="next-steps"></a>Další kroky

Vytvořte virtuální počítač z [generalizované image](vm-generalized-image-version-cli.md) pomocí Azure CLI.

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).