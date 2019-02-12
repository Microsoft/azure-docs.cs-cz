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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 96d5e94cb60888f7e098e31d7f06481a766cabd5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998514"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Kurz: Nastavení testovacího prostředí v učebně 
V tomto kurzu nastavíte testovací prostředí v učebně pomocí virtuálních počítačů, které používají studenti v učebně.  

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí v učebně
> * Přidání uživatelů do testovacího prostředí
> * Odeslání odkazu pro registraci studentům

## <a name="prerequisites"></a>Požadavky
Nastavení testovacího prostředí v učebně v účtu testovacího prostředí, musíte mít členem jedné z těchto rolí v rámci účtu testovacího prostředí: Vlastník, Autor testovacího prostředí nebo Přispěvatel. Účet, který jste použili k vytvoření účtu testovacího prostředí je automaticky přidán do role vlastníka.

Vlastník testovacího prostředí můžete přidat ostatním uživatelům **Autor testovacího prostředí** role. Vlastník testovacího prostředí, například přidá vysokoškolští do role Tvůrce prostředí. Potom vysokoškolští vytvářet testovací prostředí s virtuálními počítači pro své třídy. Studenti použijte odkaz na registraci, který obdrží z vysokoškolští k zaregistrování do testovacího prostředí. Jakmile jsou registrované, můžou použít virtuální počítače v Tato praktická cvičení udělat třídu práce a práce z domova. Podrobné pokyny pro přidání uživatele do role Tvůrce prostředí najdete v tématu [přidání uživatele do role Tvůrce prostředí](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Zadejte maximální **počet virtuálních počítačů** v testovacím prostředí. Můžete zvýšit nebo decreate počet virtuálních počítačů po vytvoření testovacího prostředí nebo v existující testovací prostředí. Další informace najdete v tématu [aktualizovat počet virtuálních počítačů v testovacím prostředí](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Vyberte **Uložit**.

        ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stránce **Select virtual machine specifications** (Výběr specifikací virtuálních počítačů) proveďte následující kroky:
    1. Vyberte **velikost** virtuálních počítačů vytvořených v testovacím prostředí. 
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
8. Na **konfigurovat šablony** stránce, proveďte následující kroky: Tyto kroky jsou **volitelné** pro tento kurz.
    1. Vyberte **Start** (Spustit) a spusťte virtuální počítač šablony.
    2. Vyberte **Connect** (Připojit) a připojte se k virtuálnímu počítači šablony. 
    3. Nainstalujte a nakonfigurujte na virtuálním počítači šablony požadovaný software. 
    4. **Zastavte** virtuální počítač.  
    5. Zadejte **popis** šablony.
9. Na stránce šablony vyberte **Next** (Další). 
10. Na stránce **Publish the template** (Publikování šablony) proveďte následující akce. 
    1. Chcete-li publikovat šablony okamžitě a vyberte **publikovat**.  

        > [!WARNING]
        > Publikování nejde vrátit zpět. 
    2. Pokud chcete publikování provést později, vyberte **Save for later** (Uložit na později). Virtuální počítač šablony můžete publikovat i po dokončení průvodce. Podrobnosti o postupu konfigurace a publikování po dokončení průvodce najdete v části [Publikování šablony](how-to-create-manage-template.md#publish-the-template-vm) v článku [Správa testovacích prostředí v učebnách](how-to-manage-classroom-labs.md).

        ![Publikování šablony](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Zobrazí se **průběh publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po úspěšném publikování šablony se zobrazí následující stránka. Vyberte **Done** (Hotovo).

    ![Publikování šablony – úspěch](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zobrazí se **řídicí panel** testovacího prostředí. 
    
    ![Řídicí panel testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Přepněte **virtuálních počítačů** stránky tak, že vyberete virtuální počítače v nabídce vlevo nebo tak, že vyberete dlaždici virtuálních počítačů. Zkontrolujte, jestli se virtuální počítače, které jsou v **Nepřiřazeno** stavu. Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Přidání uživatelů do testovacího prostředí

1. Vyberte **uživatelé** v nabídce vlevo. Ve výchozím nastavení **omezit přístup** je povolená možnost. Pokud toto nastavení je zapnuto, uživatel nemůže zaregistrovat s testovacím prostředí i v případě, že uživatel má odkaz na registraci, pokud je uživatel v seznamu uživatelů. Jenom uživatelé v seznamu lze zaregistrovat pomocí odkazu registrace, které odesíláte testovacího prostředí. V tomto postupu přidáte uživatele do seznamu. Alternativně můžete vypnout **omezit přístup**, které uživatelům umožňuje se zaregistrovat testovacího prostředí, tak dlouho, dokud mají odkaz na registraci. 
2. Vyberte **přidat uživatele** na panelu nástrojů. 

    ![Přidání uživatelů tlačítka](../media/how-to-configure-student-usage/add-users-button.png)
1. Na **přidat uživatele** stránky, zadejte e-mailové adresy uživatele na samostatných řádcích nebo na jednom řádku, oddělené středníky. 

    ![Přidat e-mailové adresy uživatele](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Vyberte **Uložit**. Zobrazí se e-mailové adresy uživatelů a jejich stavy (zaregistrované, nebo ne) v seznamu. 

    ![Seznam uživatelů](../media/how-to-configure-student-usage/users-list-new.png)


## <a name="send-registration-link-to-students"></a>Odeslání odkazu pro registraci studentům

1. Přepněte **uživatelé** zobrazit v případě, že již není na stránce. 
2. Vyberte **odkazu registrace** na panelu nástrojů.
1. V dialogovém okně **User registration** (Registrace uživatelů) vyberte tlačítko **Copy** (Kopírovat). Odkaz se zkopíruje do schránky.

    ![Odkaz pro registraci](../media/tutorial-setup-classroom-lab/registration-link.png)
1. V dialogovém okně **User registration** (Registrace uživatelů) vyberte **Close** (Zavřít). 
2. Sdílejte odkaz pro registraci se studentem, aby se mohl zaregistrovat do třídy.

## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili testovací prostředí v učebně a nakonfigurovali ho. Pokud chcete získat informace o tom, jak může student přistupovat k virtuálnímu počítači v testovacím prostředí pomocí odkazu pro registraci, přejděte na další kurz:

> [!div class="nextstepaction"]
> [Připojení k virtuálnímu počítači v testovacím prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)

