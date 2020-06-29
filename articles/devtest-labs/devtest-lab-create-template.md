---
title: Vytvoření vlastní image Azure DevTest Labs ze souboru VHD | Microsoft Docs
description: Naučte se, jak vytvořit vlastní image v Azure DevTest Labs ze souboru VHD pomocí Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ba627421a43a8e4e7f193ad96da09da8934d028c
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2020
ms.locfileid: "85481268"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Vytvoření vlastní image ze souboru VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou vytvořením vlastní image ze souboru VHD pomocí Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** .

1. V seznamu cvičení vyberte požadované testovací prostředí.  

1. V hlavním podokně testovacího prostředí vyberte **Konfigurace a zásady**. 

1. V podokně **Konfigurace a zásady** vyberte **vlastní image**.

1. V podokně **vlastní image** vyberte **+ Přidat**.

    ![Přidat vlastní image](./media/devtest-lab-create-template/add-custom-image.png)

1. Zadejte název vlastní image. Tento název se zobrazí v seznamu základních imagí při vytváření virtuálního počítače.

1. Zadejte popis vlastní image. Tento popis se zobrazí v seznamu základních imagí při vytváření virtuálního počítače.

1. Jako **typ operačního systému**vyberte buď **Windows** , nebo **Linux**.

    - Pokud vyberete možnost **Windows**, určete pomocí zaškrtávacího políčka, zda byl na počítači spuštěn *Nástroj Sysprep* . 
    - Pokud vyberete možnost **Linux**, určete pomocí zaškrtávacího políčka, zda bylo na počítači spuštěno *zrušení zřízení* . 

1. Z rozevírací nabídky vyberte **virtuální pevný disk** . Toto je virtuální pevný disk, který se použije k vytvoření nové vlastní image. V případě potřeby vyberte možnost **nahrát VHD pomocí PowerShellu**.

1. V případě, že obrázek použitý k vytvoření vlastní image není licencovaná Image (publikovaná Microsoftem), můžete zadat také název plánu, nabídku plánování a plán vydavatele.

   - **Název plánu:** Zadejte název Image Marketplace (SKU), ze které se vytvoří vlastní image. 
   - **Nabídka plánu:** Zadejte produkt (nabídku) image Marketplace, ze které se vytvoří vlastní image. 
   - **Vydavatel plánu:** Zadejte vydavatele image Marketplace, ze které se vytvoří vlastní image.

   > [!NOTE]
   > Pokud obrázek, který používáte k vytvoření vlastní image **, není licencovaný** obrázek, jsou tato pole prázdná a můžou se vyplnit, pokud zvolíte. Pokud se **jedná** o licencovanou bitovou kopii, pak se pole automaticky vyplní informacemi o plánu. Pokud se v tomto případě pokusíte změnit, zobrazí se varovná zpráva.
   >
   >

1. Vyberte **OK** a vytvořte vlastní image.

Po několika minutách se vlastní image vytvoří a uloží se do účtu úložiště testovacího prostředí. Když chce uživatel testovacího prostředí vytvořit nový virtuální počítač, bude obrázek k dispozici v seznamu základních imagí.

![Vlastní image je dostupná v seznamu základních imagí.](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky

- [Vlastní obrázky nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních imagí mezi Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další kroky

- [Přidání virtuálního počítače do testovacího prostředí](./devtest-lab-add-vm.md)
