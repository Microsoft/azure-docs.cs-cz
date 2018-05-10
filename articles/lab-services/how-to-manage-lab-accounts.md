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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 53494abead5701052f6e08f68b01ccdf1bfa211c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>Správa účtů testovacího prostředí v prostředí služby Azure (dříve Azure DevTest Labs)
Tento článek popisuje, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí.

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Z hlavní nabídky na levé straně vyberte **vytvořit prostředek**.
3. Vyhledejte **testovacího prostředí služby** v Azure Marketplace a vyberte **testovacího prostředí služby** v rozevíracím seznamu. 
4. Vyberte **Sercices laboratoře (Preview)** v seznamu flitered služeb. 
5. V **vytvořit účet testovacím** vyberte **vytvořit**.
7. V **testovacím účet** okno, proveďte následující akce: 
    1. Pro **název účtu testovacího prostředí**, zadejte název. 
    2. Vyberte **předplatného Azure** ve kterém chcete vytvořit účet testovacího prostředí.
    3. Pro **skupiny prostředků**, vyberte **vytvořit nový**a zadejte název pro skupinu prostředků.
    4. Pro **umístění**, vyberte umístění nebo oblast, ve kterém chcete účet testovacího prostředí, který chcete vytvořit. 
    5. Vyberte **Vytvořit**. 

        ![Vytvoření okna účet testovacího prostředí](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Pokud se nezobrazí na stránce pro účet testovacího prostředí, vyberte **oznámení** tlačítko a potom klikněte na **přejděte k prostředku** tlačítka na oznámení. 

    ![Vytvoření okna účet testovacího prostředí](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Najdete zde **testovacím účet** stránky:

    ![Stránka účtu testovacího prostředí](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Přidat uživatele k roli Creator testovacího prostředí
K poskytování lektorům oprávnění k vytvoření prostředí pro jejich třídy, přidejte je do role autora testovacího prostředí:

1. Na **testovacím účet** vyberte **přístup k ovládacímu prvku (IAM)** a klikněte na tlačítko **+ přidat** na panelu nástrojů. 

    ![Stránka účtu testovacího prostředí](./media/tutorial-setup-lab-account/access-control.png)
2. Na **přidat oprávnění** vyberte **testovacím Creator** pro **Role**, vyberte uživatele, kterého chcete přidat do role Autoři testovacího prostředí a vyberte **Uložit**. 

    ![Přidat uživatele k roli Creator testovacího prostředí](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


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
Začínáme s nastavení laboratoře pomocí služby Azure testovacího prostředí:

- [Nastavit Učebna testovacího prostředí](tutorial-setup-classroom-lab.md)
- [Nastavit vlastní testovací prostředí](tutorial-create-custom-lab.md)
