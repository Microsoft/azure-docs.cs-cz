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
ms.openlocfilehash: 0c50a321cbeb0d07a5039038ff796df00463ac8a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385665"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Kurz: Nastavení testovacího prostředí v učebně 
V tomto kurzu nastavíte testovací prostředí v učebně pomocí virtuálních počítačů, které používají studenti v učebně.  

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí v učebně
> * Přidat uživatele do testovacího prostředí
> * Odeslání odkazu pro registraci studentům

## <a name="prerequisites"></a>Požadavky
Pokud chcete nastavit testovací prostředí učebny v účtu testovacího prostředí, musíte být členem jedné z těchto rolí v účtu testovacího prostředí: Vlastník, autor testovacího prostředí nebo Přispěvatel. Účet, který jste použili k vytvoření účtu testovacího prostředí, je automaticky přidán do role vlastníka.

Vlastník testovacího prostředí může přidat další uživatele do role **testovacího prostředí** . Například vlastník testovacího prostředí přidá profesory do role tvůrce testovacího prostředí. Pak profesory vytvořit Labs s virtuálními počítači pro své třídy. Studenti používají registrační odkaz, který obdrží z profesory k registraci do testovacího prostředí. Jakmile jsou zaregistrované, můžou pomocí virtuálních počítačů v laboratoři provádět pracovní a domácí práci. Podrobné pokyny pro přidání uživatelů do role testovacího prostředí najdete v tématu [Přidání uživatele do role testovacího prostředí](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). Všimněte si, že aplikace Internet Explorer 11 ještě není podporována. 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Zadejte maximální **počet virtuálních počítačů** v testovacím prostředí. Po vytvoření testovacího prostředí nebo v existujícím testovacím prostředí můžete počet virtuálních počítačů zvýšit nebo vytvořit. Další informace najdete v tématu [aktualizace počtu virtuálních počítačů v testovacím prostředí](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab) .
    6. Vyberte **Uložit**.

        ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stránce **Select virtual machine specifications** (Výběr specifikací virtuálních počítačů) proveďte následující kroky:
    1. Vyberte **velikost** virtuálních počítačů vytvořených v testovacím prostředí. V současné době jsou povolené **malé**, **střední**, **střední (virtualizační)** , **velké**a střední velikosti **GPU** .
    3. Vyberte **image virtuálního počítače**, která se má použít k vytvoření virtuálních počítačů v testovacím prostředí. Pokud vyberete image pro Linux, zobrazí se vám možnost Povolit pro ni připojení ke vzdálené ploše. Podrobnosti najdete v tématu [Povolení připojení ke vzdálené ploše pro Linux](how-to-enable-remote-desktop-linux.md).
    4. Vyberte **Další**.

        ![Zadání specifikací virtuálních počítačů](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na stránce **Set credentials** (Nastavení přihlašovacích údajů) zadejte výchozí přihlašovací údaje ke všem virtuálním počítačům v testovacím prostředí. 
    1. Zadejte **jméno uživatele** pro všechny virtuální počítače v testovacím prostředí.
    2. Zadejte **heslo** tohoto uživatele. 

        > [!IMPORTANT]
        > Uživatelské jméno a heslo si poznamenejte. Znovu se už nezobrazí.
    3. Vyberte **Vytvořit**. 

        ![Nastavit přihlašovací údaje](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na stránce **Configure template** (Konfigurace šablony) se zobrazí stav vytváření testovacího prostředí. Vytvoření šablony v testovacím prostředí může trvat až 20 minut. 

    ![Konfigurace šablony](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Po dokončení konfigurace šablony se zobrazí následující stránka: 

    ![Stránka Configure template (Konfigurace šablony) po dokončení konfigurace](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Na stránce **Konfigurovat šablonu** proveďte následující kroky: Tyto kroky jsou pro kurz **volitelné** .
    2. Vyberte **Connect** (Připojit) a připojte se k virtuálnímu počítači šablony. Pokud se jedná o virtuální počítač šablony pro Linux, můžete zvolit, jestli se chcete připojit pomocí protokolu SSH nebo RDP (Pokud je povolený protokol RDP).
    1. Pokud chcete resetovat heslo pro tento virtuální počítač, vyberte **resetovat heslo** . 
    1. Nainstalujte a nakonfigurujte na virtuálním počítači šablony požadovaný software. 
    1. **Zastavte** virtuální počítač.  
    1. Zadejte **popis** šablony.
9. Na stránce šablony vyberte **Next** (Další). 
10. Na stránce **Publish the template** (Publikování šablony) proveďte následující akce. 
    1. Pokud chcete šablonu publikovat hned, vyberte **publikovat**.  

        > [!WARNING]
        > Publikování nejde vrátit zpět. 
    2. Pokud chcete publikování provést později, vyberte **Save for later** (Uložit na později). Po dokončení průvodce můžete publikovat virtuální počítač šablony. Podrobnosti o tom, jak nakonfigurovat a publikovat po dokončení průvodce, najdete v části [publikování šablony](how-to-create-manage-template.md#publish-the-template-vm) v článku [How to Manage The učeben Labs](how-to-manage-classroom-labs.md) .

        ![Publikování šablony](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Zobrazí se **průběh publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po úspěšném publikování šablony se zobrazí následující stránka. Vyberte **Done** (Hotovo).

    ![Publikování šablony – úspěch](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zobrazí se **řídicí panel** testovacího prostředí. 
    
    ![Řídicí panel testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Přepněte na stránku **virtuálních počítačů** výběrem možnosti virtuální počítače v nabídce vlevo nebo výběrem dlaždice virtuální počítače. Ověřte, že se zobrazují virtuální počítače, které  jsou v nepřiřazeném stavu. Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Přidat uživatele do testovacího prostředí

1. V nabídce vlevo vyberte **Uživatelé** . Ve výchozím nastavení je povolená možnost **omezit přístup** . Pokud je toto nastavení zapnuté, nemůže se uživatel zaregistrovat v testovacím prostředí, i když uživatel nemá odkaz na registraci, pokud se uživatel nezobrazuje v seznamu uživatelů. Pomocí registračního odkazu, který odešlete, se můžou v testovacím prostředí zaregistrovat jenom uživatelé v seznamu. V tomto postupu přidáte uživatele do seznamu. Alternativně můžete vypnout možnost **omezit přístup**, která umožňuje uživatelům registraci v testovacím prostředí, pokud mají odkaz na registraci. 
2. Na panelu nástrojů vyberte **Přidat uživatele** . 

    ![Tlačítko Přidat uživatele](../media/how-to-configure-student-usage/add-users-button.png)
1. Na stránce **Přidat uživatele** zadejte e-mailové adresy uživatelů na samostatných řádcích nebo na jeden řádek oddělený středníky. 

    ![Přidat e-mailové adresy uživatele](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Vyberte **Uložit**. V seznamu se zobrazí e-mailové adresy uživatelů a jejich stavů (registrovaných nebo ne). 

    ![Seznam uživatelů](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Nastavit kvóty pro uživatele
Kvóty na uživatele můžete nastavit pomocí následujících kroků: 

1. V nabídce vlevo vyberte **Uživatelé** , pokud už stránka není aktivní. 
2. Vybrat **kvótu pro jednotlivé uživatele: 10 hodin** na panelu nástrojů. 
3. Na stránce **kvóta na uživatele** zadejte počet hodin, který chcete každému uživateli (studentovi) přidělit: 
    1. **Celkový počet hodin testovacího prostředí na uživatele** Uživatelé můžou použít svoje virtuální počítače pro nastavený počet hodin (určených pro toto pole) **kromě naplánovaného času**. Pokud vyberete tuto možnost, zadejte do textového pole **počet hodin** . 

        ![Počet hodin na uživatele](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 hodin (pouze plán)** . Uživatelé můžou svoje virtuální počítače používat jenom během naplánovaného času nebo když se na nich stane vlastník testovacího prostředí na virtuálních počítačích.

        ![Nula hodin – pouze plánovaný čas](../media/how-to-configure-student-usage/zero-hours.png)
    4. Vyberte **Uložit**. 
5. Změněné hodnoty se zobrazí na panelu nástrojů nyní: **Kvóta na uživatele &lt;: počet hodin&gt;** 

    ![Kvóta na uživatele](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>Nastavte plán pro testovací prostředí.
Pokud jste nakonfigurovali nastavení kvóty na **0 hodin (jenom plán)** , musíte nastavit plán testovacího prostředí. V tomto kurzu nastavíte plán jako opakovaný týdenní plán.

1. Přepněte na stránku **plány** a na panelu nástrojů vyberte **Přidat plán** . 

    ![Tlačítko Přidat plán na stránce plány](../media/how-to-create-schedules/add-schedule-button.png)
2. Na stránce **Přidat plán** přepněte v horní části  na týdně. 
3. Pro **dny plánu (povinné)** vyberte dny, ve kterých se má plán projevit. V následujícím příkladu je vybrána možnost pondělí – pátek. 
4. V poli **od** zadejte **Datum zahájení plánu** nebo vyberte datum výběrem tlačítka **Kalendář** . Toto pole je povinné. 
5. Do pole **Datum ukončení plánu**zadejte nebo vyberte koncové datum, kdy se mají virtuální počítače vypnout. 
6. V části **čas spuštění**vyberte čas, kdy se mají virtuální počítače spustit. Čas spuštění je vyžadován, pokud není nastaven čas zastavení. Vyberte možnost **Odebrat událost spuštění** , pokud chcete určit pouze dobu zastavení. Pokud je **počáteční čas** zakázaný, vyberte **Přidat spustit událost** vedle rozevíracího seznamu a povolte ho. 
7. V poli **čas zastavení**vyberte čas, kdy se mají virtuální počítače vypnout. Pokud není nastaven počáteční čas, je nutné čas zastavení. Vyberte **Odebrat událost zastavení** , pokud chcete zadat pouze počáteční čas. Pokud je **Doba zastavení** zakázaná, můžete ji povolit výběrem **Přidat událost zastavení** vedle rozevíracího seznamu.
8. V poli **časové pásmo (povinné)** vyberte časové pásmo pro dobu zahájení a ukončení, kterou jste zadali.  
9. Pro **poznámky**zadejte libovolný popis nebo poznámky k plánu. 
10. Vyberte **Uložit**. 

    ![Týdenní plán](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>Odeslání e-mailu s odkazem na registraci

1. Pokud na stránce již nejste, přepněte do zobrazení **Uživatelé** . 
2. Vyberte konkrétní nebo všechny uživatele ze seznamu. Chcete-li vybrat konkrétní uživatele, zaškrtněte políčka v prvním sloupci seznamu. Chcete-li vybrat možnost všichni uživatelé, zaškrtněte políčko před nadpisem prvního sloupce (**název**) nebo zaškrtněte všechna políčka pro všechny uživatele v seznamu. Stav pozvánky můžete zobrazit v tomto  seznamu.  Na následujícím obrázku je stav pozvánky pro všechny studenty nastavený na pozvánku Neodesláno. 

    ![Vybrat studenty](../media/tutorial-setup-classroom-lab/select-students.png)
1. Vyberte **ikonu e-mailu (obálka)** v jednom z řádků (nebo) vyberte **Odeslat pozvánku** na panelu nástrojů. Chcete-li zobrazit ikonu e-mailu, můžete také umístit ukazatel myši na jméno studenta v seznamu. 

    ![Poslat odkaz na registraci e-mailem](../media/tutorial-setup-classroom-lab/send-email.png)
4. Na stránce **Odeslat odkaz pro registraci e-mailem** proveďte tyto kroky: 
    1. Zadejte **volitelnou zprávu** , kterou chcete odeslat studentům. E-mail automaticky obsahuje odkaz na registraci. 
    2. Na stránce **Odeslat odkaz pro registraci e-mailem** vyberte **Odeslat**. Zobrazí se stav pozvánky, která se mění a **odesílá Pozvánka** , a pak na **pozvánku odeslanou**. 
        
        ![Odeslané pozvánky](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili testovací prostředí v učebně a nakonfigurovali ho. Pokud chcete získat informace o tom, jak může student přistupovat k virtuálnímu počítači v testovacím prostředí pomocí odkazu pro registraci, přejděte na další kurz:

> [!div class="nextstepaction"]
> [Připojení k virtuálnímu počítači v testovacím prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)

