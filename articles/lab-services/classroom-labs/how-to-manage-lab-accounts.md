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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: fd43c62f1a291a59d5d373437a49b263d6af4cb3
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345884"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Správa účtů testovacího prostředí ve službě Azure Lab Services 
Ve službě Azure Lab Services účet testovacího prostředí je kontejner pro spravované testovací prostředí, jako je například testovací prostředí v učebnách. Správce nastaví účet testovacího prostředí pomocí Azure Lab Services a poskytuje přístup k vlastníků testovacího prostředí, kteří můžou vytvářet testovací prostředí v rámci účtu. Tento článek popisuje, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranění účtu služby testovacího prostředí.

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V hlavní nabídce na levé straně vyberte **Vytvořit prostředek**.
3. V Azure Marketplace vyhledejte **Lab Services** a v rozevíracím seznamu vyberte **Lab Services**. 
4. Ve filtrovaném seznamu služeb vyberte **Lab Services (Preview)**. 
5. V okně **Create a lab account** (Vytvořit účet testovacího prostředí) vyberte **Create** (Vytvořit).
7. V okně **Lab account** (Účet testovacího prostředí) proveďte následující akce: 
    1. V části **Lab account name** (Název účtu testovacího prostředí) zadejte název. 
    2. Vyberte **předplatné Azure**, ve kterém chcete účet testovacího prostředí vytvořit.
    3. V části **Skupina prostředků** vyberte **Vytvořit novou** a zadejte název skupiny prostředků.
    4. V části **Umístění** vyberte umístění nebo oblast, ve které chcete účet testovacího prostředí vytvořit. 
    5. Vyberte **Vytvořit**. 

        ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Pokud se vám nezobrazuje stránka účtu testovacího prostředí, vyberte tlačítko **oznámení** a potom v oznámeních klikněte na tlačítko **Přejít k prostředku**. 

    ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Zobrazí se následující stránka **účtu testovacího prostředí**:

    ![Stránka účtu testovacího prostředí](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Přidání uživatele do role Autor testovacího prostředí
Pokud chcete v účtu testovacího prostředí nastavit testovací prostředí v učebně, musí být uživatel v účtu testovacího prostředí členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Pokud máte v úmyslu použít k vytvoření testovacího prostředí v učebně stejný uživatelský účet, můžete tento krok přeskočit. Pokud chcete k vytvoření testovacího prostředí v učebně použít jiný uživatelský účet, postupujte takto: 

1. Na stránce **účtu testovacího prostředí** vyberte **Access control (IAM)** (Řízení přístupu (IAM)) a na panelu nástrojů klikněte na tlačítko **+ Add** (+ Přidat). 

    ![Stránka účtu testovacího prostředí](../media/tutorial-setup-lab-account/access-control.png)
2. Na stránce **Add permissions** (Přidat oprávnění) vyberte **Lab Creator** (Autor testovacího prostředí) v části **Role**, vyberte uživatele, kterého chcete přidat do role Autoři testovacího prostředí, a vyberte **Save** (Uložit). 

    ![Přidání uživatele do role Autor testovacího prostředí](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Určení imagí v Marketplace dostupných pro vlastníky testovacích prostředí
Jako vlastník účtu testovacího prostředí můžete zadat Image Marketplace, které tvůrci testovacího prostředí můžete použít k vytvoření testovacích prostředí v účtu testovacího prostředí. 

1. V nabídce vlevo vyberte **Marketplace images** (Image z Marketplace). Ve výchozím nastavení se zobrazí úplný seznam imagí (povolených i zakázaných). Pomocí možnosti **Enabled only**/**Disabled only** (Jenom povolené / Jenom zakázané) v rozevíracím seznamu v horní části můžete seznam filtrovat, aby se v něm zobrazovaly jen povolené/zakázané image. 
    
    ![Stránka imagí v Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Image Marketplace, které jsou zobrazeny v seznamu jsou pouze ty, které splňují následující podmínky:
        
    - Vytvoří jeden virtuální počítač.
    - Zřizování virtuálních počítačů pomocí Azure Resource Manageru
    - Nevyžaduje, aby zakoupení dalších licenčního plánu
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
3. Vyberte **Lab Services** pro **typ**. 
    Můžete také filtrovat podle předplatného, skupinu prostředků, míst a značky. 

## <a name="delete-a-lab-account"></a>Odstranění účtu služby testovacího prostředí
Postupujte podle pokynů v předchozí části, která se zobrazí v seznamu účtů testovacího prostředí. Odstranění účtu služby testovacího prostředí použijte následující pokyny: 

1. Vyberte **účet testovacího prostředí** , kterou chcete odstranit. 
2. Vyberte **odstranit** z panelu nástrojů. 
3. Typ **Ano** pro potvrzení.
4. Vyberte **Odstranit**. 

## <a name="next-steps"></a>Další postup
Začínáme s nastavením testovacího prostředí pomocí Azure Lab Services:

- [Nastavení testovacího prostředí v učebně](tutorial-setup-classroom-lab.md)
- [Nastavení testovacího prostředí](../tutorial-create-custom-lab.md)
