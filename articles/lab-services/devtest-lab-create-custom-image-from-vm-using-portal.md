---
title: Vytvoření vlastní image Azure DevTest Labs z virtuálního počítače | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit vlastní image ve službě Azure DevTest Labs z zřízeného virtuálního počítače pomocí webu Azure portal
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
ms.openlocfilehash: 22f1579b2df2acdc736ed4c1d5cee64d096c320a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635333"
---
# <a name="create-a-custom-image-from-a-vm"></a>Vytvoření vlastní image z virtuálního počítače

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Můžete vytvořit vlastní image z virtuálního počítače s zřízené a později použít tuto vlastní image k vytvoření identických virtuálních počítačů. Následující postup ukazuje, jak vytvořit vlastní image z virtuálního počítače:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.

1. V seznamu testovacích prostředí vyberte požadované prostředí.  

1. V hlavním podokně testovacího prostředí, vyberte **Moje virtual machines**.
 
1. Na **Moje virtual machines** podokně, vyberte virtuální počítač, ze kterého chcete vytvořit vlastní image.

1. V podokně Správa Virtuálního počítače, vyberte **vytvoření vlastní image (disku VHD)**.

    ![Vytvoření položky nabídky vlastní image](./media/devtest-lab-create-template/create-custom-image.png)

1. Na **vlastní image** podokně, zadejte název a popis pro vlastní image. Tyto informace se zobrazí v seznamu základních tříd, při vytváření virtuálního počítače. Vlastní image bude zahrnovat disk s operačním systémem a všechny datové disky připojené k virtuálnímu počítači.

    ![Vytvořit vlastní image podokno](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Vyberte, zda byl spuštěn nástroj sysprep na virtuálním počítači. Pokud nebyl sysprep na virtuálním počítači, určete, zda má nástroj sysprep spustit, když je virtuální počítač vytvořen z vlastní image.

1. Vyberte **OK** po dokončení vytvoření vlastní image.

Po několika minutách se vlastní image se vytvoří a je uložený v účtu úložiště testovacího prostředí. Když chce uživatel testovacího prostředí vytvořit nový virtuální počítač, na obrázku je k dispozici v seznamu základní Image.

![Vlastní image k dispozici v seznamu základních imagí](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky

- [Vlastní Image nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních Imagí mezi Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další postup

- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
