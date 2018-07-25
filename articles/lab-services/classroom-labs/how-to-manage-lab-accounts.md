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
ms.openlocfilehash: ff2968f8e2fa9a705817b020f2daa6582d78029c
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225298"
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
Nastavení testovacího prostředí v učebně v účtu testovacího prostředí, musí být uživatel členem skupiny **Autor testovacího prostředí** role v účtu testovacího prostředí. Účet, který jste použili k vytvoření účtu testovacího prostředí se automaticky přidá do této role. Pokud máte v úmyslu použít stejný uživatelský účet k vytvoření testovacího prostředí v učebně, můžete tento krok přeskočit. Pokud chcete použít jiný uživatelský účet k vytvoření testovacího prostředí v učebně, proveďte následující kroky: 

1. Na stránce **účtu testovacího prostředí** vyberte **Access control (IAM)** (Řízení přístupu (IAM)) a na panelu nástrojů klikněte na tlačítko **+ Add** (+ Přidat). 

    ![Stránka účtu testovacího prostředí](../media/tutorial-setup-lab-account/access-control.png)
2. Na stránce **Add permissions** (Přidat oprávnění) vyberte **Lab Creator** (Autor testovacího prostředí) v části **Role**, vyberte uživatele, kterého chcete přidat do role Autoři testovacího prostředí, a vyberte **Save** (Uložit). 

    ![Přidání uživatele do role Autor testovacího prostředí](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Zadejte Image z Marketplace k dispozici pro vlastníků testovacího prostředí
V této části zadáte Image Marketplace, které vlastníků testovacího prostředí můžete použít k vytvoření testovacích prostředí v učebnách. 

1. Vyberte **Image z Marketplace** v nabídce na levé straně. Ve výchozím nastavení najdete v článku na seznam všech imagí (povolené i zakázané). Můžete filtrovat seznam a zobrazit pouze povoleno/zakázáno Image tak, že vyberete **povolí jenom**/**pouze** možnost z rozevíracího seznamu v horní části. 

    ![Stránka imagí Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)
2. K **zakázat** image Marketplace, který je povolen, proveďte jednu z následujících akcí: 
    1. Vyberte **... (tři tečky)**  v posledním sloupci a vyberte **zakázat image**. 

        ![Zakažte jednu image](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Vyberte jednu nebo víc imagí ze seznamu vyberte zaškrtávací políčka před názvy těchto imagí v seznamu a vyberte **zakázat vybrané bitové kopie**. 

        ![Zakázat více bitových kopií](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Podobně, **povolit** image z Marketplace, proveďte jednu z následujících akcí: 
    1. Vyberte **... (tři tečky)**  v posledním sloupci a vyberte **bitové kopie povoleným**. 
    2. Vyberte jednu nebo víc imagí ze seznamu vyberte zaškrtávací políčka před názvy těchto imagí v seznamu a vyberte **povolit vybrané bitové kopie**. 

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
