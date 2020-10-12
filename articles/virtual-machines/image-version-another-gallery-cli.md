---
title: Kopírování verze image z jiné galerie pomocí rozhraní příkazového řádku
description: Kopírování verze image z jiné galerie pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e8aabcd1c68272a78b3c1fe88913c5a62496f681
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88225831"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Kopírování obrázku z jiné galerie pomocí Azure CLI

Pokud máte ve vaší organizaci více galerií, můžete také vytvářet verze imagí z existujících verzí imagí uložených v jiných galeriích. Můžete mít například galerii pro vývoj a testování pro vytváření a testování nových imagí. Až budou připravené k použití v produkčním prostředí, můžete je pomocí tohoto příkladu zkopírovat do produkční galerie. Můžete také vytvořit obrázek z obrázku v jiné galerii pomocí [Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku musíte mít existující galerii zdrojů, definici image a verzi image. Měli byste mít také cílovou galerii. 

Verze zdrojového obrázku se musí replikovat do oblasti, kde se nachází vaše cílová galerie. 

Při práci s tímto článkem nahraďte názvy prostředků tam, kde je to potřeba.



## <a name="get-information-from-the-source-gallery"></a>Získat informace z Galerie zdrojů

Z definice zdrojového obrázku budete potřebovat informace, abyste si mohli vytvořit jeho kopii v nové galerii.

Seznam informací o dostupných galeriích imagí pomocí [AZ SIG list](/cli/azure/sig#az-sig-list) , kde najdete informace o zdrojové galerii.

```azurecli-interactive 
az sig list -o table
```

Seznamte se s definicemi obrázků v galerii pomocí [AZ SIG image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list). V tomto příkladu hledáme definice obrázků v galerii s názvem *myGallery* ve skupině prostředků *myGalleryRG* .

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Vypíšete verze obrázku v galerii a pomocí [seznamu AZ SIG Image-Version](/cli/azure/sig/image-version#az-sig-image-version-list) vyhledáte verzi image, kterou chcete zkopírovat do nové galerie. V tomto příkladu hledáme všechny verze imagí, které jsou součástí definice image *myImageDefinition* .

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Jakmile budete mít všechny potřebné informace, můžete získat ID verze zdrojového obrázku pomocí [AZ SIG Image-Version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Vytvoření definice image 

Musíte vytvořit definici image, která odpovídá definici image verze vašeho zdrojového obrázku. V nové galerii můžete zobrazit všechny informace, které potřebujete k opětovnému vytvoření definice image, pomocí [AZ SIG image-definition show](/cli/azure/sig/image-definition#az-sig-image-definition-show).

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

Výstup bude vypadat zhruba takto:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Vytvořte novou definici obrázku ve vaší nové galerii s použitím informací z výstupu výše.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>Vytvoření verze image

Vytvořte verze pomocí [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create). Abyste mohli použít jako základ pro vytvoření verze image, budete muset předat ID spravované image. Pomocí [AZ image list](/cli/azure/image?view#az-image-list) můžete získat informace o imagích, které jsou ve skupině prostředků. 

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu je verze naší image *1.0.0* a my vytvoříme jednu repliku v *střed USA – jih* oblasti a 1 repliku v *východní USA* oblasti pomocí redundantního úložiště zóny.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> Aby bylo možné použít stejnou spravovanou bitovou kopii k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image.
>
> Image můžete ukládat do služby Premium Storage tím, že přidáte `--storage-account-type  premium_lrs` nebo zadáte [redundantní úložiště do zóny](../storage/common/storage-redundancy.md) , a to přidáním, `--storage-account-type  standard_zrs` Když vytvoříte verzi image.
>

## <a name="next-steps"></a>Další kroky

Vytvořte virtuální počítač z [zobecněné](vm-generalized-image-version-cli.md) nebo [specializované](vm-specialized-image-version-cli.md) verze image.

Také si vyzkoušejte [Azure image Builder (Preview)](./linux/image-builder-overview.md) , které vám pomůžou automatizovat vytváření verzí imagí, můžete ho dokonce použít k aktualizaci a [Vytvoření nové verze image z existující verze image](./linux/image-builder-gallery-update-image-version.md). 

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).
