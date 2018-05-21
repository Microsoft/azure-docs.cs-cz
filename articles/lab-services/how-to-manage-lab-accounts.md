---
title: Správa účtů testovacího prostředí v prostředí služby Azure | Microsoft Docs
description: Zjistěte, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí na předplatné Azure.
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
ms.openlocfilehash: 1286698fb7dd13c7568a0fa8b20c50511d5a6919
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>Správa účtů testovacího prostředí v prostředí služby Azure (dříve Azure DevTest Labs)
Tento článek popisuje, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí.

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V hlavní nabídce na levé straně vyberte **Vytvořit prostředek**.
3. V Azure Marketplace vyhledejte **Lab Services** a v rozevíracím seznamu vyberte **Lab Services**. 
4. Vyberte **Sercices laboratoře (Preview)** v seznamu flitered služeb. 
5. V okně **Create a lab account** (Vytvořit účet testovacího prostředí) vyberte **Create** (Vytvořit).
7. V okně **Lab account** (Účet testovacího prostředí) proveďte následující akce: 
    1. V části **Lab account name** (Název účtu testovacího prostředí) zadejte název. 
    2. Vyberte **předplatné Azure**, ve kterém chcete účet testovacího prostředí vytvořit.
    3. V části **Skupina prostředků** vyberte **Vytvořit novou** a zadejte název skupiny prostředků.
    4. V části **Umístění** vyberte umístění nebo oblast, ve které chcete účet testovacího prostředí vytvořit. 
    5. Vyberte **Vytvořit**. 

        ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Pokud se vám nezobrazuje stránka účtu testovacího prostředí, vyberte tlačítko **oznámení** a potom v oznámeních klikněte na tlačítko **Přejít k prostředku**. 

    ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Zobrazí se následující stránka **účtu testovacího prostředí**:

    ![Stránka účtu testovacího prostředí](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Přidání uživatele do role Autor testovacího prostředí
Abyste mohli udělit vyučujícím oprávnění k vytvoření testovacích prostředí pro jejich třídy, přidejte je do role Autor testovacího prostředí:

1. Na stránce **účtu testovacího prostředí** vyberte **Access control (IAM)** (Řízení přístupu (IAM)) a na panelu nástrojů klikněte na tlačítko **+ Add** (+ Přidat). 

    ![Stránka účtu testovacího prostředí](./media/tutorial-setup-lab-account/access-control.png)
2. Na stránce **Add permissions** (Přidat oprávnění) vyberte **Lab Creator** (Autor testovacího prostředí) v části **Role**, vyberte uživatele, kterého chcete přidat do role Autoři testovacího prostředí, a vyberte **Save** (Uložit). 

    ![Přidání uživatele do role Autor testovacího prostředí](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>Zobrazit účty testovacího prostředí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny prostředky** z nabídky. 
3. Vyberte **testovacího prostředí služby** pro **typu**. 
    Můžete také filtrovat podle předplatné, skupinu prostředků, umístění a značky. 

## <a name="delete-a-lab-account"></a>Odstranění účtu testovacího prostředí
Postupujte podle pokynů z předchozí části, které se zobrazí v seznamu účtů testovacího prostředí. Použijte následující pokyny k odstranění účtu testovacího prostředí: 

1. Vyberte **testovacím účet** , kterou chcete odstranit. 
2. Vyberte **odstranit** na panelu nástrojů. 
3. Typ **Ano** pro potvrzení.
4. Vyberte **Odstranit**. 

## <a name="next-steps"></a>Další postup
Začínáme s nastavením testovacího prostředí pomocí Azure Lab Services:

- [Nastavení testovacího prostředí v učebně](tutorial-setup-classroom-lab.md)
- [Nastavení vlastního testovacího prostředí](tutorial-create-custom-lab.md)
