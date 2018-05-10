---
title: Z virtuálního počítače vytvořit vlastní image Azure DevTest Labs | Microsoft Docs
description: Naučte se vytvořit vlastní image v Azure DevTest Labs ze zřízeného virtuálního počítače pomocí portálu Azure
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
ms.openlocfilehash: 9c98918dcd08c2e8ec72cc995c633b8320da8057
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-custom-image-from-a-vm"></a>Vytvořit vlastní image z virtuálního počítače

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Můžete vytvořit vlastní image ze zřízeného virtuálního počítače a pak použít tento vlastní bitovou kopii k Vytvořte identické virtuální počítače. Následující kroky ukazují, jak vytvořit vlastní image z virtuálního počítače:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.

1. Ze seznamu labs vyberte požadované testovací prostředí.  

1. V hlavním podokně v prostředí, vyberte **Můj virtuální počítače**.
 
1. Na **Můj virtuální počítače** podokně, vyberte virtuální počítač, ze kterého chcete vytvořit vlastní image.

1. V podokně Správa Virtuálního počítače, vyberte **vytvořit vlastní image (VHD)**.

    ![Vytvoření vlastní image položky nabídky](./media/devtest-lab-create-template/create-custom-image.png)

1. Na **vlastní image** podokně, zadejte název a popis pro vlastní bitovou kopii. Tyto informace se zobrazí v seznamu základů při vytváření virtuálního počítače.

    ![Vytvořit vlastní image podokno](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Vyberte, zda byl spuštěn nástroj sysprep na virtuálním počítači. Pokud na virtuální počítač nebyl spuštěn nástroje sysprep, určete, jestli má nástroj sysprep, které jsou spuštěny při vytvoření virtuálního počítače z této vlastní image.

1. Vyberte **OK** po dokončení vytvoření vlastní image.

Po několika minutách vlastní image se vytvoří a je uložený v účtu úložiště v prostředí. Pokud chce uživatel laboratoře k vytvoření nového virtuálního počítače, je k dispozici v seznamu obrázků základní bitovou kopii.

![Vlastní image, které jsou k dispozici v seznamu základní bitové kopie](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Příspěvky blogu související

- [Vlastní Image nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních bitových kopií mezi Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další postup

- [Přidejte virtuální počítač do testovacího prostředí](devtest-lab-add-vm.md)
