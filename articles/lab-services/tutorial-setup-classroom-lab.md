---
title: Nastavení testovacího prostředí v učebně v Azure Lab Services | Microsoft Docs
description: V tomto kurzu použijete Azure Lab Services k nastavení testovacího prostředí pro učebnu s virtuálními počítači, které používají studenti ve vaší třídě.
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 8093a1fd270cdba8bdccaf48737bf6737bdd394d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787414"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Kurz: Nastavení testovacího prostředí v učebně 
V tomto kurzu nastavíte testovací prostředí v učebně pomocí virtuálních počítačů, které používají studenti v učebně.  

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí v učebně
> * Přidat uživatele do testovacího prostředí
> * Nastavit plán pro testovací prostředí
> * Poslat e-mail pozvánky studentům

## <a name="prerequisites"></a>Předpoklady
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
    2. Vyberte dny, ve kterých se má plán projevit. V následujícím příkladu je vybrána možnost Monday-Friday. 
    3. Vyberte **koncové datum** pro plán.
    8. Vyberte **Uložit**. 

        ![Nastavit plán opakování](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Nyní na stránce **Přidat naplánovanou událost** pro **poznámky (volitelné)** zadejte libovolný popis nebo poznámky k plánu. 
4. Na stránce **Přidat naplánovanou událost** vyberte **Uložit**. 

    ![Týdenní plán](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Přejděte k počátečnímu datu v kalendáři, abyste ověřili, že je plán nastavený.
    
    ![Plán v kalendáři](./media/how-to-create-schedules/schedule-calendar.png)

    Další informace o vytváření a správě plánů pro třídu naleznete v tématu [Create and Manage Schedule for Labs](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Přidat uživatele do testovacího prostředí

Když přidáváte uživatele, je ve výchozím nastavení zapnutá možnost **omezit přístup** , a pokud nejsou v seznamu uživatelů, Students se nemůže zaregistrovat v testovacím prostředí, i když mají odkaz na registraci. Pomocí registračního odkazu, který odešlete, se můžou do testovacího prostředí zaregistrovat jenom uvedení uživatelé. Můžete vypnout možnost **omezit přístup**, která umožňuje studentům registraci v testovacím prostředí, pokud mají odkaz na registraci. 

### <a name="add-users-from-an-azure-ad-group"></a>Přidání uživatelů ze skupiny Azure AD

Seznam uživatelů testovacího prostředí můžete synchronizovat s existující skupinou Azure Active Directory (Azure AD), abyste nemuseli přidávat ani odstraňovat uživatele ručně. 

Skupinu Azure AD je možné vytvořit v rámci Azure Active Directory vaší organizace a spravovat tak přístup k prostředkům organizace a cloudovým aplikacím. Další informace najdete v tématu [skupiny Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). Pokud vaše organizace používá systém Microsoft Office 365 nebo služeb Azure, vaše organizace už bude mít správce, kteří spravují vaše Azure Active Directory. 

> [!IMPORTANT]
> Ujistěte se, že seznam uživatelů je prázdný. Pokud v testovacím prostředí existují stávající uživatelé, které jste přidali ručně nebo importem souboru CSV, možnost synchronizace testovacího prostředí do existující skupiny se nezobrazí. 

1. V levém podokně vyberte **Uživatelé**. 
1. Klikněte na **synchronizovat ze skupiny**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Přidávání uživatelů synchronizací ze skupiny Azure AD":::
    
1. Zobrazí se výzva k výběru existující skupiny Azure AD pro synchronizaci testovacího prostředí s. 
    
    Pokud v seznamu nevidíte skupinu Azure AD, může to být z následujících důvodů:

    -   Pokud jste uživatelem typu Host pro Azure Active Directory (obvykle mimo organizaci, která vlastní Azure AD), a nemůžete Hledat skupiny v rámci Azure AD. V takovém případě nebudete moct v tomto případě do testovacího prostředí přidat skupinu Azure AD. 
    -   Skupiny Azure AD vytvořené prostřednictvím týmů se v tomto seznamu nezobrazují. Aplikaci Azure Lab Services můžete do týmů přidat, chcete-li vytvářet a spravovat laboratoře přímo z ní. Podívejte se na Další informace o [správě seznamu uživatelů testovacího prostředí v rámci týmů](how-to-manage-user-lists-within-teams.md). 
1. Po vybrání skupiny Azure AD pro synchronizaci testovacího prostředí s klikněte na **Přidat**.
1. Jakmile se testovací prostředí synchronizuje, vystaví se všichni členové skupiny Azure AD do testovacího prostředí jako uživatelé a seznam uživatelů se zobrazí jako aktualizovaný. Pouze lidé z této skupiny Azure AD budou mít přístup k vašemu testovacímu prostředí. Seznam uživatelů se každých 24 hodin aktualizuje tak, aby odpovídal nejnovějšímu členství ve skupině Azure AD. Můžete také kliknout na tlačítko synchronizovat na kartě Uživatelé a ručně synchronizovat nejnovější změny ve skupině Azure AD.
1. Pozvěte uživatele do testovacího prostředí tak, že kliknete na tlačítko **pozvat všechny** , které pošle e-mail všem uživatelům s odkazem na registraci do testovacího prostředí. 

### <a name="add-users-manually-from-emails-or-csv-file"></a>Ruční přidání uživatelů z e-mailu nebo souboru CSV

V této části přidáte studenty ručně (e-mailovou adresou nebo nahráním souboru CSV). 

#### <a name="add-users-by-email-address"></a>Přidat uživatele podle e-mailové adresy

1. V levém podokně vyberte **Uživatelé**. 
1. Klikněte na **Přidat uživatele ručně**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="Ruční přidání uživatelů":::
1. Vyberte **Přidat podle e-mailové adresy** (výchozí), zadejte e-mailové adresy studentů na samostatné řádky nebo na jeden řádek oddělený středníky. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="Přidat e-mailové adresy uživatelů":::
1. Vyberte **Uložit**. 

    V seznamu se zobrazí e-mailové adresy a stavy aktuálních uživatelů, ať už jsou zaregistrované v testovacím prostředí, nebo ne. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Seznam uživatelů":::

    > [!NOTE]
    > Po registraci studentů v testovacím prostředí se v seznamu zobrazí jejich jména. Název, který je zobrazen v seznamu, je vytvořen pomocí prvního a posledního jména studentů v Azure Active Directory. 

#### <a name="add-users-by-uploading-a-csv-file"></a>Přidání uživatelů odesláním souboru CSV

Uživatele můžete také přidat tak, že nahrajete soubor CSV, který obsahuje jejich e-mailové adresy. 

Textový soubor CSV se používá k ukládání tabulkových dat oddělených čárkou (CSV) (čísla a text). Místo uložení informací do polí sloupce (například v tabulkách) ukládá soubor CSV informace oddělené čárkami. Každý řádek v souboru CSV bude mít stejný počet polí oddělených čárkou. Pomocí aplikace Excel můžete snadno vytvářet a upravovat soubory CSV.

1. V aplikaci Microsoft Excel vytvořte soubor CSV se seznamem e-mailových adres studentů v jednom sloupci.

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="Seznam uživatelů v souboru CSV":::
1. V horní části podokna **Uživatelé** vyberte **Přidat uživatele** a pak vyberte **nahrát sdílený svazek clusteru**.
1. Vyberte soubor CSV, který obsahuje e-mailové adresy studentů, a pak vyberte **otevřít**.

    V okně **Přidat uživatele** se zobrazí seznam e-mailových adres ze souboru CSV. 
1. Vyberte **Uložit**. 
1. V podokně **Uživatelé** si prohlédněte seznam přidaných studentů. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Seznam přidaných uživatelů v podokně Uživatelé"::: 

## <a name="send-invitation-emails-to-users"></a>Posílání e-mailů pozvánky uživatelům

1. Pokud na stránce již nejste, přepněte do zobrazení **Uživatelé** a na panelu nástrojů vyberte **pozvat vše** . 

    ![Vybrat studenty](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Na stránce **Odeslat pozvánku podle e-mailu** zadejte volitelnou zprávu a pak vyberte **Odeslat**. E-mail automaticky obsahuje odkaz na registraci. Tuto registrační odkaz můžete získat tak, že vyberete **... (tři tečky)** na panelu nástrojů a na **odkaz pro registraci**. 

    ![Poslat odkaz na registraci e-mailem](./media/tutorial-setup-classroom-lab/send-email.png)
4. V seznamu **uživatelů** se zobrazí stav **Pozvánka** . Stav by se měl změnit na **odesílání** a pak na **&lt; datum &gt; odeslání**. 

Další informace o tom, jak přidat studenty do třídy a jak spravovat jejich použití v testovacím prostředí, najdete v tématu [jak nakonfigurovat využití studenta](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili testovací prostředí pro třídu v Azure. Pokud chcete získat informace o tom, jak může student přistupovat k virtuálnímu počítači v testovacím prostředí pomocí odkazu pro registraci, přejděte na další kurz:

> [!div class="nextstepaction"]
> [Připojení k virtuálnímu počítači v testovacím prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)