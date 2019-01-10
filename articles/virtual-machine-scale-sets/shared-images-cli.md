---
title: Používat sdílený Image virtuálního počítače k vytvoření škálovací sady v Azure | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Azure CLI k vytvoření sdílené bitové kopie virtuálního počítače má použít k nasazení škálovací sady virtuálních počítačů v Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 41e2eee491141be3a7e39a70bccf8440890e2982
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54186494"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Vytvoření a použití sdílené bitové kopie pro škálovací sady virtuálních počítačů pomocí Azure CLI 2.0

Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. [Sdílené Galerie obrázků](shared-image-galleries.md) výrazně zjednodušuje vlastní image pro sdílení obsahu napříč vaší organizací. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. Galerie obrázků Shared umožňuje sdílení vlastních imagí virtuálních počítačů s jinými uživateli ve vaší organizaci, a to v rámci nebo napříč oblastmi v rámci tenanta služby AAD. Vybrat obrázky, které chcete sdílet, oblasti, které chcete provést, je k dispozici v a který chcete sdílet. Můžete vytvořit více galerií, takže je možné logicky seskupit sdílené bitové kopie. Galerie je prostředku nejvyšší úrovně, která poskytuje řízení přístupu na úplné na základě rolí (RBAC). Image může být označené verzí, a je možné replikovat každý verze image na jiný oblastí Azure. Galerie funguje pouze s spravované Image. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořením Galerie sdílené bitové kopie
> * Vytvoření definice sdílené bitové kopie
> * Vytvořit novou verzi sdílené bitové kopie
> * Vytvoření virtuálního počítače ze sdílené bitové kopie

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


>[!NOTE]
> Tento článek vás provede procesem použití generalizované image spravovaného. Vytvoření škálovací sady z image specializovaného virtuálního počítače se nepodporuje.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento článek vyžaduje použití Azure CLI verze 2.0.46 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Vytvoření škálovací sady z vlastní image virtuálního počítače
Vytvořte škálovací sadu pomocí příkazu [az vmss create](/cli/azure/vmss#az-vmss-create). Místo image platformy, jako je například *UbuntuLTS* nebo *CentOS*, zadejte název své vlastní image virtuálního počítače. Následující příklad vytvoří škálovací sadu *myScaleSet*, která používá vlastní image *myImage* z předchozího kroku:

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
Pokud chcete odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí příkazu [az group delete](/cli/azure/group#az_group_delete). Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Další postup

Můžete také vytvořit Galerie obrázků sdílených prostředků pomocí šablon. Nejsou k dispozici několik šablon rychlý start Azure: 

- [Vytvořit sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice Image v galerii sdílené bitové kopie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření Image verze v sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z Image verze](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Pokud narazíte na nějaké problémy, můžete si [řešení potíží s galerií sdílené bitové kopie](troubleshooting-shared-images.md).
