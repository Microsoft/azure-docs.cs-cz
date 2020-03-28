---
title: Vytvoření testovacího prostředí ve službě Azure DevTest Labs | Microsoft Docs
description: V tomto kurzu vytvoříte testovací prostředí v Azure DevTest Labs pomocí portálu Azure. Správce testovacího prostředí nastaví testovací prostředí, vytvoří virtuální počítače v testovacím prostředí a nakonfiguruje zásady.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 44539c6779afaece6d955a907819ef82d8cd7d5a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239907"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>Kurz: Nastavení testovacího prostředí ve službě Azure DevTest Labs
V tomto kurzu vytvoříte testovací prostředí na webu Azure Portal. Správce testovacího prostředí nastaví testovací prostředí v organizaci, vytvoří virtuální počítače v testovacím prostředí a nakonfiguruje zásady. Uživatelé testovacího prostředí (například vývojář a testeři) nárokují virtuální počítače v testovacím prostředí, připojují se k nim a používají je. 

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí
> * Přidání virtuálních počítačů do testovacího prostředí
> * Přidání uživatele do role uživatele testovacího prostředí

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-a-lab"></a>Vytvoření testovacího prostředí
Následující kroky ukazují postup vytvoření testovacího prostředí ve službě Azure DevTest Labs pomocí webu Azure Portal. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
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
2. Zkontrolujte, zda je testovací prostředí úspěšně vytvořeno při pohledu na oznámení. Vyberte **Přejít na zdroj**.  

    ![Oznámení](./media/tutorial-create-custom-lab/creation-notification.png)
3. Potvrďte, že se zobrazí **devTest Lab** stránku pro vaše laboratoř. 

    ![Domovská stránka pro vaši laboratoř](./media/tutorial-create-custom-lab/lab-home-page.png)

## <a name="add-a-vm-to-the-lab"></a>Přidání virtuálního počítače do testovacího prostředí

1. Na stránce **DevTest Lab** vyberte na panelu nástrojů **+ Add** (+ Přidat). 

    ![Tlačítko Přidat](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Na stránce **Choose a base** (Zvolit základní image) vyhledejte klíčové slovo (například Windows nebo Ubuntu) a vyberte jednu ze základních imagí v seznamu. 
1. Na stránce **Virtual machine** (Virtuální počítač) proveďte následující akce: 
    1. V části **Virtual machine name** (Název virtuálního počítače) zadejte název pro virtuální počítač. 
    2. V části **User name** (Uživatelské jméno) zadejte jméno uživatele, který může k virtuálnímu počítači přistupovat. 
    3. Jako **Heslo** zadejte heslo pro tohoto uživatele. 

        ![Výběr základní image](./media/tutorial-create-custom-lab/new-virtual-machine.png)
1. Vyberte **karta Upřesnit nastavení.**
    1. V části **Make this machine claimable** (Nastavit tento počítač jako nárokovatelný) vyberte **Yes** (Ano).
    2. Ujistěte se, že je **počet instancí** nastavený na **1**. Pokud je nastavený na **2**, vytvoří se 2 virtuální počítače s názvy: `<base image name>00' and <base image name>01`. Například: `win10vm00` a `win10vm01`     
    3. Vyberte **Odeslat**. 

        ![Výběr základní image](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
    9. Stav virtuálního počítače se zobrazí v seznamu **Nárokovatelné virtuální počítače**. Vytvoření virtuálního počítače může trvat přibližně 25 minut. Virtuální počítač se vytvoří v samostatné skupině prostředků Azure, jejíž název začíná názvem aktuální skupiny prostředků, která má testovací prostředí. Pokud je testovací prostředí v `labrg`, virtuální počítač se může vytvořit ve skupině prostředků `labrg3988722144002`. 

        ![Stav vytváření virtuálního počítače](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. Jakmile se virtuální počítač vytvoří, zobrazí se v seznamu **Nárokovatelné virtuální počítače**. 

    > [!NOTE] 
    > Na stránce **Upřesnit nastavení** můžete nakonfigurovat veřejnou, soukromou nebo sdílenou IP adresu pro virtuální počítač. Když je **sdílená IP adresa** povolena, Azure DevTest Labs automaticky povolí RDP pro virtuální počítače s Windows a SSH pro virtuální počítače s Linuxem. Pokud vytvoříte virtuální chod s **veřejnými IP** adresami, RDP a SSH jsou povoleny bez jakýchkoli změn z DevTest Labs.  

## <a name="add-a-user-to-the-lab-user-role"></a>Přidání uživatele do role uživatele testovacího prostředí

1. V levé nabídce vyberte **Configuration and policies** (Konfigurace a zásady). 

    ![Konfigurace a zásady](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. V nabídce vyberte **Ovládací prvek přístupu (IAM)** a na panelu nástrojů vyberte **+ Přidat přiřazení role.** 

    ![Tlačítko Přidat přiřazení role](./media/tutorial-create-custom-lab/add-role-assignment-button.png)
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

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili testovací prostředí s virtuálním počítačem a nastavili jste do něj uživateli přístup. Pokud chcete získat informace o tom, jak k testovacímu prostředí přistupovat jako uživatel testovacího prostředí, přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Kurz: Přístup k testovacímu prostředí](tutorial-use-custom-lab.md)

