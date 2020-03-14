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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: c82b5d02ab3928eb0472f2a047cdca2238bf0b63
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284288"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Správa účtů testovacího prostředí v Azure Lab Services 
V Azure Lab Services účet testovacího prostředí je kontejner pro spravované typy testovacích prostředí, jako je například učeben Labs. Správce nastaví účet testovacího prostředí pomocí Azure Lab Services a poskytne přístup k vlastníkům testovacího prostředí, kteří můžou v účtu vytvářet Labs. Tento článek popisuje, jak vytvořit účet testovacího prostředí, Zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí.

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
Následující kroky ukazují postup vytvoření účtu testovacího prostředí se službou Azure Lab Services pomocí webu Azure Portal. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby** . V části **DevOps** vyberte **účty testovacího prostředí** . Pokud vyberete hvězdičku (`*`) vedle **účtů testovacího prostředí**, přidá se do části **Oblíbené** v nabídce vlevo. Od dalšího okamžiku vyberte **účty testovacího prostředí** v části **Oblíbené**.

    ![Všechny služby – účty > Lab](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stránce **testovací účty** vyberte **Přidat** na panelu nástrojů nebo **vytvořit účet testovacího prostředí** na stránce. 

    ![Na stránce testovací účty vyberte Přidat.](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na kartě **základy** na stránce **vytvořit účet testovacího prostředí** proveďte následující akce: 
    1. V části **Lab account name** (Název účtu testovacího prostředí) zadejte název. 
    2. Vyberte **předplatné Azure**, ve kterém chcete účet testovacího prostředí vytvořit.
    3. V části **Skupina prostředků** vyberte **Vytvořit novou** a zadejte název skupiny prostředků.
    4. V části **Umístění** vyberte umístění nebo oblast, ve které chcete účet testovacího prostředí vytvořit.
    5. V poli **umožnit autor testovacího prostředí vybrat umístění testovacího prostředí** určete, jestli chcete, aby tvůrci testovacího prostředí mohli vybrat umístění pro testovací prostředí. Ve výchozím nastavení je tato možnost zakázaná. Pokud je zakázaný, tvůrci testovacího prostředí nemůžou určit umístění pro testovací prostředí, které vytváří. Laboratoře se vytvoří v nejbližším zeměpisném umístění účtu testovacího prostředí. Když je tato možnost povolená, může tvůrce testovacího prostředí vybrat umístění v době vytváření testovacího prostředí. Další informace najdete v tématu [Povolení služby Lab Creator pro výběr umístění pro testovací prostředí](allow-lab-creator-pick-lab-location.md). 

        ![Vytvoření účtu testovacího prostředí – základy >](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Vyberte **Další: Upřesnit** v dolní části stránky, přejděte na kartu **Upřesnit** a proveďte následující kroky: 
    1. Vyberte existující **galerii sdílených imagí** nebo ji vytvořte. Virtuální počítač šablony můžete uložit do galerie sdílených imagí, aby ho mohli znovu použít jiní uživatelé. Podrobné informace o galeriích sdílených imagí najdete [v tématu použití Galerie sdílených imagí v Azure Lab Services](how-to-use-shared-image-gallery.md).
    2. Určete, jestli chcete, aby se **virtuální počítače s Windows automaticky vypnuly** , když se z nich uživatelé odpojí. Určete, jak dlouho by měly virtuální počítače čekat na opětovné připojení uživatele, než se automaticky vypíná. 
    3. Pro **partnerský virtuální síť**vyberte partnerský virtuální síť (VNET) pro síť laboratoře. Laboratoře vytvořené v tomto účtu jsou připojené k vybrané virtuální síti a mají přístup k prostředkům ve vybrané virtuální síti. Další informace najdete v tématu [připojení virtuální sítě testovacího prostředí k partnerské virtuální síti](how-to-connect-peer-virtual-network.md).    
    8. Zadejte **Rozsah adres** pro virtuální počítače v testovacím prostředí. Rozsah adres by měl být v notaci směrování mezi doménami (například: 10.20.0.0/23). Virtuální počítače v testovacím prostředí se vytvoří v tomto rozsahu adres. Další informace najdete v tématu [určení rozsahu adres pro virtuální počítače v testovacím prostředí](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab) .  

        ![Vytvoření účtu testovacího prostředí – > Pokročilé](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Vyberte **Další: značky** v dolní části stránky a přepněte na kartu **značky** . Přidejte všechny značky, které chcete přidružit k účtu testovacího prostředí. Značky jsou páry název-hodnota, které umožňují kategorizaci prostředků a zobrazení konsolidované fakturace, a to použitím stejné značky na více prostředků a skupin prostředků. Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../../azure-resource-manager/management/tag-resources.md).

    ![Vytvoření účtu testovacího prostředí – > značek](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. V dolní části této stránky vyberte **zkontrolovat + vytvořit** , abyste přešli na kartu **Revize + vytvořit** . 
4. Zkontrolujte souhrnné informace na této stránce a vyberte **vytvořit**. 

    ![Vytvoření účtu testovacího prostředí – > značek](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Počkejte, až se nasazení dokončí, rozbalte **Další kroky**a vyberte **Přejít k prostředku** , jak je znázorněno na následujícím obrázku: 

    Můžete také vybrat **ikonu zvonku** na panelu nástrojů (**oznámení**), potvrdit, že nasazení proběhlo úspěšně, a pak vybrat **Přejít k prostředku**. 

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

1. Na stránce **testovacího účtu** v nabídce vlevo vyberte **všechny laboratoře** .

    ![Cvičení v účtu](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. V účtu se zobrazí **seznam cvičení** s následujícími informacemi: 
    1. Název testovacího prostředí.
    2. Datum vytvoření testovacího prostředí. 
    3. E-mailová adresa uživatele, který testovací prostředí vytvořil 
    4. Maximální počet uživatelů povolený do testovacího prostředí. 
    5. Stav testovacího prostředí. 
    6. Přiřazení rolí 

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
Viz následující články:

- [Povolí autorovi testovacího prostředí vybrat umístění testovacího prostředí.](allow-lab-creator-pick-lab-location.md)
- [Připojení sítě testovacího prostředí k partnerské virtuální síti](how-to-connect-peer-virtual-network.md)
- [Připojení Galerie sdílených imagí k testovacímu prostředí](how-to-attach-detach-shared-image-gallery.md)
- [Přidat uživatele jako vlastníka testovacího prostředí](how-to-add-user-lab-owner.md)
- [Zobrazit nastavení brány firewall pro testovací prostředí](how-to-configure-firewall-settings.md)
- [Konfigurace dalších nastavení pro testovací prostředí](how-to-configure-lab-accounts.md)