---
title: Vytvoření vlastní image Azure DevTest Labs ze souboru virtuálního pevného disku | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit vlastní image ve službě Azure DevTest Labs ze souboru VHD pomocí webu Azure portal
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 9d009847856936d14a182b234910fb717c084d11
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248057"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Vytvoření vlastní image ze souboru virtuálního pevného disku

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou procesem vytvoření vlastní image ze souboru VHD pomocí webu Azure portal:

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.

1. V seznamu testovacích prostředí vyberte požadované prostředí.  

1. V hlavním podokně testovacího prostředí, vyberte **konfigurace a zásad**. 

1. Na **konfigurace a zásad** vyberte **vlastních imagí**.

1. Na **vlastních imagí** vyberte **+ přidat**.

    ![Přidání vlastní image](./media/devtest-lab-create-template/add-custom-image.png)

1. Zadejte název vlastní image. Tento název se zobrazí v seznamu základní Image při vytváření virtuálního počítače.

1. Zadejte popis vlastní image. Tento popis se zobrazí v seznamu základních imagí při vytváření virtuálního počítače.

1. Pro **typ operačního systému**, vyberte buď **Windows** nebo **Linux**.

    - Pokud vyberete **Windows**, prostřednictvím zaškrtávacího políčka určete, zda *sysprep* byl spuštěn v počítači. 
    - Pokud vyberete **Linux**, prostřednictvím zaškrtávacího políčka určete, zda *zrušení zřízení* byl spuštěn v počítači. 

1. Vyberte **virtuálního pevného disku** z rozevírací nabídky. Toto je virtuální pevný disk, který se použije k vytvoření nové vlastní image. V případě potřeby vyberte možnost **nahrát VHD pomocí Powershellu**.

1. Pokud image použitá k vytvoření vlastní image není licencované image (vydáno nakladatelstvím Microsoft), můžete zadat také název plánu, plán nabídky a vydavatel plánu.

   - **Název plánu:** zadejte název image Marketplace (SKU) z vlastní image se vytvoří 
   - **Nabídka plánu:** zadejte produkt (nabídku) Image Marketplace, ze kterého se vytvoří vlastní image 
   - **Vydavatel plánu:** zadejte vydavatele image Marketplace, ze kterého se vytvoří vlastní image

   > [!NOTE]
   > Pokud je obrázek, který používáte k vytvoření vlastní image **není** licencované image a pak tato pole jsou prázdné a může být vyplněna, pokud se rozhodnete. Pokud image **je** licencované image a pak pole jsou automaticky vyplněna informacemi o plánu. Pokud se pokusíte v tomto případě je změnit, zobrazí se zpráva s upozorněním.
   >
   >

1. Vyberte **OK** k vytvoření vlastní image.

Po několika minutách se vlastní image se vytvoří a je uložený v účtu úložiště testovacího prostředí. Když chce uživatel testovacího prostředí vytvořit nový virtuální počítač, na obrázku je k dispozici v seznamu základní Image.

![Vlastní image k dispozici v seznamu základních imagí](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky

- [Vlastní Image nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních Imagí mezi Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další postup

- [Přidání virtuálního počítače do testovacího prostředí](./devtest-lab-add-vm.md)
