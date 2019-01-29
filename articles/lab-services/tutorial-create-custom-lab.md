---
title: Vytvoření testovacího prostředí ve službě Azure DevTest Labs | Microsoft Docs
description: V tomto rychlém startu vytvoříte testovací prostředí ve službě Azure DevTest Labs.
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
ms.openlocfilehash: 0530dbac88fcfbfb05ab29d1c5934a468f263034
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096632"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>Kurz: Nastavení testovacího prostředí pomocí Azure DevTest Labs
V tomto kurzu vytvoříte testovací prostředí na webu Azure Portal. Správce testovacího prostředí nastaví testovací prostředí v organizaci, vytvoří virtuální počítače v testovacím prostředí a nakonfiguruje zásady. Uživatelé testovacího prostředí (například vývojář a testeři) nárokují virtuální počítače v testovacím prostředí, připojují se k nim a používají je. 

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí
> * Přidání virtuálních počítačů do testovacího prostředí
> * Přidání uživatele do role uživatele testovacího prostředí

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-lab"></a>Vytvoření testovacího prostředí
Následující kroky ukazují postup vytvoření testovacího prostředí ve službě Azure DevTest Labs pomocí webu Azure Portal. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V hlavní nabídce na levé straně vyberte **Vytvořit prostředek** (v horní části seznamu), přejděte na **Vývojářské nástroje** a klikněte na **DevTest Labs**. 

    ![Nová nabídka DevTest Lab](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. V okně **Create DevTest Lab** (Vytvořit DevTestLab) proveďte následující akce: 
    1. Do pole **Lab name** (Název testovacího prostředí) zadejte název testovacího prostředí. 
    2. V části **Předplatné** vyberte předplatné, ve kterém chcete testovací prostředí vytvořit. 
    3. V části **Skupina prostředků** vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
    4. V části **Umístění** vyberte umístění nebo oblast, ve které chcete testovací prostředí vytvořit. 
    5. Vyberte **Vytvořit**. 
    6. Zaškrtněte **Připnout na řídicí panel**. Jakmile testovací prostředí vytvoříte, zobrazí se na řídicím panelu. 

        ![Vytvoření části testovacího prostředí služby DevTest Labs](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)
2. Potvrďte, že testovací prostředí úspěšně vytvořeno pohledem oznámení. Vyberte **přejít k prostředku**.  

    ![Oznámení](./media/tutorial-create-custom-lab/creation-notification.png)
3. Zkontrolujte, jestli se **DevTest Lab** stránky pro vaše testovací prostředí. 

    ![Domovská stránka testovacího prostředí.](./media/tutorial-create-custom-lab/lab-home-page.png)

## <a name="add-a-vm-to-the-lab"></a>Přidání virtuálního počítače do testovacího prostředí

1. Na stránce **DevTest Lab** vyberte na panelu nástrojů **+ Add** (+ Přidat). 

    ![Tlačítko Přidat](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Na **vyberte bázi** stránce, hledání pomocí klíčového slova (například: Windows, Ubuntu) a v seznamu vyberte jednu z základní Image. 
1. Na stránce **Virtual machine** (Virtuální počítač) proveďte následující akce: 
    1. V části **Virtual machine name** (Název virtuálního počítače) zadejte název pro virtuální počítač. 
    2. V části **User name** (Uživatelské jméno) zadejte jméno uživatele, který může k virtuálnímu počítači přistupovat. 
    3. Pro **heslo**, zadejte heslo pro tohoto uživatele. 

        ![Výběr základní image](./media/tutorial-create-custom-lab/new-virtual-machine.png)
1. Vyberte **upřesňující nastavení** kartu.
    1. V části **Make this machine claimable** (Nastavit tento počítač jako nárokovatelný) vyberte **Yes** (Ano).
    2. Ujistěte se, že je **počet instancí** nastavený na **1**. Pokud je nastavený na **2**, vytvoří se 2 virtuální počítače s názvy: `<base image name>00' and <base image name>01`. Například: `win10vm00` a `win10vm01`     
    3. Vyberte **odeslat**. 

        ![Výběr základní image](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
    9. Stav virtuálního počítače se zobrazí v seznamu **Nárokovatelné virtuální počítače**. Vytvoření virtuálního počítače může trvat přibližně 25 minut. Virtuální počítač se vytvoří v samostatné skupině prostředků Azure, jejíž název začíná názvem aktuální skupiny prostředků, která má testovací prostředí. Pokud je testovací prostředí v `labrg`, virtuální počítač se může vytvořit ve skupině prostředků `labrg3988722144002`. 

        ![Stav vytváření virtuálního počítače](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. Jakmile se virtuální počítač vytvoří, zobrazí se v seznamu **Nárokovatelné virtuální počítače**. 

    > [!NOTE] 
    > Při přidávání virtuálního počítače s Linuxem do testovacího prostředí můžete povolit přístup k tomuto virtuálnímu počítači přes SSH a protokol RDP. Pokud přístup nepovolíte během vytváření virtuálního počítače, můžete ručně přidat pravidla do skupiny zabezpečení sítě přidružené k virtuálnímu počítači a otevřít porty pro SSH a protokol RDP.

## <a name="add-a-user-to-the-lab-user-role"></a>Přidání uživatele do role uživatele testovacího prostředí

1. V levé nabídce vyberte **Configuration and policies** (Konfigurace a zásady). 

    ![Konfigurace a zásady](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. Vyberte **řízení přístupu (IAM)** z nabídky a vybereme **+ přidat přiřazení role** na panelu nástrojů. 

    ![Přidání přiřazení role – tlačítko](./media/tutorial-create-custom-lab/add-role-assignment-button.png)
1. Na stránce **Add permissions** (Přidat oprávnění) proveďte následující akce:
    1. V části **Role** (Role) vyberte **DevTest Labs User** (Uživatel služby DevTest Labs). 
    2. Vyberte **uživatele**, kterého chcete přidat. 
    3. Vyberte **Uložit**.

        ![Přidání uživatele](./media/tutorial-create-custom-lab/add-user.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Další kurz vám ukáže, jak může uživatel testovacího prostředí nárokovat virtuální počítač a připojit se k němu v testovacím prostředí. Pokud nechcete tento kurz procházet a nechcete vyčistit prostředky vytvořené v tomto kurzu, postupujte takto: 

1. Na webu Azure Portal vyberte v nabídce možnost **Skupiny prostředků**. 

    ![Skupiny prostředků](./media/tutorial-create-custom-lab/resource-groups.png)
1. Vyberte skupinu prostředků, ve které jste vytvořili testovací prostředí. 
1. Na panelu nástrojů vyberte **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků se odstraní všechny prostředky ve skupině, včetně testovacího prostředí. 

    ![Skupina prostředků testovacího prostředí](./media/tutorial-create-custom-lab/lab-resource-group.png)
1. Tento postup opakujte k odstranění další skupiny prostředků vytvořené pro vás s názvem `<your resource group name><random numbers>`. Například: `splab3988722144001`. Virtuální počítače se vytvoří v této skupině prostředků, nikoli ve skupině prostředků, ve které existuje testovací prostředí. 

## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili testovací prostředí s virtuálním počítačem a nastavili jste do něj uživateli přístup. Pokud chcete získat informace o tom, jak k testovacímu prostředí přistupovat jako uživatel testovacího prostředí, přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Kurz: Přístup k testovacím prostředí](tutorial-use-custom-lab.md)

