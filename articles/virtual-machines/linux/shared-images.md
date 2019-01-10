---
title: Vytvoření sdílené bitové kopie virtuálního počítače pomocí Azure CLI | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak používat rozhraní příkazového řádku Azure k vytvoření sdílené bitové kopie virtuálního počítače v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 08c7e84a27a4c8e9527083360dbd08296bd86775
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190047"
---
# <a name="preview-create-a-shared-image-gallery-with-the-azure-cli"></a>Verze Preview: Vytvoření galerie sdílené bitové kopie pomocí Azure CLI

A [Galerie obrázků Shared](shared-image-galleries.md) zjednodušuje vlastní image pro sdílení obsahu napříč vaší organizací. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie obrázků Shared umožňuje sdílení vlastních imagí virtuálních počítačů s jinými uživateli ve vaší organizaci, a to v rámci nebo napříč oblastmi v rámci tenanta služby AAD. Vybrat obrázky, které chcete sdílet, oblasti, které chcete provést, je k dispozici v a který chcete sdílet. Můžete vytvořit více galerií, takže je možné logicky seskupit sdílené bitové kopie. 

Galerie je prostředku nejvyšší úrovně, která poskytuje řízení přístupu na úplné na základě rolí (RBAC). Image může být označené verzí, a je možné replikovat každý verze image na jiný oblastí Azure. Galerie funguje pouze s spravované Image.

Galerie obrázků sdílené funkce má více typů prostředků. Můžeme se pomocí nebo sestavení v tomto článku:

| Prostředek | Popis|
|----------|------------|
| **Spravované image** | Toto je základní image, který můžete používat samostatně nebo použít k vytvoření **verze image** v Galerie obrázků. Vytváření spravovaných imagí z generalizovaného virtuálních počítačů. Spravované image je speciální typ virtuálního pevného disku, který je možné vytvořit několik virtuálních počítačů a můžete teď použít k vytvoření verze sdílené bitové kopie. |
| **Galerie obrázků** | Na webu Azure Marketplace, jako jsou **Galerie obrázků** je úložiště pro správu a sdílení imagí, ale vy řídíte, kdo má přístup. |
| **Definici Image** | Bitové kopie jsou definovány v rámci Galerie a budou mít informace o požadavcích pro interní použití a image. To zahrnuje, jestli obrázek je Windows nebo Linux, poznámky k verzi a požadavky na minimální a maximální paměť. Je to definice typu bitové kopie. |
| **Verze bitové kopie** | **Verze image** se používá k vytvoření virtuálního počítače, když použijete galerii. Podle potřeby pro vaše prostředí můžete mít více verze Image. Při použití, jako jsou spravované image **verze image** vytvoření virtuálního počítače, verze image slouží k vytvoření nové disky pro virtuální počítač. Verze Image můžete použít více než jednou. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače z image pomocí verze [az vm vytvořit](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   -g myGalleryRG \
   -n myVM \
   --image "/subscriptions/<subscription-ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
   --generate-ssh-keys
```

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]



## <a name="next-steps"></a>Další postup
Můžete také vytvořit Galerie obrázků sdílených prostředků pomocí šablon. Nejsou k dispozici několik šablon rychlý start Azure: 

- [Vytvořit sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice Image v galerii sdílené bitové kopie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření Image verze v sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z Image verze](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o galeriích sdílené bitové kopie, najdete v článku [přehled](shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [Poradce při potížích s sdílené Galerie obrázků](troubleshooting-shared-images.md).
