---
title: Nastavení účtu testovacího prostředí s Azure Lab Services | Microsoft Docs
description: V tomto kurzu nastavíte účet testovacího prostředí s Azure Lab Services (dříve DevTest Labs).
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
ms.date: 04/26/2018
ms.author: spelluru
ms.openlocfilehash: 5af348bfdccb9392948af962cf582e33ebd40872
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services-formerly-azure-devtest-labs"></a>Kurz: Nastavení účtu testovacího prostředí s Azure Lab Services (dříve Azure DevTest Labs)
V tomto kurzu budete vystupovat jako správce testovacího prostředí a vytvoříte účet testovacího prostředí se službou Azure Lab Services. Potom udělíte vyučujícím oprávnění, aby mohli vytvořit testovací prostředí pro své třídy v tomto účtu testovacího prostředí. Vyučující může testovací prostředí pro třídu nastavit pomocí [webu Azure Lab Services](https://labs.azure.com).   

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření účtu testovacího prostředí
> * Přidání uživatele do role Autor testovacího prostředí

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Služba Azure Lab Services je momentálně ve verzi chráněného Preview. Pokud chcete vytvořit účet testovacího prostředí, [zaregistrujte si verzi Preview](https://aka.ms/azlabspreviewsignup).

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
Následující kroky ukazují postup vytvoření účtu testovacího prostředí se službou Azure Lab Services pomocí webu Azure Portal. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Z hlavní nabídky na levé straně vyberte **Vytvořit prostředek** (v horní části seznamu), přejděte na **Vývojářské nástroje** a klikněte na **Lab Services (Preview)**.
1. V okně **Create a lab account** (Vytvořit účet testovacího prostředí) vyberte **Create** (Vytvořit).
2. V okně **Lab account** (Účet testovacího prostředí) proveďte následující akce: 
    1. V části **Lab account name** (Název účtu testovacího prostředí) zadejte název. 
    2. Vyberte **předplatné Azure**, ve kterém chcete účet testovacího prostředí vytvořit.
    3. V části **Skupina prostředků** vyberte **Vytvořit novou** a zadejte název skupiny prostředků.
    4. V části **Umístění** vyberte umístění nebo oblast, ve které chcete účet testovacího prostředí vytvořit. 
    5. Vyberte **Vytvořit**. 

        ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](./media/tutorial-setup-lab-account/lab-account-settings.png)
5. Pokud se vám nezobrazuje stránka účtu testovacího prostředí, vyberte tlačítko **oznámení** a potom v oznámeních klikněte na tlačítko **Přejít k prostředku**. 

    ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](./media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. Zobrazí se následující stránka **účtu testovacího prostředí**:

    ![Stránka účtu testovacího prostředí](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Přidání uživatele do role Autor testovacího prostředí
Abyste mohli udělit vyučujícím oprávnění k vytvoření testovacích prostředí pro jejich třídy, přidejte je do role Autor testovacího prostředí:

1. Na stránce **účtu testovacího prostředí** vyberte **Access control (IAM)** (Řízení přístupu (IAM)) a na panelu nástrojů klikněte na tlačítko **+ Add** (+ Přidat). 

    ![Stránka účtu testovacího prostředí](./media/tutorial-setup-lab-account/access-control.png)
2. Na stránce **Add permissions** (Přidat oprávnění) vyberte **Lab Creator** (Autor testovacího prostředí) v části **Role**, vyberte uživatele, kterého chcete přidat do role Autoři testovacího prostředí, a vyberte **Save** (Uložit). 

    ![Přidání uživatele do role Autor testovacího prostředí](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili účet testovacího prostředí. Pokud chcete získat informace o tom, jak vytvořit testovací prostředí v učebně jako profesionál, přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Nastavení testovacího prostředí v učebně](tutorial-setup-classroom-lab.md)

