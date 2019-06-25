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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 6f283ce007e96547e01a01a3753ddcb60574bfc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412803"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Správa účtů testovacího prostředí ve službě Azure Lab Services 
Ve službě Azure Lab Services účet testovacího prostředí je kontejner pro testovací prostředí spravované typy, jako jsou testovací prostředí v učebnách. Správce nastaví účet testovacího prostředí pomocí Azure Lab Services a poskytuje přístup k vlastníků testovacího prostředí, kteří můžou vytvářet testovací prostředí v rámci účtu. Tento článek popisuje, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranění účtu služby testovacího prostředí.

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
    5. Vyberte existující **sdílené bitové kopie Galerie** nebo ji vytvořte. Šablonu virtuálního počítače můžete uložit v galerii sdílené bitové kopie pro ni znovu použije jinými uživateli. Podrobné informace o galeriích sdílené bitové kopie, naleznete v tématu [sdílené bitové kopie Galerie v Azure Lab Services](how-to-use-shared-image-gallery.md).
    6. Pro **partnerská virtuální síť**, vyberte partnerská virtuální síť (VNet) pro síť testovacího prostředí. Testovací prostředí vytvořené v rámci tohoto účtu připojení k vybrané virtuální síti a mají přístup k prostředkům ve vybrané virtuální síti. 
    7. Zadejte **rozsah adres** pro virtuální počítače v testovacím prostředí. Rozsah adres by měl být v zápisu classless Inter-Domain routing (CIDR) (Příklad: 10.20.0.0/23). Virtuální počítače v testovacím prostředí se vytvoří v tomto rozsahu adres. Další informace najdete v tématu [zadejte rozsah adres pro virtuální počítače v testovacím prostředí](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. Pro **Autor testovacího prostředí povolit vybrat umístění testovacího prostředí** pole, zda má být creators testovacího prostředí bude moci vybrat umístění testovacího prostředí. Ve výchozím nastavení bude možnost zakázána. Pokud je zakázáno, Tvůrce prostředí nelze zadat umístění pro testovací prostředí, které se vytváří. Tato cvičení se vytvoří v nejbližší geografické umístění účtu testovacího prostředí. Pokud je povolena, Autor testovacího prostředí můžete vybrat umístění v době vytvoření testovacího prostředí.      
    9. Vyberte **Vytvořit**. 

        ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Vyberte **ikonu zvonku** na panelu nástrojů (**oznámení**), potvrďte, že nasazení proběhlo úspěšně a pak vyberte **přejít k prostředku**. 

    Můžete také vybrat **aktualizovat** na **účtů testovacího prostředí** stránky a vyberte účet testovacího prostředí, který jste vytvořili. 

    ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Zobrazí se následující stránka **účtu testovacího prostředí**:

    ![Stránka účtu testovacího prostředí](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Zobrazení účtů testovacího prostředí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny prostředky** z nabídky. 
3. Vyberte **účtů testovacího prostředí** pro **typ**. 
    Můžete také filtrovat podle předplatného, skupinu prostředků, míst a značky. 

    ![Všechny prostředky -> účtů testovacího prostředí](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

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

## <a name="delete-a-lab-account"></a>Odstranění účtu služby testovacího prostředí
Postupujte podle pokynů v předchozí části, která se zobrazí v seznamu účtů testovacího prostředí. Odstranění účtu služby testovacího prostředí použijte následující pokyny: 

1. Vyberte **účet testovacího prostředí** , kterou chcete odstranit. 
2. Vyberte **odstranit** z panelu nástrojů. 

    ![Účtů testovacího prostředí -> tlačítko Odstranit](../media/how-to-manage-lab-accounts/delete-button.png)
1. Typ **Ano** pro potvrzení.
1. Vyberte **Odstranit**. 

    ![Odstranit účet testovacího prostředí – potvrzení](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)


## <a name="next-steps"></a>Další postup
Přečtěte si následující článek: [Postup konfigurace účtů testovacího prostředí](how-to-configure-lab-accounts.md).