---
title: Používat sdílený Image virtuálního počítače k vytvoření škálovací sady v Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit sdílené bitové kopie virtuálního počítače má použít k nasazení škálovací sady virtuálních počítačů v Azure pomocí Azure Powershellu.
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
ms.date: 12/13/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 7e67e7836b1d80e623a11e552c81750bc6133205
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981634"
---
# <a name="preview-create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Verze Preview: Vytvoření a použití sdílené bitové kopie pro škálovací sady virtuálních počítačů pomocí Azure Powershellu

Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. Služby Galerie obrázků Shared výrazně zjednodušuje vlastní image pro sdílení obsahu napříč vaší organizací. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie obrázků Shared umožňuje sdílení vlastních imagí virtuálních počítačů s jinými uživateli ve vaší organizaci, a to v rámci nebo napříč oblastmi v rámci tenanta služby AAD. Vybrat obrázky, které chcete sdílet, oblasti, které chcete provést, je k dispozici v a který chcete sdílet. Můžete vytvořit více galerií, takže je možné logicky seskupit sdílené bitové kopie. 

Galerie je prostředku nejvyšší úrovně, která poskytuje řízení přístupu na úplné na základě rolí (RBAC). Image může být označené verzí, a je možné replikovat každý verze image na jiný oblastí Azure. Galerie funguje pouze s spravované Image. 

Galerie obrázků sdílené funkce má více typů prostředků. Můžeme se pomocí nebo sestavení v tomto článku:

| Prostředek | Popis|
|----------|------------|
| **Spravované image** | Toto je základní image, který můžete používat samostatně nebo použít k vytvoření **verze image** v Galerie obrázků. Vytváření spravovaných imagí z generalizovaného virtuálních počítačů. Spravované image je speciální typ virtuálního pevného disku, který je možné vytvořit několik virtuálních počítačů a můžete teď použít k vytvoření verze sdílené bitové kopie. |
| **Galerie obrázků** | Na webu Azure Marketplace, jako jsou **Galerie obrázků** je úložiště pro správu a sdílení imagí, ale vy řídíte, kdo má přístup. |
| **Definici Image** | Bitové kopie jsou definovány v rámci Galerie a budou mít informace o požadavcích pro interní použití a image. To zahrnuje, jestli obrázek je Windows nebo Linux, poznámky k verzi a požadavky na minimální a maximální paměť. Je to definice typu bitové kopie. |
| **Verze bitové kopie** | **Verze image** se používá k vytvoření virtuálního počítače, když použijete galerii. Podle potřeby pro vaše prostředí můžete mít více verze Image. Při použití, jako jsou spravované image **verze image** vytvoření virtuálního počítače, verze image slouží k vytvoření nové disky pro virtuální počítač. Verze Image můžete použít více než jednou. |

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

## <a name="before-you-begin"></a>Před zahájením

Následující postup podrobně popisuje přeměnu existujícího virtuálního počítače na opětovně použitelnou vlastní image, pomocí které můžete vytvářet nové instance virtuálních počítačů.

K dokončení příkladu v tomto článku, musíte mít existující spravované image. Můžete postupovat podle [kurzu: Vytvoření a použití vlastní image pro škálovací sady virtuálních počítačů pomocí Azure Powershellu](tutorial-use-custom-image-powershell.md) ji vytvořit v případě potřeby. Při procházení článku, nahraďte skupinu prostředků a virtuální počítač příslušných místech názvy.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Vytvoření škálovací sady z verze sdílené bitové kopie

Vytvoření virtuálního počítače škálovací sady s [New-AzVmss](/powershell/module/az.compute/new-azvmss). Následující příklad vytvoří škálovací sady z nové verze image v datacentru na západní USA. Automaticky se vytvoří síťové prostředky Azure pro virtuální síť, veřejná IP adresa a nástroj pro vyrovnávání zatížení. Po zobrazení výzvy nastavte vlastní pověření správce pro instance virtuálních počítačů ve škálovací sadě:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName myVMSSRG `
  -Location 'South Central US' `
  -VMScaleSetName 'myScaleSet' `
  -VirtualNetworkName 'myVnet' `
  -SubnetName 'mySubnet'`
  -PublicIpAddressName 'myPublicIPAddress' `
  -LoadBalancerName 'myLoadBalancer' `
  -UpgradePolicyMode 'Automatic' `
  -ImageName $imageVersion.Id
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) rutina pro odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myGalleryRG
Remove-AzResourceGroup -Name myVMSSRG
```


## <a name="next-steps"></a>Další postup

Můžete také vytvořit Galerie obrázků sdílených prostředků pomocí šablon. Nejsou k dispozici několik šablon rychlý start Azure: 

- [Vytvořit sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice Image v galerii sdílené bitové kopie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření Image verze v sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z Image verze](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o galeriích sdílené bitové kopie, najdete v článku [přehled](shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [Poradce při potížích s sdílené Galerie obrázků](troubleshooting-shared-images.md).
