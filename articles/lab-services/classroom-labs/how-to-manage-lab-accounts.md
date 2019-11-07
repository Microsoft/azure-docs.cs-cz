---
title: Správa účtů testovacího prostředí v Azure Lab Services | Microsoft Docs
description: Naučte se, jak vytvořit účet testovacího prostředí, Zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí v předplatném Azure.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: ee64780bca13bf497793dc90ad22d3eaf91949a6
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606336"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Správa účtů testovacího prostředí v Azure Lab Services 
V Azure Lab Services účet testovacího prostředí je kontejner pro spravované typy testovacích prostředí, jako je například učeben Labs. Správce nastaví účet testovacího prostředí pomocí Azure Lab Services a poskytne přístup k vlastníkům testovacího prostředí, kteří můžou v účtu vytvářet Labs. Tento článek popisuje, jak vytvořit účet testovacího prostředí, Zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí.

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
Následující kroky ukazují postup vytvoření účtu testovacího prostředí se službou Azure Lab Services pomocí webu Azure Portal. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby** . V části **DevOps** vyberte **účty testovacího prostředí** . Pokud vyberete hvězdičku (`*`) vedle **účtů testovacího prostředí**, přidá se do části **Oblíbené** v nabídce vlevo. Od dalšího okamžiku vyberte **účty testovacího prostředí** v části **Oblíbené**.

    ![Všechny služby – účty > Lab](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stránce **testovací účty** vyberte **Přidat** na panelu nástrojů. 

    ![Na stránce testovací účty vyberte Přidat.](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na stránce **testovací účet** proveďte následující akce: 
    1. V části **Lab account name** (Název účtu testovacího prostředí) zadejte název. 
    2. Vyberte **předplatné Azure**, ve kterém chcete účet testovacího prostředí vytvořit.
    3. V části **Skupina prostředků** vyberte **Vytvořit novou** a zadejte název skupiny prostředků.
    4. V části **Umístění** vyberte umístění nebo oblast, ve které chcete účet testovacího prostředí vytvořit. 
    5. Vyberte existující **galerii sdílených imagí** nebo ji vytvořte. Virtuální počítač šablony můžete uložit do galerie sdílených imagí, aby ho mohli znovu použít jiní uživatelé. Podrobné informace o galeriích sdílených imagí najdete [v tématu použití Galerie sdílených imagí v Azure Lab Services](how-to-use-shared-image-gallery.md).
    6. Pro **partnerský virtuální síť**vyberte partnerský virtuální síť (VNET) pro síť laboratoře. Laboratoře vytvořené v tomto účtu jsou připojené k vybrané virtuální síti a mají přístup k prostředkům ve vybrané virtuální síti. 
    7. Zadejte **Rozsah adres** pro virtuální počítače v testovacím prostředí. Rozsah adres by měl být v notaci směrování mezi doménami (například: 10.20.0.0/23). Virtuální počítače v testovacím prostředí se vytvoří v tomto rozsahu adres. Další informace najdete v tématu [určení rozsahu adres pro virtuální počítače v testovacím prostředí](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. V poli **umožnit autor testovacího prostředí vybrat umístění testovacího prostředí** určete, jestli chcete, aby tvůrci testovacího prostředí mohli vybrat umístění pro testovací prostředí. Ve výchozím nastavení je tato možnost zakázaná. Pokud je zakázaný, tvůrci testovacího prostředí nemůžou určit umístění pro testovací prostředí, které vytváří. Laboratoře se vytvoří v nejbližším zeměpisném umístění účtu testovacího prostředí. Když je tato možnost povolená, může tvůrce testovacího prostředí vybrat umístění v době vytváření testovacího prostředí.      
    9. Vyberte **Vytvořit**. 

        ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Vyberte **ikonu zvonku** na panelu nástrojů (**oznámení**), potvrďte, že nasazení proběhlo úspěšně, a pak vyberte **Přejít k prostředku**. 

    Případně vyberte možnost **aktualizovat** na stránce **účty testovacího prostředí** a vyberte účet testovacího prostředí, který jste vytvořili. 

    ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Zobrazí se následující stránka **účtu testovacího prostředí**:

    ![Stránka účtu testovacího prostředí](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Zobrazit účty testovacího prostředí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vyberte **všechny prostředky** . 
3. Vyberte **účty testovacího prostředí** pro daný **typ**. 
    Můžete také filtrovat podle předplatného, skupiny prostředků, umístění a značek. 

    ![Všechny prostředky – účty > Lab](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Zobrazení a Správa cvičení v účtu testovacího prostředí

1. Na stránce **účet testovacího prostředí** vyberte v nabídce vlevo možnost **Labs** .

    ![Cvičení v účtu](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. V účtu se zobrazí **seznam cvičení** s následujícími informacemi: 
    1. Název testovacího prostředí.
    2. Datum vytvoření testovacího prostředí. 
    3. E-mailová adresa uživatele, který testovací prostředí vytvořil 
    4. Maximální počet uživatelů povolený do testovacího prostředí. 
    5. Stav testovacího prostředí. 

## <a name="delete-a-lab-in-the-lab-account"></a>Odstranění testovacího prostředí v účtu testovacího prostředí
Postupujte podle pokynů v předchozí části, kde se zobrazí seznam cvičení v účtu testovacího prostředí.

1. Vybrat **... (tři tečky)** a vyberte **Odstranit**. 

    ![Tlačítko pro odstranění testovacího prostředí](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. U zprávy upozornění vyberte **Ano** . 

    ![Potvrdit odstranění testovacího prostředí](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Odstranění účtu testovacího prostředí
Postupujte podle pokynů v předchozí části, které zobrazují účty testovacího prostředí v seznamu. K odstranění účtu testovacího prostředí použijte následující pokyny: 

1. Vyberte **účet testovacího prostředí** , který chcete odstranit. 
2. Na panelu nástrojů vyberte **Odstranit** . 

    ![Účty testovacího prostředí – > tlačítko Odstranit](../media/how-to-manage-lab-accounts/delete-button.png)
1. Pro potvrzení zadejte **Ano** .
1. Vyberte **Odstranit**. 

    ![Odstranit účet testovacího prostředí – potvrzení](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Ke správě účtů testovacího prostředí můžete použít také modul PowerShellu AZ. LabServices (Preview). Další informace najdete na [domovské stránce AZ. LabServices na GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Další kroky
Přečtěte si následující článek: [jak nakonfigurovat účty testovacího prostředí](how-to-configure-lab-accounts.md).