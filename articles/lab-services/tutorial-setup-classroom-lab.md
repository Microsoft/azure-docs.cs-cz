---
title: Nastavení testovacího prostředí v učebně v Azure Lab Services | Microsoft Docs
description: V tomto kurzu použijete Azure Lab Services k nastavení testovacího prostředí pro učebnu s virtuálními počítači, které používají studenti ve vaší třídě.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 1155646d31ddb8a0a3abce025acde5c4cb645f54
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336730"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Kurz: Nastavení testovacího prostředí v učebně 
V tomto kurzu nastavíte testovací prostředí v učebně pomocí virtuálních počítačů, které používají studenti v učebně.  

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí v učebně
> * Přidat uživatele do testovacího prostředí
> * Nastavit plán pro testovací prostředí
> * Poslat e-mail pozvánky studentům

## <a name="prerequisites"></a>Požadavky
V tomto kurzu nastavíte testovací prostředí s virtuálními počítači pro vaši třídu. Pokud chcete nastavit prostředí učebny v účtu testovacího prostředí, musíte být členem jedné z těchto rolí v účtu testovacího prostředí: vlastník, autor testovacího prostředí nebo přispěvatele. Účet, který jste použili k vytvoření účtu testovacího prostředí, je automaticky přidán do role vlastníka. K vytvoření testovacího prostředí učebny můžete použít uživatelský účet, který jste použili k vytvoření účtu testovacího prostředí. 

Toto je typický pracovní postup při použití Azure Lab Services:

