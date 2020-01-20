---
title: Použití sdílených imagí virtuálních počítačů k vytvoření sady škálování v Azure
description: Naučte se používat rozhraní příkazového řádku Azure CLI k vytváření sdílených imagí virtuálních počítačů, které se použijí pro nasazení služby Virtual Machine Scale Sets v Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 83b3d5c904a65b28482acf8b685c939493c8c03b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276271"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Vytvoření a použití sdílených imagí pro Virtual Machine Scale Sets pomocí Azure CLI 2,0

Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. [Galerie sdílených imagí](shared-image-galleries.md) značně zjednodušují sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. Galerie sdílených imagí umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci v rámci oblastí nebo napříč nimi v rámci tenanta AAD. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. Můžete vytvořit několik galerií, abyste mohli logicky seskupovat sdílené image. Galerie je prostředek nejvyšší úrovně, který poskytuje úplné řízení přístupu na základě role (RBAC). Bitové kopie můžou být ve verzi a můžete se rozhodnout pro replikaci každé verze image na jinou sadu oblastí Azure. Galerie funguje pouze se spravovanými bitovými kopiemi. 

>[!NOTE]
> Tento článek vás provede procesem použití generalizované spravované image. Vytvoření škálovací sady z image specializovaného virtuálního počítače se nepodporuje.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Vytvoření škálovací sady z vlastní image virtuálního počítače
Vytvořte sadu škálování pomocí [`az vmss create`](/cli/azure/vmss#az-vmss-create). Místo image platformy, jako je například *UbuntuLTS* nebo *CentOS*, zadejte název své vlastní image virtuálního počítače. Následující příklad vytvoří škálovací sadu *myScaleSet*, která používá vlastní image *myImage* z předchozího kroku:

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
Pokud chcete odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí příkazu [az group delete](/cli/azure/group). Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Další kroky

Pomocí šablon můžete také vytvořit prostředek Galerie sdílených imagí. K dispozici je několik šablon rychlého startu Azure: 

- [Vytvoření galerie sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice obrázku v galerii sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze image v galerii sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z verze image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Pokud narazíte na nějaké problémy, můžete [řešit problémy s galerií sdílených imagí](troubleshooting-shared-images.md).
