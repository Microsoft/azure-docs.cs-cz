---
title: Vytvořit sdílené bitové kopie virtuálního počítače pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak pomocí prostředí Azure PowerShell k vytvoření image sdíleného virtuálního počítače v Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 0a44f7d9c18e406850e2dbfb091088be0b8c2113
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235895"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Vytvořením Galerie sdílené bitové kopie pomocí Azure Powershellu 

A [Galerie obrázků Shared](shared-image-galleries.md) zjednodušuje vlastní image pro sdílení obsahu napříč vaší organizací. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní Image můžete použít ke spuštění úlohy nasazení, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie obrázků Shared umožňuje sdílení vlastních imagí virtuálních počítačů s jinými uživateli ve vaší organizaci, a to v rámci nebo napříč oblastmi v rámci tenanta služby AAD. Vybrat obrázky, které chcete sdílet, oblasti, které chcete provést, je k dispozici v a který chcete sdílet. Můžete vytvořit více galerií, takže je možné logicky seskupit sdílené bitové kopie. 

Galerie je prostředku nejvyšší úrovně, která poskytuje řízení přístupu na úplné na základě rolí (RBAC). Image může být označené verzí, a je možné replikovat každý verze image na jiný oblastí Azure. Galerie funguje pouze s spravované Image.

Galerie obrázků sdílené funkce má více typů prostředků. Můžeme se pomocí nebo sestavení v tomto článku:

| Resource | Popis|
|----------|------------|
| **Spravované image** | Toto je základní image, který můžete používat samostatně nebo použít k vytvoření **verze image** v Galerie obrázků. Vytváření spravovaných imagí z generalizovaného virtuálních počítačů. Spravované image je speciální typ virtuálního pevného disku, který je možné vytvořit několik virtuálních počítačů a můžete teď použít k vytvoření verze sdílené bitové kopie. |
| **Galerie obrázků** | Na webu Azure Marketplace, jako jsou **Galerie obrázků** je úložiště pro správu a sdílení imagí, ale vy řídíte, kdo má přístup. |
| **Definici Image** | Bitové kopie jsou definovány v rámci Galerie a budou mít informace o požadavcích pro interní použití a image. To zahrnuje, jestli obrázek je Windows nebo Linux, poznámky k verzi a požadavky na minimální a maximální paměť. Je to definice typu bitové kopie. |
| **Verze bitové kopie** | **Verze image** se používá k vytvoření virtuálního počítače, když použijete galerii. Podle potřeby pro vaše prostředí můžete mít více verze Image. Při použití, jako jsou spravované image **verze image** vytvoření virtuálního počítače, verze image slouží k vytvoření nové disky pro virtuální počítač. Verze Image můžete použít více než jednou. |

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

K dokončení příkladu v tomto článku, musíte mít existující spravované image. Můžete postupovat podle [kurzu: Vytvoření vlastní image virtuálního počítače Azure pomocí Azure Powershellu](tutorial-custom-images.md) ji vytvořit v případě potřeby. Pokud spravované image obsahuje datový disk, velikost datového disku nemůže být větší než 1 TB.

Při nahrazení projdete tohoto článku, skupinu prostředků a virtuální počítač příslušných místech názvy.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]
 
## <a name="create-vms-from-an-image"></a>Vytvoření virtuálních počítačů z image

Po dokončení se verze image můžete vytvořit jeden nebo více nových virtuálních počítačů. Pomocí parametru zjednodušené nastavit pro [rutiny New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) rutiny, stačí zadat ID bitové kopie verze image. 

Tento příklad vytvoří virtuální počítač s názvem *myVMfromImage*v *myResourceGroup* v *USA – východ* datového centra.

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $imageVersion.Id `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Další postup
[Image Builder pro Azure (preview)](image-builder-overview.md) můžou usnadnit automatizaci vytvoření verze image, můžete ho použít i aktualizovat a [vytvořit novou verzi image z existující verze image](image-builder-gallery-update-image-version.md). 

Můžete také vytvořit Galerie obrázků sdílených prostředků pomocí šablon. Nejsou k dispozici několik šablon rychlý start Azure: 

- [Vytvořit sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice Image v galerii sdílené bitové kopie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření Image verze v sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z Image verze](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o galeriích sdílené bitové kopie, najdete v článku [přehled](shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [Poradce při potížích s sdílené Galerie obrázků](troubleshooting-shared-images.md).