1. Tvůrce účtu testovacího prostředí přidá další uživatele do role **testovacího prostředí** . Například autor nebo správce účtu testovacího prostředí přidává pedagogy do role **Tvůrce testovacího prostředí** , aby mohli vytvářet Labs pro své třídy. 
2. Pak pedagogy vytvoří Labs s virtuálními počítači pro své třídy a odesílají odkazy na registraci studentům ve třídě. 
3. Studenti používají registrační odkaz, který obdrží od pedagogů k registraci do testovacího prostředí. Jakmile jsou zaregistrované, můžou pomocí virtuálních počítačů v laboratoři provádět pracovní a domácí práci. 

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně
V tomto kroku vytvoříte testovací prostředí pro třídu v Azure. 

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). Všimněte si, že aplikace Internet Explorer 11 ještě není podporována. 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. Vyberte **nové testovací prostředí**. 
    
    ![Snímek obrazovky, který zobrazuje "Azure Lab Services" s vybraným tlačítkem nové testovací prostředí.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí a vyberte **Další**.  

        ![Vytvoření testovacího prostředí v učebně](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Na stránce **přihlašovací údaje virtuálního počítače** zadejte výchozí přihlašovací údaje pro všechny virtuální počítače v testovacím prostředí. Zadejte **jméno** a **heslo** pro uživatele a pak vyberte **Další**.  

        ![Nové okno testovacího prostředí](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Uživatelské jméno a heslo si poznamenejte. Znovu se už nezobrazí.
    3. Na stránce **zásady testovacího prostředí** vyberte **Dokončit**. 

        ![Kvóta pro každého uživatele](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Měla by se zobrazit následující obrazovka, která zobrazuje stav vytvoření virtuálního počítače šablony. Tato operace trvá až 20 minut. 

    ![Stav vytvoření virtuálního počítače šablony](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na stránce **Šablona** proveďte následující kroky: tyto kroky jsou pro kurz **volitelné** .

    1. Vyberte **Connect** (Připojit) a připojte se k virtuálnímu počítači šablony. Pokud se jedná o virtuální počítač šablony pro Linux, můžete zvolit, jestli se chcete připojit pomocí protokolu SSH nebo RDP (Pokud je povolený protokol RDP).
    3. Nainstalujte a nakonfigurujte software vyžadovaný pro vaši třídu na virtuálním počítači šablony. 
    4. **Zastavte** virtuální počítač šablony.  

    > [!NOTE]
    > Virtuální počítače šablony účtují **náklady** při jejich spuštění, takže se ujistěte, že je virtuální počítač šablony vypnutý, když ho nepotřebujete používat. 

## <a name="publish-the-template-vm"></a>Publikování šablony virtuálního počítače
V tomto kroku publikujete virtuální počítač šablony. Když publikujete virtuální počítač šablony, Azure Lab Services vytvoří virtuální počítače v testovacím prostředí pomocí šablony. Všechny virtuální počítače mají stejnou konfiguraci jako šablona.

1. Na stránce **Šablona** vyberte **publikovat** na panelu nástrojů. 

    ![Tlačítko publikovat šablonu](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Publikování nejde vrátit zpět. 
2. Na stránce **publikovat šablonu** zadejte počet virtuálních počítačů, které chcete v testovacím prostředí vytvořit, a pak vyberte **publikovat**. 

    ![Šablona publikování – počet virtuálních počítačů](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Na stránce se zobrazí **stav publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Počkejte, než se publikování dokončí, a pak přepněte na stránku **fondu virtuálních počítačů** tak, že v nabídce vlevo vyberete **virtuální počítače** nebo dlaždici **virtuální počítače** . Ověřte, že se zobrazují virtuální počítače, které jsou v **nepřiřazeném** stavu. Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Když Educator zapnete virtuální počítač studenta, neovlivní kvóta pro studenta. Kvóta pro uživatele určuje počet hodin testovacího prostředí uživatele mimo plánovaný čas třídy. Další informace o kvótách najdete v tématu [nastavení kvót pro uživatele](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Nastavte plán pro testovací prostředí.
Vytvořte naplánovanou událost pro testovací prostředí, aby se virtuální počítače v testovacím prostředí automaticky spustily nebo zastavily v určitých časech. Kvóta uživatele (výchozí: 10 hodin), kterou jste zadali dříve, je další čas přiřazený každému uživateli mimo tento naplánovaný čas. 

1. Přepněte na stránku **plány** a na panelu nástrojů vyberte **Přidat naplánovanou událost** . 

    ![Snímek obrazovky zobrazující tlačítko "Přidat naplánované události" na stránce plány](./media/how-to-create-schedules/add-schedule-button.png)
2. Na stránce **Přidat naplánovanou událost** proveďte následující kroky:
    1. Potvrďte, že **Standard** je vybraný jako **Typ události**.  
    2. Vyberte **počáteční datum** pro třídu. 
    4. Vyberte **čas spuštění** , ve kterém chcete virtuální počítače spustit.
    5. Vyberte **dobu** , po kterou mají být virtuální počítače vypnuté. 
    6. Vyberte **časové pásmo** pro dobu zahájení a ukončení, kterou jste zadali. 
3. Na stejné stránce **Přidat naplánovanou událost** vyberte aktuální plán v části **Opakovat** .  

    ![Tlačítko Přidat plán na stránce plány](./media/how-to-create-schedules/select-current-schedule.png)
5. V dialogovém okně **Opakovat** proveďte následující kroky:
    1. Potvrďte, že je pro pole **Opakovat** nastaven **každý týden** . 
    2. Vyberte dny, ve kterých se má plán projevit. V následujícím příkladu je vybrána možnost pondělí – pátek. 
    3. Vyberte **koncové datum** pro plán.
    8. Vyberte **Uložit**. 

        ![Nastavit plán opakování](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Nyní na stránce **Přidat naplánovanou událost** pro **poznámky (volitelné)** zadejte libovolný popis nebo poznámky k plánu. 
4. Na stránce **Přidat naplánovanou událost** vyberte **Uložit**. 

    ![Týdenní plán](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Přejděte k počátečnímu datu v kalendáři, abyste ověřili, že je plán nastavený.
    
    ![Plán v kalendáři](./media/how-to-create-schedules/schedule-calendar.png)

    Další informace o vytváření a správě plánů pro třídu naleznete v tématu [Create and Manage Schedule for učeben Labs](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Přidat uživatele do testovacího prostředí

1. V nabídce vlevo vyberte **Uživatelé** . Ve výchozím nastavení je povolená možnost **omezit přístup** . Pokud je toto nastavení zapnuté, nemůže se uživatel zaregistrovat v testovacím prostředí, i když uživatel nemá odkaz na registraci, pokud se uživatel nezobrazuje v seznamu uživatelů. Pomocí registračního odkazu, který odešlete, se můžou v testovacím prostředí zaregistrovat jenom uživatelé v seznamu. V tomto postupu přidáte uživatele do seznamu. Alternativně můžete vypnout možnost **omezit přístup**, která umožňuje uživatelům registraci v testovacím prostředí, pokud mají odkaz na registraci. 
2. Na panelu nástrojů vyberte **Přidat uživatele** a pak vyberte **Přidat podle e-mailové adresy**. 

    ![Tlačítko Přidat uživatele](./media/how-to-configure-student-usage/add-users-button.png)
1. Na stránce **Přidat uživatele** zadejte e-mailové adresy uživatelů na samostatných řádcích nebo na jeden řádek oddělený středníky. 

    ![Přidat e-mailové adresy uživatele](./media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Vyberte **Uložit**. V seznamu se zobrazí e-mailové adresy uživatelů a jejich stavů (registrovaných nebo ne). 

    ![Seznam uživatelů](./media/how-to-configure-student-usage/users-list-new.png)

    Po registraci do testovacího prostředí se v seznamu zobrazí jména uživatelů. 
    

## <a name="send-invitation-emails-to-users"></a>Posílání e-mailů pozvánky uživatelům

1. Pokud na stránce již nejste, přepněte do zobrazení **Uživatelé** a na panelu nástrojů vyberte **pozvat vše** . 

    ![Vybrat studenty](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Na stránce **Odeslat pozvánku podle e-mailu** zadejte volitelnou zprávu a pak vyberte **Odeslat**. E-mail automaticky obsahuje odkaz na registraci. Tuto registrační odkaz můžete získat tak, že vyberete **... (tři tečky)** na panelu nástrojů a na **odkaz pro registraci**. 

    ![Poslat odkaz na registraci e-mailem](./media/tutorial-setup-classroom-lab/send-email.png)
4. V seznamu **uživatelů** se zobrazí stav **Pozvánka** . Stav by se měl změnit na **odesílání** a pak na ** &lt; datum &gt; odeslání**. 

    Další informace o tom, jak přidat studenty do třídy a jak spravovat jejich použití v testovacím prostředí, najdete v tématu [jak nakonfigurovat využití studenta](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili testovací prostředí pro třídu v Azure. Pokud chcete získat informace o tom, jak může student přistupovat k virtuálnímu počítači v testovacím prostředí pomocí odkazu pro registraci, přejděte na další kurz:

> [!div class="nextstepaction"]
> [Připojení k virtuálnímu počítači v testovacím prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)

