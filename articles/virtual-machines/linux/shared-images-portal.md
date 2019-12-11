---
title: Vytvoření sdílených imagí virtuálních počítačů Azure pro Linux pomocí portálu
description: Naučte se používat Azure Portal k vytváření a sdílení imagí virtuálních počítačů.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 44df85a94ad26d3abcd48f01c31e7aa093c1123f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978692"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Vytvoření galerie sdílených imagí Azure pomocí portálu

[Galerie sdílených imagí](shared-image-galleries.md) zjednodušuje sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spuštění úloh nasazení, jako jsou předem načtené aplikace, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených imagí umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci v rámci oblastí nebo napříč nimi v rámci tenanta AAD. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. Můžete vytvořit několik galerií, abyste mohli logicky seskupovat sdílené image. 

Galerie je prostředek nejvyšší úrovně, který poskytuje úplné řízení přístupu na základě role (RBAC). Bitové kopie můžou být ve verzi a můžete se rozhodnout pro replikaci každé verze image na jinou sadu oblastí Azure. Galerie funguje pouze se spravovanými bitovými kopiemi.

Funkce Galerie sdílených imagí má více typů prostředků. V tomto článku budeme používat nebo sestavovat tyto:

| Prostředek | Popis|
|----------|------------|
| **Spravovaná image** | Základní image, která se dá použít samostatně nebo použít k vytvoření **verze image** v galerii imagí. Spravované image se vytvářejí z [zobecněných](shared-image-galleries.md#generalized-and-specialized-images) virtuálních počítačů. Spravovaná bitová kopie je speciální typ VHD, který se dá použít k vytvoření více virtuálních počítačů a dá se teď použít k vytváření verzí sdílených imagí. |
| **Snímek** | Kopie VHD, která se dá použít k vytvoření **Image verze** Snímky se dají považovat ze [specializovaného](shared-image-galleries.md#generalized-and-specialized-images) virtuálního počítače (který se nezobecněný) pak použít samostatně, nebo se snímky datových disků, aby se vytvořila specializovaná verze image.
| **Galerie imagí** | Podobně jako u Azure Marketplace je **Galerie imagí** úložiště pro správu a sdílení imagí, ale Vy řídíte, kdo má přístup. |
| **Definice obrázku** | Image jsou definované v rámci Galerie a obsahují informace o imagi a požadavcích na jejich použití v rámci vaší organizace. Můžete zahrnout informace, jako je například to, zda je obrázek zobecněný nebo specializovaný, operační systém, minimální a maximální požadavky na paměť a poznámky k verzi. Je definicí typu obrázku. |
| **Verze image** | **Verze image** je to, co použijete k vytvoření virtuálního počítače při použití galerie. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Podobně jako u spravované image při použití **verze image** k vytvoření virtuálního počítače se verze image používá k vytvoření nových disků pro virtuální počítač. Verze bitové kopie lze použít několikrát. |

<br>

> [!IMPORTANT]
> Specializované obrázky jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Známá omezení verze Preview** Virtuální počítače se dají vytvářet jenom z specializovaných imagí pomocí portálu nebo rozhraní API. Pro verzi Preview není podporována podpora rozhraní příkazového řádku nebo PowerShellu.


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
1. Hodnota pro **Image** se automaticky vyplní verzí bitové kopie `latest`, pokud jste začali ze stránky pro definici image.
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

- [Vytvoření galerie sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice obrázku v galerii sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze image v galerii sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z verze image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o galeriích sdílených imagí najdete v [přehledu](shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [řešení potíží s galeriemi sdílených imagí](troubleshooting-shared-images.md).

