---
title: Vytváření sdílených galerií obrázků pomocí příkazového příkazového příkazu Azure
description: V tomto článku se dozvíte, jak pomocí azure cli vytvořit sdílenou image virtuálního počítače v Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c75d33f8310cfd143bf201cdac861954d07baf0b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758417"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Vytvoření sdílené galerie obrázků pomocí příkazového příkazového příkazu k Řešení<

[Galerie sdílených obrázků](shared-image-galleries.md) zjednodušuje vlastní sdílení obrázků v celé organizaci. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených obrázků umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci, v rámci nebo napříč oblastmi, v rámci klienta AAD. Zvolte, které obrázky chcete sdílet, ve kterých oblastech je chcete zpřístupnit a s kým je chcete sdílet. Můžete vytvořit více galerií, abyste mohli logicky seskupit sdílené obrázky. 

Galerie je prostředek nejvyšší úrovně, který poskytuje úplné řízení přístupu na základě rolí (RBAC). Image může být verzí a můžete se rozhodnout replikovat každou verzi bitové kopie do jiné sady oblastí Azure. Galerie pracuje pouze se spravovanými obrázky.

Funkce Galerie sdílených obrázků má více typů prostředků. Budeme používat nebo stavební tyto v tomto článku:

| Prostředek | Popis|
|----------|------------|
| **Spravovaná bitová kopie** | Jedná se o základní obrázek, který lze použít samostatně nebo použít k vytvoření **verze obrázku** v galerii obrázků. Spravované bitové kopie se vytvářejí z generalizovaných virtuálních měn. Spravovaná bitová kopie je speciální typ virtuálního pevného disku, který lze použít k vytvoření více virtuálních počítačů a nyní lze použít k vytvoření verzí sdílených bitových obrázků. |
| **Galerie obrázků** | Stejně jako Azure Marketplace, **galerie obrázků** je úložiště pro správu a sdílení bitových kopií, ale máte řízení, kdo má přístup. |
| **Definice obrázku** | Obrázky jsou definovány v galerii a nesou informace o obrázku a požadavky na jeho použití interně. To zahrnuje, zda je bitová kopie Windows nebo Linux, poznámky k verzi a minimální a maximální požadavky na paměť. Jedná se o definici typu obrazu. |
| **Verze obrázku** | **Verze bitové kopie** je to, co používáte k vytvoření virtuálního počítačů při použití galerie. Podle potřeby pro vaše prostředí můžete mít více verzí bitové kopie. Stejně jako spravovaná **image version** bitová kopie, když k vytvoření virtuálního počítače použijete verzi image k vytvoření nových disků pro virtuální počítače. Verze obrázků lze použít vícekrát. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální hosti z nejnovější verze image pomocí [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

Určitou verzi můžete také použít pomocí ID `--image` verze bitové kopie pro parametr. Chcete-li například použít obrázek verze `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` *1.0.0* typu: .

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Další kroky
[Azure Image Builder (preview)](image-builder-overview.md) může pomoci automatizovat vytváření verzí bitové kopie, můžete ji dokonce použít k aktualizaci a [vytvoření nové verze image z existující verze image](image-builder-gallery-update-image-version.md). 

Prostředky Galerie sdílených obrázků můžete také vytvořit pomocí šablon. K dispozici je několik šablon Azure QuickStart: 

- [Vytvoření galerie sdílených obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice obrazu ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze obrázku ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního virtuálního virtuálního mísy z verze bitové kopie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o sdílených galeriích obrázků naleznete v [tématu Přehled](shared-image-galleries.md). Pokud narazíte na problémy, [přečtěte si článek Poradce při potížích s galeriemi sdílených obrázků](troubleshooting-shared-images.md).
