---
title: Vytvoření vlastního testovacího prostředí pomocí Azure DevTest Labs | Microsoft Docs
description: V tomto rychlém startu vytvoříte vlastní testovací prostředí pomocí služby Azure DevTest Labs.
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
ms.openlocfilehash: df70322a2d6562fce8da17fde652dd0dfbfcaec4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-custom-lab-by-using-azure-devtest-labs"></a>Kurz: Nastavení vlastního testovacího prostředí pomocí Azure DevTest Labs
V tomto kurzu vytvoříte vlastní testovací prostředí pomocí portálu Azure Portal. Správce testovacího prostředí nastaví testovací prostředí v organizaci, vytvoří virtuální počítače v testovacím prostředí a nakonfiguruje zásady. Uživatelé testovacího prostředí (například vývojář a testeři) nárokují virtuální počítače v testovacím prostředí, připojují se k nim a používají je. 

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření vlastního testovacího prostředí
> * Přidání virtuálních počítačů do testovacího prostředí
> * Přidání uživatele do role uživatele testovacího prostředí

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-custom-lab"></a>Vytvoření vlastního testovacího prostředí
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

## <a name="add-a-vm-to-the-lab"></a>Přidání virtuálního počítače do testovacího prostředí

1. Na stránce **DevTest Lab** vyberte na panelu nástrojů **+ Add** (+ Přidat). 

    ![Tlačítko Přidat](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Na stránce **Choose a base** (Zvolit základní image) vyhledejte klíčové slovo **Ubuntu** a vyberte jednu ze základních imagí v seznamu. 
1. Na stránce **Virtual machine** (Virtuální počítač) proveďte následující akce: 
    1. V části **Virtual machine name** (Název virtuálního počítače) zadejte název pro virtuální počítač. 
    2. V části **User name** (Uživatelské jméno) zadejte jméno uživatele, který může k virtuálnímu počítači přistupovat. 
    3. V části **Type a value** (Zadejte hodnotu) zadejte heslo pro tohoto uživatele. 
    4. Vyberte **Upřesňující nastavení**.
    5. V části **Make this machine claimable** (Nastavit tento počítač jako nárokovatelný) vyberte **Yes** (Ano).
    6. Ujistěte se, že je **počet instancí** nastavený na **1**. Pokud je nastavený na **2**, vytvoří se 2 virtuální počítače s názvy: `<base image name>00' and <base image name>01`. Například: `win10vm00` a `win10vm01` 
    7. Pokud chcete zavřít stránku **Advanced** (Upřesnit), klikněte na **OK**. 
    8. Vyberte **Vytvořit**. 

        ![Výběr základní image](./media/tutorial-create-custom-lab/new-virtual-machine.png)
    9. Stav virtuálního počítače se zobrazí v seznamu **Nárokovatelné virtuální počítače**. Vytvoření virtuálního počítače může trvat přibližně 25 minut. Virtuální počítač se vytvoří v samostatné skupině prostředků Azure, jejíž název začíná názvem aktuální skupiny prostředků, která má testovací prostředí. Pokud je testovací prostředí v `labrg`, virtuální počítač se může vytvořit ve skupině prostředků `labrg3988722144002`. 

        ![Stav vytváření virtuálního počítače](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. Jakmile se virtuální počítač vytvoří, zobrazí se v seznamu **Nárokovatelné virtuální počítače**. 

## <a name="add-a-user-to-the-lab-user-role"></a>Přidání uživatele do role uživatele testovacího prostředí

1. V levé nabídce vyberte **Configuration and policies** (Konfigurace a zásady). 

    ![Konfigurace a zásady](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. V nabídce vyberte **Access control (IAM)** (Řízení přístupu (IAM)) a na panelu nástrojů vyberte **+ Add** (+Přidat). 

    ![Řízení přístupu – tlačítko Přidat uživatele](./media/tutorial-create-custom-lab/access-control-add.png)
1. Na stránce **Add permissions** (Přidat oprávnění) proveďte následující akce:
    1. V části **Role** (Role) vyberte **DevTest Labs User** (Uživatel služby DevTest Labs). 
    2. Vyberte **uživatele**, kterého chcete přidat. 
    3. Vyberte **Uložit**.

        ![Přidání oprávnění](./media/tutorial-create-custom-lab/add-lab-user.png)
4. Pokud chcete zavřít okno **Configuration and policies - Access control (IAM)** (Konfigurace a zásady – řízení přístupu (IAM)), vyberte **X** v pravém rohu. 

## <a name="cleanup-resources"></a>Vyčištění prostředků
Další kurz vám ukáže, jak může uživatel testovacího prostředí nárokovat virtuální počítač a připojit se k němu v testovacím prostředí. Pokud nechcete tento kurz procházet a nechcete vyčistit prostředky vytvořené v tomto kurzu, postupujte takto: 

1. Na webu Azure Portal vyberte v nabídce možnost **Skupiny prostředků**. 
2. Vyberte skupinu prostředků, ve které jste vytvořili testovací prostředí. 
3. Na panelu nástrojů vyberte **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků se odstraní všechny prostředky ve skupině, včetně testovacího prostředí. 
4. Tento postup opakujte k odstranění další skupiny prostředků vytvořené pro vás s názvem `<your resource group name><random numbers>`. Například: `splab3988722144001`. Virtuální počítače se vytvoří v této skupině prostředků, nikoli ve skupině prostředků, ve které existuje testovací prostředí. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili vlastní testovací prostředí s virtuálním počítačem a udělili jste uživateli přístup k tomuto testovacímu prostředí. Pokud chcete získat informace o tom, jak k testovacímu prostředí přistupovat jako uživatel testovacího prostředí, přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Kurz: Přístup k vlastnímu testovacímu prostředí](tutorial-use-custom-lab.md)

