---
title: Vytvoření vlastní image Azure DevTest Labs z virtuálního počítače | Microsoft Docs
description: Naučte se, jak vytvořit vlastní image v Azure DevTest Labs ze zřízeného virtuálního počítače pomocí Azure Portal
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "60868420"
---
# <a name="create-a-custom-image-from-a-vm"></a>Vytvoření vlastní image z virtuálního počítače

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Můžete vytvořit vlastní image ze zřízeného virtuálního počítače a následně ji pomocí této vlastní image vytvořit identické virtuální počítače. Následující kroky ukazují, jak vytvořit vlastní image z virtuálního počítače:

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** .

1. V seznamu cvičení vyberte požadované testovací prostředí.  

1. V hlavním podokně testovacího prostředí vyberte **moje virtuální počítače**.
 
1. V podokně **moje virtuální počítače** vyberte virtuální počítač, ze kterého chcete vytvořit vlastní image.

1. V podokně Správa virtuálního počítače vyberte **vytvořit vlastní image (VHD)**.

    ![Vytvořit vlastní položku nabídky image](./media/devtest-lab-create-template/create-custom-image.png)

1. V podokně **vlastní obrázek** zadejte název a popis vlastní image. Tyto informace se zobrazí v seznamu základů při vytváření virtuálního počítače. Vlastní image bude zahrnovat disk s operačním systémem a všechny datové disky připojené k virtuálnímu počítači.

    ![Vytvořit vlastní podokno obrázku](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Vyberte, zda byl na virtuálním počítači spuštěn nástroj Sysprep. Pokud se na virtuálním počítači nespustí nástroj Sysprep, určete, jestli se má při vytvoření vlastní image spustit nástroj Sysprep na virtuálním počítači.

1. Po dokončení vyberte **OK** , aby se vytvořila vlastní image.

Po několika minutách se vlastní image vytvoří a uloží se do účtu úložiště testovacího prostředí. Když chce uživatel testovacího prostředí vytvořit nový virtuální počítač, bude obrázek k dispozici v seznamu základních imagí.

![Vlastní image je dostupná v seznamu základních imagí.](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky

- [Vlastní obrázky nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních imagí mezi Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další kroky

- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
