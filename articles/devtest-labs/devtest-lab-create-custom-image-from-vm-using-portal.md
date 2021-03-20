---
title: Vytvoření vlastní image Azure DevTest Labs z virtuálního počítače | Microsoft Docs
description: Naučte se, jak vytvořit vlastní image v Azure DevTest Labs ze zřízeného virtuálního počítače pomocí Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad45ed6eb7f97e14ec0ca0bb89efb2967c90fc16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87277023"
---
# <a name="create-a-custom-image-from-a-vm"></a>Vytvoření vlastní image z virtuálního počítače

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Můžete vytvořit vlastní image ze zřízeného virtuálního počítače a následně ji pomocí této vlastní image vytvořit identické virtuální počítače. Následující kroky ukazují, jak vytvořit vlastní image z virtuálního počítače:

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .

1. V seznamu cvičení vyberte požadované testovací prostředí.  

1. V hlavním podokně testovacího prostředí vyberte **moje virtuální počítače**.
 
1. V podokně **moje virtuální počítače** vyberte virtuální počítač, ze kterého chcete vytvořit vlastní image.

1. V podokně Správa virtuálního počítače vyberte **vytvořit vlastní image** v části **operace**.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image.png" alt-text="Vytvořit vlastní položku nabídky image":::
1. V podokně **vlastní obrázek** zadejte název a popis vlastní image. Tyto informace se zobrazí v seznamu základů při vytváření virtuálního počítače. Vlastní image bude zahrnovat disk s operačním systémem a všechny datové disky připojené k virtuálnímu počítači.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="Vytvořit vlastní stránku obrázku":::
1. Vyberte, zda byl na virtuálním počítači spuštěn nástroj Sysprep. Pokud se na virtuálním počítači nespustí nástroj Sysprep, určete, jestli se má při vytvoření vlastní image spustit nástroj Sysprep na virtuálním počítači.
1. Po dokončení vyberte **OK** , aby se vytvořila vlastní image.

    Po několika minutách se vlastní image vytvoří a uloží se do účtu úložiště testovacího prostředí. Když chce uživatel testovacího prostředí vytvořit nový virtuální počítač, bude obrázek k dispozici v seznamu základních imagí.

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="vlastní image je dostupná v seznamu základních imagí.":::

## <a name="related-blog-posts"></a>Související blogové příspěvky

- [Vlastní obrázky nebo vzorce?](./devtest-lab-faq.md#blog-post)
- [Kopírování vlastních imagí mezi Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další kroky

- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
