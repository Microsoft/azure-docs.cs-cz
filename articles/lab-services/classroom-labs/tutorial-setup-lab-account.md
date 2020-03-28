---
title: Nastavení účtu testovacího prostředí s Azure Lab Services | Microsoft Docs
description: Zjistěte, jak nastavit účet testovacího prostředí pomocí služby Azure Lab Services, přidat tvůrce testovacího prostředí a zadat image Marketplace, které budou používat testovací prostředí v účtu testovacího prostředí.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: dba6a4c07691f3d7ec88d8b889e68d6ac7116f07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239445"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Kurz: Nastavení účtu testovacího prostředí s Azure Lab Services
Účet testovacího prostředí v Azure Lab Services slouží jako centrální účet, ve kterém se spravují testovací prostředí vaší organizace. V účtu testovacího prostředí můžete udělit oprávnění vytvářet testovací prostředí dalším uživatelům a nastavit zásady, které budou platit pro všechna testovací prostředí pod účtem testovacího prostředí. V tomto kurzu se dozvíte, jak vytvořit účet testovacího prostředí. 

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření účtu testovacího prostředí
> * Přidání uživatele do role Autor testovacího prostředí

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
Následující kroky ukazují postup vytvoření účtu testovacího prostředí se službou Azure Lab Services pomocí webu Azure Portal. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levé nabídce vyberte **Všechny služby.** Vyberte **možnost DevOps** z **kategorie**. Potom vyberte **Lab Services**. Pokud vyberete`*`hvězdičku ( ) vedle **položky Lab Services**, přidá se do části **OBLÍBENÉ položky** v levé nabídce. Od příštího dne vyberte **lab služby** v části **OBLÍBENÉ POLOŽKY**.

    ![Všechny služby -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stránce **Lab Services** vyberte **Přidat** na panelu nástrojů nebo na stránce vyberte Tlačítko Vytvořit **účet testovacího prostředí.** 

    ![Výběr možnosti Přidat na stránce Účty testovacího prostředí](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na kartě **Základy** na stránce **Vytvořit účet testovacího prostředí** proveďte následující akce: 
    1. V části **Lab account name** (Název účtu testovacího prostředí) zadejte název. 
    2. Vyberte **předplatné Azure**, ve kterém chcete účet testovacího prostředí vytvořit.
    3. Ve **skupině Prostředků**vyberte existující skupinu prostředků nebo vyberte Vytvořit **nový**a zadejte název skupiny prostředků.
    4. V **části Umístění**vyberte umístění nebo oblast, ve které chcete vytvořit účet testovacího prostředí. 

        ![Laboratorní účet - stránka základů](../media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Vyberte **Zkontrolovat a vytvořit**.
    6. Zkontrolujte souhrn a vyberte **Vytvořit**. 

        ![Recenze + vytvořit -> vytvořit](../media/tutorial-setup-lab-account/create-button.png)    
5. Po dokončení nasazení rozbalte **další kroky**a vyberte **Přejít na prostředek**. 

    ![Přejít na stránku účtu testovacího prostředí](../media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Potvrďte, že se zobrazí stránka **Účet testovacího prostředí.** 

    ![Stránka účtu testovacího prostředí](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Přidání uživatele do role Autor testovacího prostředí
Pokud chcete v účtu testovacího prostředí nastavit testovací prostředí v učebně, musí být uživatel v účtu testovacího prostředí členem role **Autor testovacího prostředí**. Chcete-li pedagogům poskytnout oprávnění k vytváření testovacích prostředí pro jejich třídy, přidejte je do role **Tvůrce testovacího prostředí:**

> [!NOTE]
> Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Pokud plánujete použít stejný uživatelský účet k vytvoření testovacího prostředí učebny v tomto kurzu, tento krok přeskočte. 

1. Na stránce **Účet testovacího prostředí** vyberte **ovládací prvek Přístup (IAM),** vyberte **+ Přidat** na panelu nástrojů a pak vyberte + Přidat **přiřazení role** na panelu nástrojů. 

    ![Řízení přístupu -> tlačítko Přidat přiřazení role](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stránce **Přidat přiřazení role** vyberte Tvůrce **lab** pro **roli**, vyberte uživatele, kterého chcete přidat do role Tvůrci testovacího prostředí, a vyberte **Uložit**. 

    ![Přidat tvůrce testovacího prostředí](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili účet testovacího prostředí. Chcete-li se dozvědět o tom, jak vytvořit učebnu laboratoř jako profesor, přejde k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Nastavení testovacího prostředí v učebně](tutorial-setup-classroom-lab.md)

