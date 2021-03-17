---
title: Vytvoření galerie sdílených imagí Azure pomocí portálu
description: Naučte se používat Azure Portal k vytváření a sdílení imagí virtuálních počítačů.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.openlocfilehash: e28a1412fa417b9f15ab7a6f79305d07ca6d9fec
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556090"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Vytvoření galerie sdílených imagí Azure pomocí portálu

[Galerie sdílených imagí](../shared-image-galleries.md) zjednodušuje sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spuštění úloh nasazení, jako jsou předem načtené aplikace, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených imagí umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci v rámci oblastí nebo napříč nimi v rámci tenanta AAD. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. Můžete vytvořit několik galerií, abyste mohli logicky seskupovat sdílené image. 

Galerie je prostředek na nejvyšší úrovni, který poskytuje úplné řízení přístupu na základě role Azure (Azure RBAC). Bitové kopie můžou být ve verzi a můžete se rozhodnout pro replikaci každé verze image na jinou sadu oblastí Azure. Galerie funguje pouze se spravovanými bitovými kopiemi.

Funkce Galerie sdílených imagí má více typů prostředků. V tomto článku budeme používat nebo sestavovat tyto:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


Při práci s tímto článkem nahraďte názvy skupin prostředků a virtuálních počítačů tam, kde je to potřeba.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Vytvoření virtuálních počítačů

Nyní můžete vytvořit jeden nebo více nových virtuálních počítačů. Tento příklad vytvoří virtuální počítač s názvem *myVM* v *myResourceGroup* v datovém centru *východní USA* .

1. Přejít k definici image. Pomocí filtru prostředků můžete zobrazit všechny dostupné definice obrázků.
1. Na stránce definice image vyberte v nabídce v horní části stránky **vytvořit virtuální počítač** .
1. V poli **Skupina prostředků** vyberte **vytvořit novou** a jako název zadejte *myResourceGroup* .
1. Do **název virtuálního počítače** zadejte *myVM*.
1. V **oblasti oblast** vyberte *východní USA*.
1. U **možností dostupnosti** ponechte výchozí možnost *bez nutnosti redundance infrastruktury*.
1. Hodnota pro **Image** se automaticky vyplní `latest` verzí image, pokud jste začali ze stránky pro definici image.
1. Jako **Velikost** zvolte velikost virtuálního počítače ze seznamu dostupných velikostí a pak zvolte **Vybrat**.
1. V části **účet správce** je v případě, že byla bitová kopie zobecněna, nutné zadat uživatelské jméno, například *azureuser* a heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm). Pokud byla vaše image specializovaná, pole uživatelského jména a hesla se zobrazí šedě, protože se používá uživatelské jméno a heslo pro zdrojový virtuální počítač.
1. Pokud chcete povolit vzdálený přístup k virtuálnímu počítači, vyberte v části **veřejné příchozí porty** možnost **Povolit vybrané porty** a v rozevíracím seznamu vyberte **RDP (3389)** . Pokud nechcete povolit vzdálený přístup k virtuálnímu počítači, nechte **žádné** vybrané pro **veřejné příchozí porty**.
1. Po dokončení vyberte tlačítko **Revize + vytvořit** v dolní části stránky.
1. Jakmile virtuální počítač projde ověřením, v dolní části stránky vyberte **vytvořit** a spusťte nasazení.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro příslušný virtuální počítač, vyberete **Odstranit** a pak potvrdíte název skupiny prostředků, kterou chcete odstranit.

Pokud chcete odstranit jednotlivé prostředky, je nutné je odstranit v opačném pořadí. Chcete-li například odstranit definici obrázku, je nutné odstranit všechny verze imagí vytvořené z této bitové kopie.

## <a name="next-steps"></a>Další kroky

Pomocí šablon můžete také vytvořit prostředek Galerie sdílených imagí. K dispozici je několik šablon rychlého startu Azure: 

- [Vytvoření služby Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definici image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z verze image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o galeriích sdílených imagí najdete v [přehledu](../shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [řešení potíží s galeriemi sdílených imagí](../troubleshooting-shared-images.md).