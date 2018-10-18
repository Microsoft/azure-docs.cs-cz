---
title: Nastavení testovacího prostředí v učebně v Azure Lab Services | Microsoft Docs
description: V tomto kurzu nastavíte testovací prostředí pro použití v učebně.
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
ms.date: 10/05/2018
ms.author: spelluru
ms.openlocfilehash: 6696d6e7e53e98dfab2a65c7c66825936020f33c
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856629"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Kurz: Nastavení testovacího prostředí v učebně 
V tomto kurzu nastavíte testovací prostředí v učebně pomocí virtuálních počítačů, které používají studenti v učebně.  

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí v učebně
> * Konfigurace testovacího prostředí v učebně
> * Odeslání odkazu pro registraci studentům

## <a name="prerequisites"></a>Požadavky
Pokud chcete nastavit testovací prostředí v učebně v účtu testovacího prostředí, musíte v účtu testovacího prostředí být členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Vlastník testovacího prostředí může přidat další uživatele do role Autor testovacího prostředí podle postupu v následujícím článku: [Přidání uživatele do role Autor testovacího prostředí](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Zdejte maximální **počet uživatelů** s povoleným přístupem k testovacímu prostředí. 
    6. Vyberte **Uložit**.

        ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stránce **Select virtual machine specifications** (Výběr specifikací virtuálních počítačů) proveďte následující kroky:
    1. Vyberte **velikost** virtuálních počítačů vytvořených v testovacím prostředí. 
    2. Vyberte **oblast**, ve které se mají virtuální počítače vytvořit. 
    3. Vyberte **image virtuálního počítače**, která se má použít k vytvoření virtuálních počítačů v testovacím prostředí. 
    4. Vyberte **Další**.

        ![Zadání specifikací virtuálních počítačů](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na stránce **Set credentials** (Nastavení přihlašovacích údajů) zadejte výchozí přihlašovací údaje ke všem virtuálním počítačům v testovacím prostředí. 
    1. Zadejte **jméno uživatele** pro všechny virtuální počítače v testovacím prostředí.
    2. Zadejte **heslo** tohoto uživatele. 

        > [!IMPORTANT]
        > Uživatelské jméno a heslo si poznamenejte. Znovu se už nezobrazí.
    3. Vyberte **Vytvořit**. 

        ![Nastavení přihlašovacích údajů](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na stránce **Configure template** (Konfigurace šablony) se zobrazí stav vytváření testovacího prostředí. Vytvoření šablony v testovacím prostředí může trvat až 20 minut. 

    ![Konfigurace šablony](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Po dokončení konfigurace šablony se zobrazí následující stránka: 

    ![Stránka Configure template (Konfigurace šablony) po dokončení konfigurace](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Následující kroky jsou pro tento kurz volitelné: 
    1. Vyberte **Start** (Spustit) a spusťte virtuální počítač šablony.
    2. Vyberte **Connect** (Připojit) a připojte se k virtuálnímu počítači šablony. 
    3. Nainstalujte a nakonfigurujte na virtuálním počítači šablony požadovaný software. 
    4. **Zastavte** virtuální počítač.  
    5. Zadejte **popis** šablony.

        ![Tlačítko Next (Další) na stránce Configure template (Konfigurace šablony)](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Na stránce šablony vyberte **Next** (Další). 
10. Na stránce **Publish the template** (Publikování šablony) proveďte následující akce. 
    1. Pokud chcete šablonu publikovat okamžitě, zaškrtněte políčko *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Chápu, že publikovanou šablonu není možné upravit. Tento postup je možné provést pouze jednou a může trvat až hodinu.) a vyberte **Publish** (Publikovat).  

        > [!WARNING]
        > Publikování nejde vrátit zpět. 
    2. Pokud chcete publikování provést později, vyberte **Save for later** (Uložit na později). Virtuální počítač šablony můžete publikovat i po dokončení průvodce. Podrobnosti o postupu konfigurace a publikování po dokončení průvodce najdete v části [Publikování šablony](how-to-manage-classroom-labs.md#publish-the-template) v článku [Správa testovacích prostředí v učebnách](how-to-manage-classroom-labs.md).

        ![Publikování šablony](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Zobrazí se **průběh publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po úspěšném publikování šablony se zobrazí následující stránka. Vyberte **Done** (Hotovo).

    ![Publikování šablony – úspěch](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zobrazí se **řídicí panel** testovacího prostředí. 
    
    ![Řídicí panel testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Přepněte na stránku **Virtual machines** (Virtuální počítače) a zkontrolujte, že se zobrazí virtuální počítače ve stavu **Unassigned** (Nepřiřazeno). Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>Odeslání odkazu pro registraci studentům

1. Přepněte do zobrazení **Dashboard** (Řídicí panel) tím, že v nabídce vlevo vyberete **Dashboard** (Řídicí panel). 
2. Vyberte dlaždici **User registration** (Registrace uživatelů).

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. V dialogovém okně **User registration** (Registrace uživatelů) vyberte tlačítko **Copy** (Kopírovat). Odkaz se zkopíruje do schránky. Vložte ho do editoru e-mailů a e-mail odešlete studentovi. 

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/registration-link.png)
2. V dialogovém okně **User registration** (Registrace uživatelů) vyberte **Close** (Zavřít). 
3. Sdílejte odkaz pro registraci se studentem, aby se mohl zaregistrovat do třídy. 


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili testovací prostředí v učebně a nakonfigurovali ho. Pokud chcete získat informace o tom, jak může student přistupovat k virtuálnímu počítači v testovacím prostředí pomocí odkazu pro registraci, přejděte na další kurz:

> [!div class="nextstepaction"]
> [Připojení k virtuálnímu počítači v testovacím prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)

