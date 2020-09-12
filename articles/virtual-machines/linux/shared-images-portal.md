---
title: Vytvoření sdílených imagí virtuálních počítačů Azure Linux pomocí portálu
description: Naučte se používat Azure Portal k vytváření a sdílení imagí virtuálních počítačů se systémem Linux.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 30e61a9a8bf4d7f843db5e68278e4b10a3a8c023
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318110"
---
# <a name="create-a-shared-image-gallery-using-the-portal"></a>Vytvoření galerie sdílených imagí pomocí portálu

[Galerie sdílených imagí](shared-image-galleries.md) zjednodušuje sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spuštění úloh nasazení, jako jsou předem načtené aplikace, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených imagí umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci v rámci oblastí nebo napříč nimi v rámci tenanta Azure AD. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. Můžete vytvořit několik galerií, abyste mohli logicky seskupovat sdílené image. 

Galerie je prostředek nejvyšší úrovně, který poskytuje úplné řízení přístupu na základě role (RBAC). Bitové kopie můžou být ve verzi a můžete se rozhodnout pro replikaci každé verze image na jinou sadu oblastí Azure. Galerie funguje pouze se spravovanými bitovými kopiemi.

Funkce Galerie sdílených imagí má více typů prostředků. V tomto článku budeme používat nebo sestavovat tyto:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>





## <a name="before-you-begin"></a>Než začnete

Chcete-li dokončit příklad v tomto článku, musíte mít existující spravovanou bitovou kopii zobecněného virtuálního počítače nebo snímek specializovaného virtuálního počítače. Postup najdete v [kurzu: Vytvoření vlastní image virtuálního počítače Azure s Azure PowerShell](tutorial-custom-images.md) pro vytvoření spravované Image nebo [vytvoření snímku](../windows/snapshot-copy-managed-disk.md) pro specializovaný virtuální počítač. U spravovaných imagí i snímků nemůže být velikost datového disku větší než 1 TB.

Při práci s tímto článkem nahraďte názvy skupin prostředků a virtuálních počítačů tam, kde je to potřeba.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Vytvoření virtuálních počítačů 

Nyní můžete vytvořit jeden nebo více nových virtuálních počítačů. Tento příklad vytvoří virtuální počítač s názvem *myVMfromImage*v *myResourceGroup* v datovém centru *východní USA* .

1. Přejít k definici image. Pomocí filtru prostředků můžete zobrazit všechny dostupné definice obrázků.
1. Na stránce definice image vyberte v nabídce v horní části stránky **vytvořit virtuální počítač** .
1. V poli **Skupina prostředků**vyberte **vytvořit novou** a jako název zadejte *myResourceGroup* .
1. Do **název virtuálního počítače**zadejte *myVM*.
1. V **oblasti oblast**vyberte *východní USA*.
1. U **možností dostupnosti**ponechte výchozí možnost *bez nutnosti redundance infrastruktury*.
1. Hodnota pro **Image** se automaticky vyplní `latest` verzí image, pokud jste začali ze stránky pro definici image.
1. Jako **Velikost**zvolte velikost virtuálního počítače ze seznamu dostupných velikostí a pak zvolte **Vybrat**.
1. V části **účet správce**, pokud byl zdrojový virtuální počítač zobecněný, zadejte svoje **uživatelské jméno** a **veřejný klíč SSH**. Pokud byl zdrojový virtuální počítač specializovaný, budou tyto možnosti šedé, protože se používají informace ze zdrojového virtuálního počítače.
1. Pokud chcete povolit vzdálený přístup k virtuálnímu počítači, vyberte v části **veřejné příchozí porty**možnost **Povolit vybrané porty** a v rozevíracím seznamu vyberte **SSH (22)** . Pokud nechcete povolit vzdálený přístup k virtuálnímu počítači, nechte **žádné** vybrané pro **veřejné příchozí porty**.
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

Další informace o galeriích sdílených imagí najdete v [přehledu](shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [řešení potíží s galeriemi sdílených imagí](troubleshooting-shared-images.md).

