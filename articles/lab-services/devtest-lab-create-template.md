---
title: Vytvoření vlastní image Azure DevTest Labs ze souboru VHD | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit vlastní image v Azure DevTest Labs ze souboru Virtuálního pevného disku pomocí portálu Azure
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
ms.openlocfilehash: 853c138c8cf73b41b0cebb6c1d349865e18eab6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61295950"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Vytvoření vlastního obrázku ze souboru VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou vytvořením vlastní image ze souboru Virtuálního pevného disku pomocí portálu Azure:

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**

1. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  

1. V hlavním podokně testovacího prostředí vyberte **Možnost Konfigurace a zásady**. 

1. V podokně **Konfigurace a zásady** vyberte **Vlastní obrázky**.

1. V podokně **Vlastní obrázky** vyberte **+Přidat**.

    ![Přidat vlastní obrázek](./media/devtest-lab-create-template/add-custom-image.png)

1. Zadejte název vlastního obrázku. Tento název se zobrazí v seznamu základních bitových kopií při vytváření virtuálního virtuálního soudu.

1. Zadejte popis vlastního obrázku. Tento popis se zobrazí v seznamu základních bitových kopií při vytváření virtuálního virtuálního soudu.

1. Pro **typ operačního systému**vyberte **buď Windows** nebo **Linux**.

    - Pokud vyberete **systém Windows**, zadejte prostřednictvím zaškrtávacího políčka, zda byl v počítači spuštěn *program sysprep.* 
    - Pokud vyberete **Linux**, zadejte prostřednictvím zaškrtávacího políčka, zda bylo v počítači spuštěno *zrušení zřízení.* 

1. V rozevírací nabídce vyberte **virtuální disk.** Jedná se o virtuální pevný disk, který bude použit k vytvoření nové vlastní image. V případě potřeby vyberte **nahrát virtuální pevný disk pomocí prostředí PowerShell**.

1. Můžete také zadat název plánu, nabídku plánu a vydavatele plánu, pokud bitová kopie použitá k vytvoření vlastní bitové kopie není licencovanou bitovou kopií (publikovanou společností Microsoft).

   - **Název plánu:** Zadejte název image Marketplace (SKU), ze kterého je tato vlastní bitová kopie vytvořena. 
   - **Nabídka plánu:** Zadejte produkt (nabídku) image Marketplace, ze kterého je tento vlastní obrázek vytvořen. 
   - **Vydavatel plánu:** Zadejte vydavatele image Marketplace, ze kterého je tato vlastní bitová kopie vytvořena.

   > [!NOTE]
   > Pokud obrázek, který používáte k vytvoření vlastního obrázku, **není** licencovaným obrázkem, jsou tato pole prázdná a lze je vyplnit, pokud se rozhodnete. Pokud **se jedná** o licencovanou bitovou kopii, pole jsou automaticky vyplněna informacemi o plánu. Pokud se je pokusíte v tomto případě změnit, zobrazí se varovná zpráva.
   >
   >

1. Chcete-li vytvořit vlastní obrázek, vyberte **OK.**

Po několika minutách se vytvoří vlastní image a je uložena v účtu úložiště testovacího prostředí. Když uživatel testovacího prostředí chce vytvořit nový virtuální virtuální ms, image je k dispozici v seznamu základních bitových kopií.

![Vlastní obrázek dostupný v seznamu základních obrázků](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související příspěvky blogu

- [Vlastní obrázky nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních ibi mezi azure devtest labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další kroky

- [Přidání virtuálního počítače do testovacího prostředí](./devtest-lab-add-vm.md)
