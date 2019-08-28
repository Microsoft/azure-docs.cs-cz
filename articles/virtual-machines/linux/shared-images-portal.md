---
title: Vytvoření sdílených imagí virtuálních počítačů Azure pro Linux pomocí portálu | Microsoft Docs
description: Naučte se používat Azure Portal k vytváření a sdílení imagí virtuálních počítačů.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 8be4890f01ae2c0d893bb7c45f29c6f8178844f9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082116"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Vytvoření galerie sdílených imagí pomocí Azure Portal

[Galerie sdílených imagí](shared-image-galleries.md) zjednodušuje sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spuštění úloh nasazení, jako jsou předem načtené aplikace, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených imagí umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci v rámci oblastí nebo napříč nimi v rámci tenanta AAD. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. Můžete vytvořit několik galerií, abyste mohli logicky seskupovat sdílené image. 

Galerie je prostředek nejvyšší úrovně, který poskytuje úplné řízení přístupu na základě role (RBAC). Bitové kopie můžou být ve verzi a můžete se rozhodnout pro replikaci každé verze image na jinou sadu oblastí Azure. Galerie funguje pouze se spravovanými bitovými kopiemi.

Funkce Galerie sdílených imagí má více typů prostředků. V tomto článku budeme používat nebo sestavovat tyto:

| Resource | Popis|
|----------|------------|
| **Spravovaná image** | Toto je základní obrázek, který se dá použít samostatně nebo použít k vytvoření **verze image** v galerii imagí. Spravované image se vytvářejí z zobecněných virtuálních počítačů. Spravovaná bitová kopie je speciální typ VHD, který se dá použít k vytvoření více virtuálních počítačů a dá se teď použít k vytváření verzí sdílených imagí. |
| **Galerie imagí** | Podobně jako u Azure Marketplace je **Galerie imagí** úložiště pro správu a sdílení imagí, ale Vy řídíte, kdo má přístup. |
| **Definice obrázku** | Image jsou definované v rámci Galerie a obsahují informace o imagi a požadavcích na jejich interní používání. To zahrnuje, zda se jedná o obrázek Windows nebo Linux, poznámky k verzi a minimální a maximální požadavky na paměť. Je definicí typu obrázku. |
| **Verze image** | **Verze image** je to, co použijete k vytvoření virtuálního počítače při použití galerie. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Podobně jako u spravované image při použití **verze image** k vytvoření virtuálního počítače se verze image používá k vytvoření nových disků pro virtuální počítač. Verze bitové kopie lze použít několikrát. |


## <a name="before-you-begin"></a>Před zahájením

Chcete-li dokončit příklad v tomto článku, musíte mít existující spravovanou bitovou kopii. Můžete postupovat [podle kurzu: Vytvořte vlastní image virtuálního počítače Azure pomocí Azure PowerShell](tutorial-custom-images.md) a v případě potřeby ho vytvořte. Pokud spravovaná bitová kopie obsahuje datový disk, velikost datového disku nemůže být větší než 1 TB.

Při práci s tímto článkem nahraďte názvy skupin prostředků a virtuálních počítačů tam, kde je to potřeba.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>Vytvoření virtuálních počítačů z Image

Po dokončení verze image můžete vytvořit jeden nebo více nových virtuálních počítačů. 

> [!IMPORTANT]
> Portál nemůžete použít k nasazení virtuálního počítače z image v jiném tenantovi Azure. Pokud chcete vytvořit virtuální počítač z image sdílené mezi klienty, musíte použít [Azure CLI](shared-images.md#create-a-vm) nebo [PowerShell](../windows/shared-images.md#create-vms-from-an-image).


Tento příklad vytvoří virtuální počítač s názvem *myVMfromImage*v *myResourceGroup* v datovém centru *východní USA* .

1. Na stránce verze image vyberte v nabídce v horní části stránky **vytvořit virtuální počítač** .
1. V poli **Skupina prostředků**vyberte **vytvořit novou** a jako název zadejte *myResourceGroup* .
1. Do **název virtuálního počítače**zadejte *myVM*.
1. V **oblasti oblast**vyberte *východní USA*.
1. U **možností dostupnosti**ponechte výchozí možnost *bez nutnosti redundance infrastruktury*.
1. Hodnota pro **Obrázek** by se měla automaticky vyplnit, pokud jste začali ze stránky verze image.
1. Jako **Velikost**zvolte velikost virtuálního počítače ze seznamu dostupných velikostí a pak klikněte na vybrat.
1. V části **účet správce**vyberte **heslo** nebo **veřejný klíč SSH**a pak zadejte svoje informace.
1. Pokud chcete povolit vzdálený přístup k virtuálnímu počítači, vyberte v části **veřejné příchozí porty**možnost **Povolit vybrané porty** a v rozevíracím seznamu vyberte **SSH (22)** . Pokud nechcete povolit vzdálený přístup k virtuálnímu počítači, nechte **žádné** vybrané pro **veřejné příchozí porty**.
1. Po dokončení vyberte tlačítko **Revize + vytvořit** v dolní části stránky.
1. Jakmile virtuální počítač projde ověřením, v dolní části stránky vyberte **vytvořit** a spusťte nasazení.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro příslušný virtuální počítač, vyberete **Odstranit** a pak potvrdíte název skupiny prostředků, kterou chcete odstranit.

Pokud chcete odstranit jednotlivé prostředky, je nutné je odstranit v opačném pořadí. Chcete-li například odstranit definici obrázku, je nutné odstranit všechny verze imagí vytvořené z této bitové kopie.

## <a name="next-steps"></a>Další postup

Pomocí šablon můžete také vytvořit prostředek Galerie sdílených imagí. K dispozici je několik šablon rychlého startu Azure: 

- [Vytvoření galerie sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice obrázku v galerii sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze image v galerii sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z verze image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o galeriích sdílených imagí najdete v [přehledu](shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [řešení potíží s galeriemi sdílených imagí](troubleshooting-shared-images.md).

