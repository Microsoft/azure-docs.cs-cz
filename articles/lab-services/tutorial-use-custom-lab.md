---
title: Přístup k vlastnímu testovacímu prostředí v Azure DevTest Labs | Microsoft Docs
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
ms.date: 04/09/2018
ms.author: spelluru
ms.openlocfilehash: ce4522673bac56f73944413d102b7cb36cf93f30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-access-a-custom-lab-in-azure-devtest-labs"></a>Kurz: Přístup k vlastnímu testovacímu prostředí v Azure DevTest Labs
V tomto kurzu použijete vlastní testovací prostředí vytvořené v tématu [Kurz: Vytvoření vlastní testovacího prostředí](tutorial-create-custom-lab.md).

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Nárokování virtuálního počítače ve vlastním testovacím prostředí
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

## <a name="unclaim-the-vm"></a>Vrácení virtuálního počítače
Jakmile virtuální počítač přestanete používat, vraťte ho pomocí následujícího postupu: 

1. Na stránce virtuálního počítače vyberte na panelu nástrojů možnost **Vrátit**. 

    ![Vrácení virtuálního počítače](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. Virtuální počítač se před vrácením vypne. 

    ![Stav vrácení](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. Po dokončení operace vrácení se virtuální počítač zobrazí dole v seznamu **Nárokovatelné virtuální počítače**. 
    
## <a name="next-steps"></a>Další kroky
Tento kurz vám ukázal, jak přistupovat k vlastnímu testovacímu prostředí vytvořenému pomocí služby Azure DevTest Labs a jak ho používat. Další informace o přístupu k virtuálním počítačům a o jejich používání ve vlastním testovacím prostředí najdete v tématu 

> [!div class="nextstepaction"]
> [Postupy: Použití virtuálních počítačů ve vlastním testovacím prostředí](devtest-lab-add-vm.md)

