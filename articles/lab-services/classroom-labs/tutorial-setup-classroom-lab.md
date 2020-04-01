---
title: Nastavení testovacího prostředí v učebně v Azure Lab Services | Microsoft Docs
description: V tomto kurzu pomocí Azure Lab Services nastavit učebny laboratoře s virtuálními počítači, které používají studenti ve vaší třídě.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 166ec4db2a2891d25a1e80526f8c1bd9770f9eef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77592216"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Kurz: Nastavení testovacího prostředí v učebně 
V tomto kurzu nastavíte testovací prostředí v učebně pomocí virtuálních počítačů, které používají studenti v učebně.  

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí v učebně
> * Přidání uživatelů do testovacího prostředí
> * Nastavení plánu pro testovací prostředí
> * Odeslání e-mailu s pozvánkou studentům

## <a name="prerequisites"></a>Požadavky
V tomto kurzu nastavíte testovací prostředí s virtuálními počítači pro vaši třídu. Chcete-li nastavit testovací prostředí učebny v testovacím účtu, musíte být členem jedné z těchto rolí v účtu testovacího prostředí: Vlastník, Tvůrce testovacího prostředí nebo Přispěvatel. Účet, který jste použili k vytvoření účtu testovacího prostředí, se automaticky přidá do role vlastníka. Můžete tedy použít uživatelský účet, který jste použili k vytvoření účtu testovacího prostředí, k vytvoření testovacího prostředí učebny. 

Tady je typický pracovní postup při používání Služby Azure Lab:

