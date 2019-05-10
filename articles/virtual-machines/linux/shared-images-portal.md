---
title: Vytvoření sdílené bitové kopie virtuálního počítače Azure pro Linux pomocí portálu | Dokumentace Microsoftu
description: Zjistěte, jak pomocí webu Azure portal k vytváření a sdílení imagí virtuálních počítačů.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 1b760612d8d9a5ed0817ce662ed190f3477cd125
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235849"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Vytvořením Galerie sdílené bitové kopie pomocí webu Azure portal

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


## <a name="before-you-begin"></a>Než začnete

K dokončení příkladu v tomto článku, musíte mít existující spravované image. Můžete postupovat podle [kurzu: Vytvoření vlastní image virtuálního počítače Azure pomocí Azure Powershellu](tutorial-custom-images.md) ji vytvořit v případě potřeby. Pokud spravované image obsahuje datový disk, velikost datového disku nemůže být větší než 1 TB.

Při nahrazení projdete tohoto článku, skupinu prostředků a virtuální počítač příslušných místech názvy.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>Vytvoření virtuálních počítačů z image

Po dokončení se verze image můžete vytvořit jeden nebo více nových virtuálních počítačů. 

Tento příklad vytvoří virtuální počítač s názvem *myVMfromImage*v *myResourceGroup* v *USA – východ* datového centra.

1. Na stránce pro vaši verzi image, vyberte **vytvořit virtuální počítač** v nabídce v horní části stránky.
1. Pro **skupiny prostředků**vyberte **vytvořit nový** a typ *myResourceGroup* pro název.
1. V **název virtuálního počítače**, typ *myVM*.
1. Pro **oblasti**vyberte *USA – východ*.
1. Pro **možností dostupnosti**, ponechte výchozí hodnotu *žádné redundance infrastruktury požadované*.
1. Hodnota pro **Image** by měl být vyplněné automaticky Pokud jste začali ze stránky pro verzi image.
1. Pro **velikost**, zvolte velikost virtuálního počítače ze seznamu dostupných velikostí a klikněte na "Vyberte".
1. V části **účet správce**vyberte **heslo** nebo **veřejný klíč SSH**, zadejte vaše informace.
1. Pokud chcete povolit vzdálený přístup k virtuálnímu počítači, v části **veřejné příchozí porty**, zvolte **povolit vybrané porty** a pak vyberte **SSH (22)** z rozevíracího seznamu. Pokud nechcete povolit vzdálený přístup k virtuálnímu počítači, ponechejte **žádný** vybrala pro **veřejné příchozí porty**.
1. Až budete hotovi, vyberte **revize + vytvořit** tlačítko v dolní části stránky.
1. Když virtuální počítač úspěšně proběhne ověření, vyberte **vytvořit** v dolní části stránky a spustit nasazení.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro příslušný virtuální počítač, vyberete **Odstranit** a pak potvrdíte název skupiny prostředků, kterou chcete odstranit.

Pokud chcete odstranit jednotlivé prostředky, musíte je odstranit, v opačném pořadí. Například pokud chcete odstranit definici image, musíte odstranit všechny verze image vytvořené z této image.

## <a name="next-steps"></a>Další postup

Můžete také vytvořit Galerie obrázků sdílených prostředků pomocí šablon. Nejsou k dispozici několik šablon rychlý start Azure: 

- [Vytvořit sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice Image v galerii sdílené bitové kopie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření Image verze v sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z Image verze](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o galeriích sdílené bitové kopie, najdete v článku [přehled](shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [Poradce při potížích s sdílené Galerie obrázků](troubleshooting-shared-images.md).

