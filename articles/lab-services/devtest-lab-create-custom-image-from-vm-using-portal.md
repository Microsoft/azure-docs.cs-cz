---
title: Vytvoření vlastní image Azure DevTest Labs z virtuálního počítače | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit vlastní image v Azure DevTest Labs z zřízeného virtuálního počítače pomocí portálu Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 07f3b60b9218f74bb3a778daa27f31687c4538b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60868420"
---
# <a name="create-a-custom-image-from-a-vm"></a>Vytvoření vlastní image z virtuálního počítače

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Můžete vytvořit vlastní image z zřízeného virtuálního virtuálního hotelu a poté použít tuto vlastní image k vytvoření stejných virtuálních virtuálních měn. Následující kroky ilustrují, jak vytvořit vlastní obrázek z virtuálního virtuálního soudu:

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**

1. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  

1. V hlavním podokně testovacího prostředí vyberte **Moje virtuální počítače**.
 
1. V podokně **Moje virtuální počítače** vyberte virtuální počítač, ze kterého chcete vytvořit vlastní image.

1. V podokně správy virtuálního počítače vyberte **Vytvořit vlastní image (VHD).**

    ![Vytvořit vlastní položku nabídky obrázku](./media/devtest-lab-create-template/create-custom-image.png)

1. V podokně **Vlastní obrázek** zadejte název a popis vlastního obrázku. Tyto informace se zobrazí v seznamu základen při vytváření virtuálního virtuálního soudu. Vlastní bitová kopie bude obsahovat disk operačního systému a všechny datové disky připojené k virtuálnímu počítači.

    ![Vytvořit vlastní podokno obrázků](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Vyberte, zda byl sysprep spuštěn na virtuálním počítači. Pokud sysprep nebyl spuštěn na virtuálním počítači, určete, zda chcete sysprep spustit na virtuálním počítači při vytvoření vlastní image.

1. Chcete-li vytvořit vlastní obrázek, vyberte po dokončení možnost **OK.**

Po několika minutách se vytvoří vlastní image a je uložena v účtu úložiště testovacího prostředí. Když uživatel testovacího prostředí chce vytvořit nový virtuální virtuální ms, image je k dispozici v seznamu základních bitových kopií.

![Vlastní obrázek dostupný v seznamu základních obrázků](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související příspěvky blogu

- [Vlastní obrázky nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních ibi mezi azure devtest labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další kroky

- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
