---
title: Spravovat šablonu testovacího prostředí v učebně ve službě Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet a spravovat šablony classroom testovacího prostředí ve službě Azure Lab Services.
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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: d0942d3465811687937ce113e664fbf3f91277d0
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815240"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Vytvářet a spravovat šablony classroom ve službě Azure Lab Services
Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurovaný přesně podle toho, co chcete uživatelům testovacího prostředí poskytnout. Můžete zadat název a popis šablony, které uvidí uživatelé testovacího prostředí. Potom můžete publikovat šablony, která má instance šablony virtuálního počítače zpřístupnit uživatelům testovacího prostředí. Jakmile publikujete šablonu, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona.

Tento článek popisuje, jak vytvářet a spravovat šablony virtuálního počítače v prostředí v učebně Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publikování šablony při vytváření testovacího prostředí v učebně
Nejprve můžete nastavit a publikovat šablony při vytváření testovacího prostředí v učebně.

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
    2. Pokud chcete publikování provést později, vyberte **Save for later** (Uložit na později). Virtuální počítač šablony můžete publikovat i po dokončení průvodce. Podrobnosti o tom, jak konfigurace a publikování po dokončení průvodce, naleznete v části Podrobnosti o tom, jak konfigurace a publikování po dokončení průvodce, najdete v části publikování v oddílu šablony [Správa testovacích prostředí v učebnách](how-to-manage-classroom-labs.md) článku.

        ![Publikování šablony](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Zobrazí se **průběh publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po úspěšném publikování šablony se zobrazí následující stránka. Vyberte **Done** (Hotovo).

    ![Publikování šablony – úspěch](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zobrazí se **řídicí panel** testovacího prostředí. 
    
    ![Řídicí panel testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

 
## <a name="set-or-update-template-title-and-description"></a>Nastavení nebo aktualizovat název šablony a popis
Pomocí následujícího postupu k nastavení názvu a popisu poprvé a později aktualizovat. 

1. V **šablony** části, najeďte myší **název** šablony nebo **popis** šablony a vyberte ji. 
2. Zadejte **nový název** nebo **nový popis** šablony a stiskněte klávesu **ENTER**.

    ![Templae název a popis](../media/how-to-create-manage-template/template-name-description.png)

## <a name="set-up-or-update-a-template-vm"></a>Nastavení nebo aktualizaci šablony virtuálního počítače
 Než šablonu virtuálního počítače zpřístupníte studentům, připojíte se k ní a nainstalujete na ní požadovaný software. Následujícím postupem nastavit šablonu virtuálního počítače poprvé nebo aktualizovat virtuální počítač. 

1. Počkejte, až bude šablona virtuálního počítače připravená. Jakmile bude připravená, mělo by se aktivovat tlačítko **Start** (Spustit). Pokud chcete virtuální počítač spustit, vyberte **Start** (Spustit).

    ![Spuštění šablony virtuálního počítače](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Přečtěte si upozornění a vyberte **Start**. 

    ![Spustit šablonu – upozornění](../media/how-to-create-manage-template/start-template-warning.png)
1. Po spuštění pro připojení k virtuálnímu počítači, vybrat **připojit**a postupujte podle pokynů. 
1. Nainstalujte software, který studenti v testovacím prostředí potřebují (například sadu Visual Studio, Průzkumníka služby Azure Storage atd.). 
2. Odpojte se od šablony virtuálního počítače (ukončete relaci vzdálené plochy). 
3. **Zastavte** šablonu virtuálního počítače výběrem **Stop** (Zastavit). 

## <a name="publish-the-template-vm"></a>Publikování šablony virtuálního počítače  
Pokud při vytváření testovacího prostředí není publikovat šablony, můžete ho publikovat později. Před publikováním, můžete se připojte k šabloně virtuálního počítače a aktualizujte s jakýmkoli softwarem. Jakmile publikujete šablonu, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona. 

1. V části **Template** (Šablona) vyberte **Publish** (Publikovat). 

    ![Publikování šablony virtuálního počítače](../media/tutorial-setup-classroom-lab/public-access.png)
1. Na **publikovat šablony** zprávou, projděte si zprávu a vybrat **publikovat**. Tento proces může trvat nějakou dobu v závislosti na tom, kolik virtuálních počítačů jsou vytvářeny.
    
    > [!IMPORTANT]
    > Jakmile je šablona publikovaná, nejde to vrátit. I když můžete znovu publikovat šablony. 
4. Počkejte na stav šablonu, kterou chcete změnit na **publikováno**. 

    ![Stav publikování](../media/how-to-create-manage-template/publish-status.png)
1. Přepněte na stránku **Virtual machines** (Virtuální počítače) a zkontrolujte, že se zobrazí virtuální počítače ve stavu **Unassigned** (Nepřiřazeno). Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Počkejte na vytvoření virtuálních počítačů. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Můžete je spustit na této stránce nebo nechat své studenty spustit virtuální počítače. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>Šablonu znovu publikoval 
Po publikování šablony pořád připojit k šabloně virtuálního počítače, aktualizujte ji a poté ji znovu publikovat. Při opětovném publikování šablony virtuálních počítačů, všechny uživatele, kterého odpojeny virtuálních počítačů a jejich rekonstruovaly podle aktualizovanou šablonu. 

1. Na stránce řídicího panelu vašeho testovacího prostředí, vyberte **znovu publikovat** v části šablony. 

    ![Tlačítko Publikovat](../media/how-to-create-manage-template/republish-button.png)
2. Na **šablonu znovu publikoval** zprávou, zkontrolujte text a vyberte **znovu publikovat** pokračujte. V opačném případě vyberte **zrušit**. 

    ![Opakované publikování zpráv šablonu](../media/how-to-create-manage-template/republish-template-message.png)
3. Zobrazení stavu publikovat na dlaždici v **šablony** oddílu.

    ![Stav publikování](../media/how-to-create-manage-template/republish-status-publishing.png)
4. Jakmile je šablona publikovaná, je stav nastaven **publikováno**. 

    ![Znovu úspěšné](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako správce vytvářet a spravovat účty pro testovací prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvoření a správa testovacích prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí konfigurovat a spravovat využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)
