---
title: Nastavení účtu testovacího prostředí s Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit účet testovacího prostředí pomocí Azure Lab Services, přidat autora testovacího prostředí a zadat image z Marketplace, které budou používat Labs v účtu testovacího prostředí.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239445"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Kurz: Nastavení účtu testovacího prostředí s Azure Lab Services
Účet testovacího prostředí v Azure Lab Services slouží jako centrální účet, ve kterém se spravují testovací prostředí vaší organizace. V účtu testovacího prostředí můžete udělit oprávnění vytvářet testovací prostředí dalším uživatelům a nastavit zásady, které budou platit pro všechna testovací prostředí pod účtem testovacího prostředí. V tomto kurzu se dozvíte, jak vytvořit účet testovacího prostředí. 

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření účtu testovacího prostředí
> * Přidání uživatele do role Autor testovacího prostředí

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
Následující kroky ukazují postup vytvoření účtu testovacího prostředí se službou Azure Lab Services pomocí webu Azure Portal. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby** . Vyberte **DevOps** z **kategorií**. Pak vyberte **služby testovacího prostředí**. Pokud vyberete hvězdičku (`*`) vedle položky **testovací služby**, přidá se do části **Oblíbené** v nabídce vlevo. Od dalšího okamžiku vyberte v části **Oblíbené položky** **testovací služby** .

    ![Všechny služby – > Laboratorní služby](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stránce **testovací služby** vyberte na panelu nástrojů možnost **Přidat** nebo na stránce vyberte tlačítko **vytvořit účet testovacího prostředí** . 

    ![Na stránce testovací účty vyberte Přidat.](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na kartě **základy** na stránce **vytvořit účet testovacího prostředí** proveďte následující akce: 
    1. V části **Lab account name** (Název účtu testovacího prostředí) zadejte název. 
    2. Vyberte **předplatné Azure**, ve kterém chcete účet testovacího prostředí vytvořit.
    3. V poli **Skupina prostředků**vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**a zadejte název skupiny prostředků.
    4. V poli **umístění**vyberte umístění nebo oblast, ve které chcete vytvořit účet testovacího prostředí. 

        ![Stránka s základy účtu testovacího prostředí](../media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Vyberte **Zkontrolovat a vytvořit**.
    6. Zkontrolujte souhrn a vyberte **vytvořit**. 

        ![Kontrola + vytvoření-> Vytvoření](../media/tutorial-setup-lab-account/create-button.png)    
5. Po dokončení nasazení rozbalte **Další kroky**a vyberte **Přejít k prostředku**. 

    ![Přejít na stránku účtu testovacího prostředí](../media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Potvrďte, že se zobrazí stránka **účet testovacího prostředí** . 

    ![Stránka účtu testovacího prostředí](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Přidání uživatele do role Autor testovacího prostředí
Pokud chcete v účtu testovacího prostředí nastavit testovací prostředí v učebně, musí být uživatel v účtu testovacího prostředí členem role **Autor testovacího prostředí**. Přidejte vyučující do role **Autor testovacího prostředí**, abyste jim poskytli oprávnění k vytvoření testovacích prostředí pro jejich třídy:

> [!NOTE]
> Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Pokud plánujete použít stejný uživatelský účet pro vytvoření testovacího prostředí učebny v tomto kurzu, tento krok přeskočte. 

1. Na stránce **účet testovacího prostředí** vyberte **řízení přístupu (IAM)** , vyberte **+ Přidat** na panelu nástrojů a pak na panelu nástrojů vyberte **+ Přidat přiřazení role** . 

    ![Access Control – tlačítko Přidat přiřazení role >](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stránce **Přidat přiřazení role** vyberte možnost **Autor testovacího prostředí** pro **role**, vyberte uživatele, kterého chcete přidat do role testovací tvůrci, a vyberte **Uložit**. 

    ![Přidat autora testovacího prostředí](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili účet testovacího prostředí. Další informace o tom, jak vytvořit prostředí učebny jako profesor, najdete v dalším kurzu:

> [!div class="nextstepaction"]
> [Nastavení testovacího prostředí v učebně](tutorial-setup-classroom-lab.md)

