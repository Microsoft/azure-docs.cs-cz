---
title: Použití sdílených imagí virtuálních počítačů k vytvoření sady škálování v Azure PowerShell
description: Naučte se používat Azure PowerShell k vytváření sdílených imagí virtuálních počítačů, které se použijí pro nasazení služby Virtual Machine Scale Sets v Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: b765c8b77662c4c7f80735ef92be4a78089c8341
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323443"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Vytváření a používání sdílených imagí pro sady škálování virtuálních počítačů s Azure PowerShell

Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. Služba Galerie sdílených imagí značně zjednodušuje sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených imagí umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci v rámci oblastí nebo napříč nimi v rámci tenanta AAD. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. Můžete vytvořit několik galerií, abyste mohli logicky seskupovat sdílené image. 

Galerie je prostředek nejvyšší úrovně, který poskytuje úplné řízení přístupu na základě role (RBAC). Bitové kopie můžou být ve verzi a můžete se rozhodnout pro replikaci každé verze image na jinou sadu oblastí Azure. Galerie funguje pouze se spravovanými bitovými kopiemi. 

Funkce Galerie sdílených imagí má více typů prostředků. 


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>Než začnete

Následující postup podrobně popisuje přeměnu existujícího virtuálního počítače na opětovně použitelnou vlastní image, pomocí které můžete vytvářet nové instance virtuálních počítačů.

Chcete-li dokončit příklad v tomto článku, musíte mít existující spravovanou bitovou kopii. Postup najdete v tomto [kurzu: vytvoření a použití vlastní image pro Virtual Machine Scale Sets s Azure PowerShell](tutorial-use-custom-image-powershell.md) pro vytvoření v případě potřeby. Pokud spravovaná bitová kopie obsahuje datový disk, velikost datového disku nemůže být větší než 1 TB.

Při práci s článkem nahraďte skupinu prostředků a názvy virtuálních počítačů tam, kde je to potřeba.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]




## <a name="next-steps"></a>Další kroky

Pomocí šablon můžete také vytvořit prostředek Galerie sdílených imagí. K dispozici je několik šablon rychlého startu Azure: 

- [Vytvoření služby Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definici image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z verze image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o galeriích sdílených imagí najdete v [přehledu](shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [řešení potíží s galeriemi sdílených imagí](../virtual-machines/troubleshooting-shared-images.md).