1. Tvůrce účtu testovacího prostředí přidá další uživatele do role **Tvůrce testovacího prostředí.** Například tvůrce/správce účtu testovacího prostředí přidá profesory do role **Tvůrce testovacího prostředí,** aby mohli vytvářet testovací prostředí pro své třídy. 
2. Potom profesoři vytvořit laboratoře s virtuálními aplikacemi pro jejich třídy a odeslat registrační odkazy pro studenty ve třídě. 
3. Studenti používají registrační odkaz, který obdrží od profesorů, aby se zaregistrovali do laboratoře. Jakmile jsou zaregistrovány, mohou použít virtuální chod v laboratořích k práci ve třídě a domácí práci. 

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně
V tomto kroku vytvoříte testovací prostředí pro vaši třídu v Azure. 

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). Všimněte si, že internet explorer 11 ještě není podporován. 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. Vyberte **nové testovací prostředí**. 
    
    ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí a vyberte **Další**.  

        ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Na stránce **přihlašovací údaje virtuálního počítače** zadejte výchozí pověření pro všechny virtuální počítače v testovacím prostředí. Zadejte **jméno** a **heslo** uživatele a pak vyberte **Další**.  

        ![Nové okno laboratoře](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Uživatelské jméno a heslo si poznamenejte. Znovu se už nezobrazí.
    3. Na stránce **Zásady testovacího prostředí** vyberte **Dokončit**. 

        ![Kvóta pro každého uživatele](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Měli byste vidět na následující obrazovce, která zobrazuje stav vytvoření šablony virtuálního počítače. Tato operace trvá až 20 minut. 

    ![Stav vytvoření šablony virtuálního počítače](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na stránce **Šablona** postupujte takto: Tyto kroky jsou **volitelné** pro kurz.

    1. Vyberte **Connect** (Připojit) a připojte se k virtuálnímu počítači šablony. Pokud se jedná o virtuální počítač se šablonou Linuxu, můžete zvolit, zda se chcete připojit pomocí SSH nebo RDP (pokud je rdp povolen).
    3. Nainstalujte a nakonfigurujte software potřebný pro vaši třídu na virtuálním počítači šablony. 
    4. **Zastavte** virtuální počítače šablony.  

## <a name="publish-the-template-vm"></a>Publikování šablony virtuálního počítače
V tomto kroku publikujete virtuální počítače šablony. Když publikujete šablonu virtuálního počítače, Azure Lab Services vytvoří virtuální počítače v testovacím prostředí pomocí šablony. Všechny virtuální počítače mají stejnou konfiguraci jako šablona.

1. Na stránce **Šablona** vyberte **Publikovat** na panelu nástrojů. 

    ![Tlačítko Publikovat šablonu](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Publikování nejde vrátit zpět. 
2. Na stránce **Šablony publikování** zadejte počet virtuálních počítačů, které chcete vytvořit v testovacím prostředí, a pak vyberte **Publikovat**. 

    ![Šablona publikování – počet virtuálních počítače](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Na stránce se zobrazí **stav publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Počkejte, až publikování je dokončena a přepněte na stránku **fondu virtuálních počítačů** výběrem **virtuální počítače** v levém menu nebo výběrem virtuální **počítače** dlaždice. Zkontrolujte, zda se zobrazují virtuální počítače, které jsou ve stavu **Nepřiřazené.** Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Když pedagog zapne virtuální počítač pro studenty, kvóta pro studenta není ovlivněna. Kvóta pro uživatele určuje počet hodin testovacího prostředí, které má uživatel k dispozici mimo plánovaný čas třídy. Další informace o kvótách naleznete v tématu [Nastavení kvót pro uživatele](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Nastavení plánu pro testovací prostředí
Vytvořte naplánovanou událost pro testovací prostředí tak, aby virtuální chody v testovacím prostředí byly automaticky spuštěny nebo zastaveny v určitých časech. Kvóta uživatele (výchozí hodnota: 10 hodin), kterou jste zadali dříve, je dodatečný čas přiřazený každému uživateli mimo tento naplánovaný čas. 

1. Přepněte na stránku **Plány** a na panelu nástrojů vyberte **Přidat naplánovanou událost.** 

    ![Tlačítko Přidat plán na stránce Plány](../media/how-to-create-schedules/add-schedule-button.png)
2. Na stránce **Přidat naplánovanou událost** postupujte takto:
    1. Zkontrolujte, zda je vybrán typ **události** **Standard** .  
    2. Vyberte **počáteční datum** třídy. 
    4. Vyberte **čas zahájení,** ve kterém chcete virtuální chod spustit.
    5. Vyberte **čas zastavení,** kdy mají být virtuální ms vypnuty. 
    6. Vyberte **časové pásmo** pro zadané časy zahájení a zastavení. 
3. Na stejné stránce **Přidat naplánovanou událost** vyberte aktuální plán v části **Opakovat.**  

    ![Tlačítko Přidat plán na stránce Plány](../media/how-to-create-schedules/select-current-schedule.png)
5. V dialogovém okně **Opakovat** proveďte následující kroky:
    1. Zkontrolujte, zda je pro pole **Opakovat** nastaven **každý týden.** 
    2. Vyberte dny, kdy se má plán projevit. V následujícím příkladu je vybrána možnost Pondělí-Pátek. 
    3. Vyberte **koncové datum** plánu.
    8. Vyberte **Uložit**. 

        ![Nastavit plán opakování](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Nyní na stránce **Přidat naplánovanou událost** pro **poznámky (volitelné)** zadejte libovolný popis nebo poznámky pro plán. 
4. Na stránce **Přidat naplánovanou událost** vyberte **Uložit**. 

    ![Týdenní rozvrh](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Přejděte k počátečnímu datu v kalendáři a ověřte, zda je plán nastaven.
    
    ![Plán v kalendáři](../media/how-to-create-schedules/schedule-calendar.png)

    Další informace o vytváření a správě plánů pro kurz naleznete v tématu [Vytvoření a správa plánu pro laboratoře ve třídě](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Přidání uživatelů do testovacího prostředí

1. V levé nabídce vyberte **Uživatelé.** Ve výchozím nastavení je povolena možnost **Omezit přístup.** Pokud je toto nastavení zapnuté, uživatel se nemůže zaregistrovat v testovacím prostředí, i když má odkaz na registraci, pokud není v seznamu uživatelů. Pouze uživatelé v seznamu se mohou zaregistrovat v testovacím prostředí pomocí odeslaný registrační odkaz. V tomto postupu přidáte uživatele do seznamu. Případně můžete vypnout **omezit přístup**, který umožňuje uživatelům zaregistrovat se v testovacím prostředí tak dlouho, dokud mají odkaz na registraci. 
2. Na panelu nástrojů vyberte **Přidat uživatele** a pak **vyberte Přidat podle e-mailové adresy**. 

    ![Tlačítko Přidat uživatele](../media/how-to-configure-student-usage/add-users-button.png)
1. Na stránce **Přidat uživatele** zadejte e-mailové adresy uživatelů do samostatných řádků nebo do jednoho řádku odděleného středníky. 

    ![Přidání e-mailových adres uživatelů](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Vyberte **Uložit**. V seznamu se zobrazí e-mailové adresy uživatelů a jejich stavy (registrované nebo neregistrované). 

    ![Seznam uživatelů](../media/how-to-configure-student-usage/users-list-new.png)

    Zobrazí se jména uživatelů v seznamu poté, co jsou registrovány do testovacího prostředí. 
    

## <a name="send-invitation-emails-to-users"></a>Odeslání e-mailů s pozvánkami uživatelům

1. Pokud ještě na stránce nejste, přepněte do zobrazení **Uživatelé** a na panelu nástrojů vyberte **Pozvat vše.** 

    ![Výběr studentů](../media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Na stránce **Odeslat pozvánku e-mailem** zadejte volitelnou zprávu a pak vyberte **Odeslat**. E-mail automaticky obsahuje registrační odkaz. Můžete získat tento registrační odkaz výběrem **... (tři tečky)** na panelu nástrojů a **odkaz Registrace**. 

    ![Poslat registrační odkaz e-mailem](../media/tutorial-setup-classroom-lab/send-email.png)
4. Stav **pozvánky** se zobrazí v seznamu **Uživatelé.** Stav by se měl změnit na **Odeslat** a potom **na &lt;Odesláno k datu&gt;**. 

    Další informace o přidávání studentů do kurzu a správě jejich používání testovacího prostředí najdete v tématu [Konfigurace využití studentů](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili testovací prostředí pro vaši třídu v Azure. Pokud chcete získat informace o tom, jak může student přistupovat k virtuálnímu počítači v testovacím prostředí pomocí odkazu pro registraci, přejděte na další kurz:

> [!div class="nextstepaction"]
> [Připojení k virtuálnímu počítači v testovacím prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)

