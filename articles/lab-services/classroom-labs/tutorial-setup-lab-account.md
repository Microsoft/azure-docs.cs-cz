---
title: Nastavení účtu testovacího prostředí s Azure Lab Services | Microsoft Docs
description: V tomto kurzu nastavíte účet testovacího prostředí s Azure Lab Services.
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
ms.date: 04/24/2019
ms.author: spelluru
ms.openlocfilehash: 8c252870a82a60a561f12fab9d728c028458212a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562098"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Kurz: Nastavení účtu testovacího prostředí s Azure Lab Services
Účet testovacího prostředí v Azure Lab Services slouží jako centrální účet, ve kterém se spravují testovací prostředí vaší organizace. V účtu testovacího prostředí můžete udělit oprávnění vytvářet testovací prostředí dalším uživatelům a nastavit zásady, které budou platit pro všechna testovací prostředí pod účtem testovacího prostředí. V tomto kurzu zjistíte, jak vytvořit účet testovacího prostředí jako správce testovacího prostředí. 

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření účtu testovacího prostředí
> * Přidání uživatele do role Autor testovacího prostředí
> * Určení imagí v Marketplace dostupných pro vlastníky testovacích prostředí

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
Následující kroky ukazují postup vytvoření účtu testovacího prostředí se službou Azure Lab Services pomocí webu Azure Portal. 

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby** . V části **DEVOPS** vyberte **testovací služby** . Pokud vyberete hvězdičku (`*`) vedle položky **testovací služby**, přidá se do části **Oblíbené** v nabídce vlevo. Od dalšího okamžiku vyberte v části **Oblíbené položky** **testovací služby** .

    ![Všechny služby – > Laboratorní služby](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stránce **služby testovacího prostředí** vyberte **Přidat** na panelu nástrojů. 

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

## <a name="add-a-user-to-the-lab-creator-role"></a>Přidání uživatele do role Autor testovacího prostředí
Pokud chcete v účtu testovacího prostředí nastavit testovací prostředí v učebně, musí být uživatel v účtu testovacího prostředí členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Pokud máte v úmyslu použít k vytvoření testovacího prostředí v učebně stejný uživatelský účet, můžete tento krok přeskočit. Pokud chcete k vytvoření testovacího prostředí v učebně použít jiný uživatelský účet, postupujte takto: 

Přidejte vyučující do role **Autor testovacího prostředí**, abyste jim poskytli oprávnění k vytvoření testovacích prostředí pro jejich třídy:

1. Na stránce **účet testovacího prostředí** vyberte **řízení přístupu (IAM)** , vyberte **+ Přidat** na panelu nástrojů a pak na panelu nástrojů vyberte **+ Přidat přiřazení role** . 

    ![Access Control – tlačítko Přidat přiřazení role >](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stránce **Přidat přiřazení role** vyberte možnost **Autor testovacího prostředí** pro **role**, vyberte uživatele, kterého chcete přidat do role testovací tvůrci, a vyberte **Uložit**. 

    ![Přidat autora testovacího prostředí](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Určení imagí Marketplace dostupných pro tvůrci testovacího prostředí
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

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili účet testovacího prostředí. Další informace o tom, jak vytvořit prostředí učebny jako profesor, najdete v dalším kurzu:

> [!div class="nextstepaction"]
> [Nastavení testovacího prostředí v učebně](tutorial-setup-classroom-lab.md)

