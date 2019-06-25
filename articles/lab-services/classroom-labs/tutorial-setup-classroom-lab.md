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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 803fe6eff8804dbd407642386865fe975c8db524
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123278"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Kurz: Nastavení testovacího prostředí v učebně 
V tomto kurzu nastavíte testovací prostředí v učebně pomocí virtuálních počítačů, které používají studenti v učebně.  

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí v učebně
> * Přidání uživatelů do testovacího prostředí
> * Odeslání odkazu pro registraci studentům

## <a name="prerequisites"></a>Požadavky
Nastavení testovacího prostředí v učebně v účtu testovacího prostředí, musíte být členem jedné z těchto rolí v rámci účtu testovacího prostředí: Vlastník, Autor testovacího prostředí nebo Přispěvatel. Účet, který jste použili k vytvoření účtu testovacího prostředí je automaticky přidán do role vlastníka.

Vlastník testovacího prostředí můžete přidat ostatním uživatelům **Autor testovacího prostředí** role. Vlastník testovacího prostředí, například přidá vysokoškolští do role Tvůrce prostředí. Potom vysokoškolští vytvářet testovací prostředí s virtuálními počítači pro své třídy. Studenti použijte odkaz na registraci, který obdrží z vysokoškolští k zaregistrování do testovacího prostředí. Jakmile jsou registrované, můžou použít virtuální počítače v Tato praktická cvičení udělat třídu práce a práce z domova. Podrobné pokyny pro přidání uživatele do role Tvůrce prostředí najdete v tématu [přidání uživatele do role Tvůrce prostředí](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). Všimněte si, že aplikace Internet Explorer 11 se ještě nepodporuje. 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Zadejte maximální **počet virtuálních počítačů** v testovacím prostředí. Můžete zvýšit nebo decreate počet virtuálních počítačů po vytvoření testovacího prostředí nebo v existující testovací prostředí. Další informace najdete v tématu [aktualizovat počet virtuálních počítačů v testovacím prostředí](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Vyberte **Uložit**.

        ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stránce **Select virtual machine specifications** (Výběr specifikací virtuálních počítačů) proveďte následující kroky:
    1. Vyberte **velikost** virtuálních počítačů vytvořených v testovacím prostředí. V současné době **malé**, **střední**, **střední (virtualizace)** , **velké**, a **GPU** velikosti jsou povolené.
    3. Vyberte **image virtuálního počítače**, která se má použít k vytvoření virtuálních počítačů v testovacím prostředí. Pokud vyberete image Linuxu, zobrazí se možnost Povolit připojení ke vzdálené ploše pro něj. Podrobnosti najdete v tématu [povolit připojení ke vzdálené ploše pro Linux](how-to-enable-remote-desktop-linux.md).
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
    2. Vyberte **Connect** (Připojit) a připojte se k virtuálnímu počítači šablony. Pokud je šablona Linux virtuálního počítače, můžete vybrat, jestli se chcete připojit pomocí SSH nebo RDP (Pokud je povolen protokol RDP).
    1. Vyberte **resetovat heslo** k resetování hesla pro virtuální počítač. 
    1. Nainstalujte a nakonfigurujte na virtuálním počítači šablony požadovaný software. 
    1. **Zastavte** virtuální počítač.  
    1. Zadejte **popis** šablony.
9. Na stránce šablony vyberte **Next** (Další). 
10. Na stránce **Publish the template** (Publikování šablony) proveďte následující akce. 
    1. Chcete-li publikovat šablony okamžitě, vyberte **publikovat**.  

        > [!WARNING]
        > Publikování nejde vrátit zpět. 
    2. Pokud chcete publikování provést později, vyberte **Save for later** (Uložit na později). Po dokončení průvodce můžete publikovat šablony virtuálního počítače. Podrobnosti o tom, jak konfigurace a publikování po dokončení průvodce najdete v tématu [publikovat šablony](how-to-create-manage-template.md#publish-the-template-vm) tématu [Správa testovacích prostředí v učebnách](how-to-manage-classroom-labs.md) článku.

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

## <a name="set-quotas-for-users"></a>Nastavení kvót pro uživatele
Kvóty uživatele můžete nastavit pomocí následujících kroků: 

1. Vyberte **uživatelé** v nabídce vlevo, pokud ještě není aktivní na stránce. 
2. Vyberte **kvóty na uživatele:** na panelu nástrojů. 
3. Na **kvóty uživatele** stránky, zadejte počet hodin, kterou chcete přidělit pro jednotlivé uživatele (student): 
    1. **0 hodin (pouze schéma)** . Uživatelé mohou používat své virtuální počítače pouze v naplánovaném čase nebo jako vlastník testovacího prostředí se změní na virtuálních počítačích pro ně.

        ![Nula hodin – pouze naplánovaném čase](../media/how-to-configure-student-usage/zero-hours.png)
    1. **Celkový počet hodin testovacího prostředí na uživatele**. Uživatelé můžou používat svoje virtuální počítače pro zadaného počtu hodin (zadaný pro toto pole) **kromě naplánovaném čase**. Pokud vyberete tuto možnost, zadejte **počet hodin** v textovém poli. 

        ![Počet hodin na uživatele](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Vyberte **Uložit**. 
5. Nyní uvidíte změněné hodnoty na panelu nástrojů: **Kvóta na uživatele: &lt;počet hodin&gt;** . 

    ![Kvóta na uživatele](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>Nastavte plán pro testovací prostředí
Pokud jste nakonfigurovali nastavení kvóty pro **0 hodin (pouze schéma)** , je nutné nastavit plán pro testovací prostředí. V tomto kurzu nastavte plán bude týdenního plánu opakování.

1. Přepněte **plány** stránku a vybrat **přidat plán** na panelu nástrojů. 

    ![Přidejte tlačítko plán na stránce plány](../media/how-to-create-schedules/add-schedule-button.png)
2. Na **přidat plán** stránce, přepněte na **týdenní** v horní části. 
3. Pro **naplánovat dnů (povinné)** , vyberte dny, ve které chcete plánu se projeví. V následujícím příkladu je vybrána pondělí až pátek. 
4. Pro **z** zadejte **naplánovat datum zahájení** nebo vyberte datum tak, že vyberete **kalendáře** tlačítko. Toto pole je povinné. 
5. Pro **datum ukončení plánu**, zadejte nebo vyberte koncové datum, na kterém jsou virtuální počítače vypnou. 
6. Pro **počáteční čas**, vyberte čas, kdy chcete spustit virtuální počítače. Počáteční čas je vyžadována, pokud čas ukončení není nastaven. Vyberte **odebrat zahájit událost** Pokud chcete určit pouze čas ukončení. Pokud **počáteční čas** je zakázaná, vyberte **přidat počáteční událost** vedle rozevíracího seznamu, aby je. 
7. Pro **čas ukončení**, vyberte čas, kdy chcete vypnout virtuální počítače. Čas ukončení je vyžadována, pokud není nastaven počáteční čas. Vyberte **událost odebrání po zastavení** Pokud chcete zadat čas spuštění. Pokud **čas ukončení** je zakázaná, vyberte **události zastavení přidat** vedle rozevíracího seznamu, aby je.
8. Pro **časové pásmo (povinné)** , vyberte časové pásmo pro spuštění a zastavení časy, které jste zadali.  
9. Pro **poznámky**, zadejte popis případné poznámky pro daný plán. 
10. Vyberte **Uložit**. 

    ![Týdenní plán](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>Odeslat e-mail s odkazem registraci

1. Přepněte **uživatelé** zobrazit v případě, že již není na stránce. 
2. V seznamu vyberte konkrétní nebo všechny uživatele. Pokud chcete vybrat konkrétní uživatele, vyberte zaškrtávací políčka v prvním sloupci v seznamu. Chcete-li vybrat všechny uživatele, zaškrtněte políčko před název prvního sloupce (**název**) nebo vyberte všechna zaškrtávací políčka pro všechny uživatele v seznamu. Zobrazí se stav **pozvánku stavu** v tomto seznamu.  Na následujícím obrázku, stav pozvánku pro všechny studenty je nastavený na **neodeslaná Pozvánka**. 

    ![Vyberte studenty](../media/tutorial-setup-classroom-lab/select-students.png)
1. Vyberte **ikony e-mailu (obálky)** v jednom z řádků (nebo) vyberte **poslat pozvánku** na panelu nástrojů. Můžete také ukazatele myši nad jméno studenta v seznamu se zobrazí ikona e-mailu. 

    ![Poslat odkaz registraci e-mailem](../media/tutorial-setup-classroom-lab/send-email.png)
4. Na **registraci odeslat odkaz e-mailem** stránce, postupujte podle těchto kroků: 
    1. Typ **volitelnou zprávu** , který chcete odeslat pro studenty. E-mailu automaticky zahrnuje odkaz na registraci. 
    2. Na **registraci odeslat odkaz e-mailem** stránce **odeslat**. Zobrazí stav změnit na pozvánku **posílá se Pozvánka** a potom do **Pozvánka se odeslala**. 
        
        ![Odeslání pozvánky](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili testovací prostředí v učebně a nakonfigurovali ho. Pokud chcete získat informace o tom, jak může student přistupovat k virtuálnímu počítači v testovacím prostředí pomocí odkazu pro registraci, přejděte na další kurz:

> [!div class="nextstepaction"]
> [Připojení k virtuálnímu počítači v testovacím prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)

