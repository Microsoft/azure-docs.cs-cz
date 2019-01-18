---
title: Správa testovacích prostředí v učebnách ve službě Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a nakonfigurovat prostředí v učebně, zobrazit všichni testovací prostředí v učebnách, sdílet registraci propojit s uživatelem testovacího prostředí nebo odstranění testovacího prostředí.
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
ms.date: 01/16/2019
ms.author: spelluru
ms.openlocfilehash: 5c1207b1b21e2d2ee229f5bea068b99f3b3218b1
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389124"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Správa testovacích prostředí v učebnách ve službě Azure Lab Services 
Tento článek popisuje, jak vytvářet a odstraňovat prostředí v učebně. Je také ukazuje, jak zobrazit všechny testovací prostředí v učebnách účtu testovacího prostředí. 

## <a name="prerequisites"></a>Požadavky
Pokud chcete nastavit testovací prostředí v učebně v účtu testovacího prostředí, musíte v účtu testovacího prostředí být členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Vlastník testovacího prostředí můžete přidat další uživatele do role Tvůrce prostředí pomocí kroků v následujícím článku: [Přidání uživatele do role Tvůrce prostředí](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). 
2. Vyberte **přihlášení**. Vyberte nebo zadejte **ID uživatele** , který je členem skupiny **Autor testovacího prostředí** roli v testovacím prostředí účet a zadat heslo. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Zadejte **počet uživatelů, kteří** na testovacím prostředí povolená. 
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
6. Na stránce **Configure template** (Konfigurace šablony) se zobrazí stav vytváření testovacího prostředí. Vytvoření šablony v testovacím prostředí může trvat až 20 minut. Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurovaný přesně podle toho, co chcete uživatelům testovacího prostředí poskytnout.  

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
    1. Pokud chcete šablonu publikovat okamžitě, zaškrtněte políčko *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Chápu, že publikovanou šablonu není možné upravit. Tento postup je možné provést pouze jednou a může trvat až hodinu.) a vyberte **Publish** (Publikovat).  Publikujte šablonu, aby instance šablony virtuálního počítače byly dostupné všem uživatelům testovacího prostředí.

        > [!WARNING]
        > Publikování nejde vrátit zpět. 
    2. Pokud chcete publikování provést později, vyberte **Save for later** (Uložit na později). Virtuální počítač šablony můžete publikovat i po dokončení průvodce. Podrobnosti o tom, jak nakonfigurovat a publikování po dokončení průvodce, přečtěte si další informace o tom, jak konfigurace a publikování po dokončení průvodce najdete v tématu [publikovat šablony](#publish-the-template) tématu [Správa testovacích prostředí v učebnách ](how-to-manage-classroom-labs.md) článku.

        ![Publikování šablony](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Zobrazí se **průběh publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po úspěšném publikování šablony se zobrazí následující stránka. Vyberte **Done** (Hotovo).

    ![Publikování šablony – úspěch](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zobrazí se **řídicí panel** testovacího prostředí. 
    
    ![Řídicí panel testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Přepněte na stránku **Virtual machines** (Virtuální počítače) a zkontrolujte, že se zobrazí virtuální počítače ve stavu **Unassigned** (Nepřiřazeno). Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="view-all-classroom-labs"></a>Zobrazit všechny testovací prostředí v učebnách
1. Přejděte do [portálu Azure Lab Services](https://labs.azure.com).
2. Vyberte **přihlášení**. Vyberte nebo zadejte **ID uživatele** , který je členem skupiny **Autor testovacího prostředí** roli v testovacím prostředí účet a zadat heslo. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. Ověřte, že testovací prostředí v rámci účtu vybraného testovacího prostředí. 

    ![Všechny testovací prostředí](../media/how-to-manage-classroom-labs/all-labs.png)
3. Pomocí rozevíracího seznamu v horní části vyberte účet jiného testovacího prostředí. Zobrazí testovací prostředí v rámci účtu vybraného testovacího prostředí. 

## <a name="delete-a-classroom-lab"></a>Odstranění testovacího prostředí v učebně
1. Na dlaždici pro testovací prostředí vyberte tři tečky (...) v pravém rohu. 

    ![Výběr testovacího prostředí](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Vyberte **Odstranit**. 

    ![Tlačítko Odstranit](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na **Delete lab** dialogu **odstranit**. 

    ![Dialogové okno Odstranit](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako vlastník testovacího prostředí nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurovat a spravovat využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)

