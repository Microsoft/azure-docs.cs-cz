---
title: Vytvoření škálovací sady v Azure pomocí sdílených ibi virtuálních počítačích
description: Zjistěte, jak pomocí azure cli vytvořit sdílené image virtuálních počítačů pro nasazení škálovacích sad virtuálních strojů v Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 83b3d5c904a65b28482acf8b685c939493c8c03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276271"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Vytváření a používání sdílených ibipro škálovací sady virtuálních strojů pomocí azure CLI 2.0

Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. [Sdílené galerie obrázků](shared-image-galleries.md) výrazně zjednodušují vlastní sdílení obrázků v celé organizaci. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. Galerie sdílených obrázků umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci, v rámci nebo napříč oblastmi, v rámci klienta AAD. Zvolte, které obrázky chcete sdílet, ve kterých oblastech je chcete zpřístupnit a s kým je chcete sdílet. Můžete vytvořit více galerií, abyste mohli logicky seskupit sdílené obrázky. Galerie je prostředek nejvyšší úrovně, který poskytuje úplné řízení přístupu na základě rolí (RBAC). Image může být verzí a můžete se rozhodnout replikovat každou verzi bitové kopie do jiné sady oblastí Azure. Galerie pracuje pouze se spravovanými obrázky. 

>[!NOTE]
> Tento článek vás provede procesem použití generalizované spravované bitové kopie. Vytvoření škálovací sady z image specializovaného virtuálního počítače se nepodporuje.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Vytvoření škálovací sady z vlastní image virtuálního počítače
Vytvořte škálovací [`az vmss create`](/cli/azure/vmss#az-vmss-create)sadu pomocí . Místo image platformy, jako je například *UbuntuLTS* nebo *CentOS*, zadejte název své vlastní image virtuálního počítače. Následující příklad vytvoří škálovací sadu *myScaleSet*, která používá vlastní image *myImage* z předchozího kroku:

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>Vyčištění prostředků
Chcete-li odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky [odstraněním skupiny AZ](/cli/azure/group). Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Další kroky

Prostředek Galerie sdílených obrázků můžete také vytvořit pomocí šablon. K dispozici je několik šablon Azure QuickStart: 

- [Vytvoření galerie sdílených obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice obrazu ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze obrázku ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního virtuálního virtuálního mísy z verze bitové kopie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Pokud narazíte na nějaké problémy, můžete [řešit sdílené galerie obrázků](troubleshooting-shared-images.md).
