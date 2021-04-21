---
title: CLI – vytvoření image ze snímku nebo spravovaného disku v galerii sdílených imagí
description: Naučte se, jak vytvořit image ze snímku nebo spravovaného disku v galerii sdílených imagí pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2dc6d99b8b1c913479fc584b52f6ff919dfac675
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792286"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Vytvoření image ze spravovaného disku nebo snímku v galerii sdílených imagí pomocí Azure CLI

Pokud máte existující snímek nebo spravovaný disk, který byste chtěli migrovat do sdílené Galerie imagí, můžete vytvořit image galerie sdílených imagí přímo ze spravovaného disku nebo snímku. Po otestování nové image můžete zdrojový spravovaný disk nebo snímek odstranit. Můžete také vytvořit image ze spravovaného disku nebo snímku v galerii sdílených imagí pomocí [Azure PowerShell](image-version-snapshot-powershell.md).

Obrázky v galerii obrázků mají dvě komponenty, které vytvoříme v tomto příkladu:
- **Definice obrázku** obsahuje informace o imagi a požadavcích na jejich použití. To zahrnuje, zda se jedná o obrázek Windows nebo Linux, specializované nebo zobecněné, poznámky k verzi a minimální a maximální požadavky na paměť. Je definicí typu obrázku. 
- **Verze image** je ta, která se používá k vytvoření virtuálního počítače při použití Galerie sdílených imagí. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Když vytvoříte virtuální počítač, použije se k vytvoření nového disku pro virtuální počítač verze image. Verze bitové kopie lze použít několikrát.


## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku musíte mít snímek nebo spravovaný disk. 

Pokud chcete zahrnout datový disk, velikost datového disku nemůže být větší než 1 TB.

Při práci s tímto článkem nahraďte názvy prostředků tam, kde je to potřeba.

## <a name="find-the-snapshot-or-managed-disk"></a>Vyhledání snímku nebo spravovaného disku 

Seznam snímků, které jsou k dispozici ve skupině prostředků, můžete zobrazit pomocí [AZ Snapshot list](/cli/azure/snapshot#az_snapshot_list). 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Místo snímku můžete použít také spravovaný disk. Pokud chcete získat spravovaný disk, použijte příkaz [AZ disk list](/cli/azure/disk#az_disk_list). 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Jakmile budete mít ID snímku nebo spravovaného disku a přiřadíte ho k proměnné s názvem `$source` pro pozdější použití.

Stejný postup můžete použít k získání všech datových disků, které chcete zahrnout do bitové kopie. Přiřaďte je proměnným a pak tyto proměnné použijte později při vytváření verze image.


## <a name="find-the-gallery"></a>Najít galerii

K vytvoření definice obrázku budete potřebovat informace o galerii imagí.

Seznam informací o dostupných galeriích imagí pomocí [AZ SIG list](/cli/azure/sig#az_sig_list). Poznamenejte si název galerie, se kterou má Galerie použít, aby se později používala.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Vytvoření definice obrázku

Definice obrázků vytvoří logické seskupení obrázků. Používají se ke správě informací o imagi. Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. 

Při vytváření definice obrázku se ujistěte, že jsou všechny správné informace. V tomto příkladu předpokládáme, že je snímek nebo spravovaný disk z virtuálního počítače, který se používá, a zatím není zobecněný. Pokud se spravovaný disk nebo snímek převzal na zobecněný operační systém (po spuštění nástroje Sysprep pro Windows nebo [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` nebo `-deprovision+user` Linux), změňte na `-OsState` `generalized` . 

Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](./shared-image-galleries.md#image-definitions).

Vytvořte definici obrázku v galerii pomocí [AZ SIG image-definition Create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

V tomto příkladu se definice image jmenuje *myImageDefinition* a je určena pro [specializovanou](./shared-image-galleries.md#generalized-and-specialized-images) image operačního systému Linux. Pokud chcete vytvořit definici imagí pomocí operačního systému Windows, použijte `--os-type Windows` . 

V tomto příkladu má Galerie název *myGallery*, je ve skupině prostředků *myGalleryRG* a název definice image bude *mImageDefinition*.

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
   --os-state specialized
```


## <a name="create-the-image-version"></a>Vytvoření verze image

Vytvoření verze Image pomocí [AZ Image Galerie vytvořit-Image-Version](/cli/azure/sig/image-version#az_sig_image_version_create) 

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu je verze naší image *1.0.0* a my vytvoříme jednu repliku v *střed USA – jih* oblasti a 1 repliku v *východní USA 2* oblasti pomocí redundantního úložiště zóny. Při volbě cílových oblastí pro replikaci nezapomeňte, že je také nutné zahrnout *zdrojovou* oblast spravovaného disku nebo snímku jako cíl pro replikaci.

Předejte ID snímku nebo spravovaný disk v `--os-snapshot` parametru.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Pokud chcete zahrnout datové disky do bitové kopie, musíte zahrnout `--data-snapshot-luns` Parametr nastavený na číslo logické jednotky a `--data-snapshots` nastavit na ID datového disku nebo snímku.

> [!NOTE]
> Aby bylo možné použít stejnou spravovanou bitovou kopii k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image.
>
> Můžete také uložit všechny repliky verze image v [zóně redundantního úložiště](../storage/common/storage-redundancy.md) přidáním `--storage-account-type standard_zrs` při vytváření verze image.
>

## <a name="next-steps"></a>Další kroky

Vytvoří virtuální počítač ze [specializované verze image](vm-specialized-image-version-cli.md).

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).