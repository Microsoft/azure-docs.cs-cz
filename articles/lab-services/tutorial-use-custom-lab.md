---
title: Přístup k testovacímu prostředí v Azure DevTest Labs | Microsoft Docs
description: V tomto kurzu budete přistupovat k testovacímu prostředí vytvořenému pomocí služby Azure DevTest Labs, nárokovat virtuální počítače, používat je a potom je vracet.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/18/2019
ms.author: spelluru
ms.openlocfilehash: b5abb8d4aad7c58bf673aa578255efe12d32ad4b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422894"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Kurz: Přístup k testovacímu prostředí v Azure DevTest Labs
V tomto kurzu použijete, který byl vytvořen v testovacím prostředí [kurzu: Vytvoření testovacího prostředí ve službě Azure DevTest Labs](tutorial-create-custom-lab.md) .

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Nárokování virtuálního počítače v testovacím prostředí
> * Připojení k virtuálnímu počítači
> * Vrácení virtuálního počítače

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="access-the-lab"></a>Přístup k testovacímu prostředí

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **Všechny prostředky**. 
3. Jako typ prostředku vyberte **DevTest Labs**. 
4. Vyberte testovací prostředí. 

    ![Výběr testovacího prostředí](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Nárokování virtuálního počítače

1. V seznamu **Nárokovatelné virtuální počítače** vyberte **...**  (tři tečky) a vyberte **Nárokovat počítač**.

    ![Nárokování virtuálního počítače](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Zkontrolujte, jestli virtuální počítač vidíte v seznamu **Moje virtuální počítače**.

    ![Můj virtuální počítač](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

1. V seznamu vyberte svůj virtuální počítač. Zobrazí se **stránka virtuálního počítače** pro váš virtuální počítač. Na panelu nástrojů vyberte **Připojit**.

    ![Připojení k virtuálnímu počítači](./media/tutorial-use-custom-lab/connect-button.png)
2. Stažený soubor **RDP** uložte na pevný disk a použijte ho pro připojení k virtuálnímu počítači. Zadejte uživatelské jméno a heslo, které jste uvedli při vytváření virtuálního počítače v předchozí části. 

    > [!NOTE] 
    > Pokud se chcete připojit k virtuálnímu počítači s Linuxem, musí pro něj být povolený přístup přes SSH nebo protokol RDP. Postup připojení k virtuálnímu počítači s Linuxem přes protokol RDP najdete v tématu [Instalace a konfigurace Vzdálené plochy pro připojení k virtuálnímu počítači s Linuxem v Azure](../virtual-machines/linux/use-remote-desktop.md). 


## <a name="unclaim-the-vm"></a>Vrácení virtuálního počítače
Jakmile virtuální počítač přestanete používat, vraťte ho pomocí následujícího postupu: 

1. Na stránce virtuálního počítače vyberte na panelu nástrojů možnost **Vrátit**. 

    ![Vrácení virtuálního počítače](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. Virtuální počítač se před vrácením vypne. Zobrazí se stav této operace v oznámeních.  
3. Kliknutím na název vašeho testovacího prostředí v nabídce navigace s popisem cesty v horní části přejděte zpět na stránku DevTest Lab. 
    
    ![Přejděte zpět do testovacího prostředí](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Zkontrolujte, jestli se virtuální počítač v seznamu **nárokovatelné virtuální počítače** seznam v dolní části.

    
## <a name="next-steps"></a>Další postup
Tento kurz vám ukázal, jak přistupovat k testovacímu prostředí vytvořenému pomocí služby Azure DevTest Labs a jak ho používat. Další informace o přístupu k virtuálním počítačům a o jejich používání v testovacím prostředí najdete v tématu 

> [!div class="nextstepaction"]
> [Postup: Použití virtuálních počítačů v testovacím prostředí](devtest-lab-add-vm.md)

