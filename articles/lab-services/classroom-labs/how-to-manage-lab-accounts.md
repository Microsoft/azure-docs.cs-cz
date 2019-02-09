---
title: Správa účtů testovacího prostředí ve službě Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí v rámci předplatného Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: 7e3142e0274f2328d3e0c8a3e6f9a2e4c3d45d87
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959133"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Správa účtů testovacího prostředí ve službě Azure Lab Services 
Ve službě Azure Lab Services účet testovacího prostředí je kontejner pro spravované testovací prostředí, jako je například testovací prostředí v učebnách. Správce nastaví účet testovacího prostředí pomocí Azure Lab Services a poskytuje přístup k vlastníků testovacího prostředí, kteří můžou vytvářet testovací prostředí v rámci účtu. Tento článek popisuje, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranění účtu služby testovacího prostředí.

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
Následující kroky ukazují postup vytvoření účtu testovacího prostředí se službou Azure Lab Services pomocí webu Azure Portal. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v nabídce vlevo. Vyberte **účtů testovacího prostředí** v **DEVOPS** oddílu. Pokud vyberte hvězdičku (`*`) vedle položky **účtů testovacího prostředí**, přidá se do **Oblíbené** části v nabídce vlevo. V příštím a vyšší, vyberte **účtů testovacího prostředí** pod **Oblíbené**.

    ![Všechny služby -> účtů testovacího prostředí](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na **účtů testovacího prostředí** stránce **přidat** na panelu nástrojů. 

    ![Vyberte možnost přidat na stránku účtů testovacího prostředí](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na **účet testovacího prostředí** stránce, proveďte následující akce: 
    1. V části **Lab account name** (Název účtu testovacího prostředí) zadejte název. 
    2. Vyberte **předplatné Azure**, ve kterém chcete účet testovacího prostředí vytvořit.
    3. V části **Skupina prostředků** vyberte **Vytvořit novou** a zadejte název skupiny prostředků.
    4. V části **Umístění** vyberte umístění nebo oblast, ve které chcete účet testovacího prostředí vytvořit. 
    5. Vyberte **Vytvořit**. 

        ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Vyberte **ikonu zvonku** na panelu nástrojů (**oznámení**), potvrďte, že nasazení proběhlo úspěšně a pak vyberte **přejít k prostředku**. 

    Můžete také vybrat **aktualizovat** na **účtů testovacího prostředí** stránky a vyberte účet testovacího prostředí, který jste vytvořili. 

    ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Zobrazí se následující stránka **účtu testovacího prostředí**:

    ![Stránka účtu testovacího prostředí](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Přidání uživatele do role Autor testovacího prostředí
Pokud chcete v účtu testovacího prostředí nastavit testovací prostředí v učebně, musí být uživatel v účtu testovacího prostředí členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Pokud máte v úmyslu použít k vytvoření testovacího prostředí v učebně stejný uživatelský účet, můžete tento krok přeskočit. Pokud chcete k vytvoření testovacího prostředí v učebně použít jiný uživatelský účet, postupujte takto: 

Přidejte vyučující do role **Autor testovacího prostředí**, abyste jim poskytli oprávnění k vytvoření testovacích prostředí pro jejich třídy:

1. Na **účet testovacího prostředí** stránce **řízení přístupu (IAM)** a klikněte na tlačítko **+ přidat přiřazení role** na panelu nástrojů. 

    ![Řízení přístupu -> tlačítko Přidat přiřazení Role](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na **přidat přiřazení role** stránce **Autor testovacího prostředí** pro **Role**, vyberte uživatele, kterou chcete přidat do role Tvůrce prostředí a vyberte **Uložit**. 

    ![Přidat Autor testovacího prostředí](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="specify-marketplace-images-available-to-lab-creators"></a>Zadejte Image z Marketplace k dispozici pro tvůrce prostředí
Jako vlastník účtu testovacího prostředí můžete určit image z Marketplace, které můžou autoři testovacích prostředí použít k vytváření testovacích prostředí v tomto účtu testovacího prostředí. 

1. V nabídce vlevo vyberte **Marketplace images** (Image z Marketplace). Ve výchozím nastavení se zobrazí úplný seznam imagí (povolených i zakázaných). Pomocí možnosti **Enabled only**/**Disabled only** (Jenom povolené / Jenom zakázané) v rozevíracím seznamu v horní části můžete seznam filtrovat, aby se v něm zobrazovaly jen povolené/zakázané image. 
    
    ![Stránka imagí v Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    V tomto seznamu se zobrazí pouze image z Marketplace, které splňují následující podmínky:
        
    - Vytváří jeden virtuální počítač.
    - Ke zřízení virtuálních počítačů používají Azure Resource Manager.
    - Nevyžadují zakoupení dalšího licenčního plánu.
2. Pokud chcete povolenou image v Marketplace **zakázat**, proveďte některý z těchto kroků: 
    1. V posledním sloupci vyberte **…** (tři tečky) a zvolte **Disable image** (Zakázat image). 

        ![Zakázání jedné image](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Zaškrtnutím políček před názvy imagí v seznamu můžete vybrat několik imagí. Potom zvolte **Disable selected images** (Zakázat vybrané image). 

        ![Zakázání několika imagí](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Stejně tak pokud chcete některou image z Marketplace **povolit**, proveďte některý z těchto kroků: 
    1. V posledním sloupci vyberte **…** (tři tečky) a zvolte **Enable image** (Povolit image). 
    2. Zaškrtnutím políček před názvy imagí v seznamu můžete vybrat několik imagí. Potom zvolte **Enable selected images** (Povolit vybrané image). 

## <a name="view-lab-accounts"></a>Zobrazení účtů testovacího prostředí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny prostředky** z nabídky. 
3. Vyberte **účtů testovacího prostředí** pro **typ**. 
    Můžete také filtrovat podle předplatného, skupinu prostředků, míst a značky. 

    ![Všechny prostředky -> účtů testovacího prostředí](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Odstranění účtu služby testovacího prostředí
Postupujte podle pokynů v předchozí části, která se zobrazí v seznamu účtů testovacího prostředí. Odstranění účtu služby testovacího prostředí použijte následující pokyny: 

1. Vyberte **účet testovacího prostředí** , kterou chcete odstranit. 
2. Vyberte **odstranit** z panelu nástrojů. 

    ![Účtů testovacího prostředí -> tlačítko Odstranit](../media/how-to-manage-lab-accounts/delete-button.png)
1. Typ **Ano** pro potvrzení.
1. Vyberte **Odstranit**. 

    ![Odstranit účet testovacího prostředí – potvrzení](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Zobrazení a správa testovacích prostředí v rámci účtu testovacího prostředí

1. Na **účet testovacího prostředí** stránce **Labs** v nabídce vlevo.

    ![Testovací prostředí v rámci účtu](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Zobrazí **seznamu testovacích prostředí** na účet s následujícími informacemi: 
    1. Název testovacího prostředí.
    2. Datum vytvoření testovacího prostředí. 
    3. E-mailová adresa uživatele, který vytvořil testovacího prostředí. 
    4. Maximální počet uživatelů povolených v testovacím prostředí. 
    5. Stav testovacího prostředí. 

## <a name="delete-a-lab-in-the-lab-account"></a>Odstranit testovací prostředí v účtu testovacího prostředí
Postupujte podle pokynů v předchozí části zobrazíte seznam testovacích prostředí v rámci účtu testovacího prostředí.

1. Vyberte **... (tři tečky)** a vyberte **odstranit**. 

    ![Odstranění testovacího prostředí – tlačítko](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Vyberte **Ano** na upozornění. 

    ![Potvrzení odstranění testovacího prostředí](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako vlastník testovacího prostředí vytvoření a správa testovacích prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurovat a spravovat využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)