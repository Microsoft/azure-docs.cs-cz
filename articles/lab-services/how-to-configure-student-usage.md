---
title: Konfigurace nastavení použití v laboratořích Azure Lab Services
description: Přečtěte si, jak nakonfigurovat počet studentů pro testovací prostředí, jak je zaregistrované v testovacím prostředí, určete počet hodin, po které může virtuální počítač používat, a další.
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 380a587eecb276c457b93ca3c3f3ac08b2239275
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791959"
---
# <a name="add-and-manage-lab-users"></a>Přidání a správa uživatelů testovacího prostředí

Tento článek popisuje, jak přidat uživatele studenta do testovacího prostředí, zaregistrovat je v testovacím prostředí, řídit počet dalších hodin, po které může virtuální počítač (VM) používat. 

Když přidáváte uživatele, je ve výchozím nastavení zapnutá možnost **omezit přístup** , a pokud nejsou v seznamu uživatelů, Students se nemůže zaregistrovat v testovacím prostředí, i když mají odkaz na registraci. Pomocí registračního odkazu, který odešlete, se můžou do testovacího prostředí zaregistrovat jenom uvedení uživatelé. Můžete vypnout možnost **omezit přístup**, která umožňuje studentům registraci v testovacím prostředí, pokud mají odkaz na registraci. 

Tento článek ukazuje, jak přidat uživatele do testovacího prostředí.

## <a name="add-users-from-an-azure-ad-group"></a>Přidání uživatelů ze skupiny Azure AD

### <a name="overview"></a>Přehled

Nyní můžete synchronizovat seznam uživatelů testovacího prostředí s existující skupinou Azure Active Directory (Azure AD), abyste nemuseli přidávat ani odstraňovat uživatele ručně. 

Skupinu Azure AD je možné vytvořit v rámci Azure Active Directory vaší organizace a spravovat tak přístup k prostředkům organizace a cloudovým aplikacím. Další informace najdete v tématu [skupiny Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). Pokud vaše organizace používá systém Microsoft Office 365 nebo služeb Azure, vaše organizace už bude mít správce, kteří spravují vaše Azure Active Directory. 

### <a name="sync-users-with-azure-ad-group"></a>Synchronizace uživatelů se skupinou Azure AD

> [!IMPORTANT]
> Ujistěte se, že seznam uživatelů je prázdný. Pokud v testovacím prostředí existují stávající uživatelé, které jste přidali ručně nebo importem souboru CSV, možnost synchronizace testovacího prostředí do existující skupiny se nezobrazí. 

1. Přihlaste se k [webu Azure Lab Services](https://labs.azure.com/).
1. Vyberte testovací prostředí, se kterým chcete pracovat.
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

### <a name="automatic-management-of-virtual-machines-based-on-changes-to-the-azure-ad-group"></a>Automatická správa virtuálních počítačů na základě změn ve skupině Azure AD 

Po synchronizaci testovacího prostředí se skupinou Azure AD bude počet virtuálních počítačů v testovacím prostředí automaticky odpovídat počtu uživatelů ve skupině. Nebudete už moct ručně aktualizovat kapacitu testovacího prostředí. Při přidání uživatele do skupiny Azure AD bude testovací prostředí automaticky přidávat virtuální počítač pro tohoto uživatele. Když se uživatel ze skupiny Azure AD odstraní, testovací prostředí automaticky odstraní virtuální počítač uživatele z testovacího prostředí. 

## <a name="add-users-manually-from-emails-or-csv-file"></a>Ruční přidání uživatelů z e-mailu nebo souboru CSV

V této části přidáte studenty ručně (e-mailovou adresou nebo nahráním souboru CSV). 

### <a name="add-users-by-email-address"></a>Přidat uživatele podle e-mailové adresy

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

### <a name="add-users-by-uploading-a-csv-file"></a>Přidání uživatelů odesláním souboru CSV

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

## <a name="send-invitations-to-users"></a>Odesílat pozvánky uživatelům

Chcete-li odeslat odkaz na registraci novým uživatelům, použijte jednu z následujících metod. 

Pokud je pro testovací prostředí povolená možnost **omezit přístup** , můžou se k registraci do testovacího prostředí použít jenom uvedení uživatelé s odkazem na registraci. Tato možnost je ve výchozím nastavení povolená. 

### <a name="invite-all-users"></a>Pozvat všechny uživatele

Tato metoda vám ukáže, jak odeslat e-mail s odkazem na registraci a volitelnou zprávou všem uvedeným studentům.

1. V podokně **Uživatelé** vyberte **pozvat vše**. 

    ![Tlačítko pro pozvání všech](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. V okně **poslat pozvánku e-mailem** zadejte volitelnou zprávu a pak vyberte **Odeslat**. 

    E-mail automaticky obsahuje odkaz na registraci. Pokud chcete odkaz pro registraci získat a uložit samostatně, vyberte tři tečky (**...**) v horní části podokna **Uživatelé** a pak vyberte **odkaz na registraci**. 

    ![Okno Odeslat odkaz pro registraci e-mailem](./media/tutorial-setup-classroom-lab/send-email.png)

    Sloupec **Pozvánka** v seznamu **Uživatelé** zobrazuje stav pozvánky pro každého přidaného uživatele. Stav by se měl změnit na **odesílání** a pak **\<date> na.** 

### <a name="invite-selected-users"></a>Pozvat vybrané uživatele

Tato metoda vám ukáže, jak pozvat jenom určité studenty a získat odkaz na registraci, který můžete sdílet s dalšími lidmi.

1. V podokně **Uživatelé** vyberte studenta nebo více studentů v seznamu. 

1. V řádku pro studenta, který jste vybrali, vyberte ikonu **obálky** nebo na panelu nástrojů vyberte **pozvat**. 

    ![Pozvat vybrané uživatele](./media/how-to-configure-student-usage/invite-selected-users.png)

1. V okně **poslat pozvánku e-mailem** zadejte volitelnou **zprávu** a pak vyberte **Odeslat**. 

    ![Odeslat e-mail vybraným uživatelům](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    V podokně **Uživatelé** se zobrazí stav této operace ve sloupci **Pozvánka** v tabulce. E-mail pozvánky zahrnuje registrační odkaz, který můžou studenti použít k registraci v testovacím prostředí.

## <a name="get-the-registration-link"></a>Získat odkaz na registraci

V této části můžete získat odkaz na registraci z portálu a odeslat ho pomocí vlastní e-mailové aplikace. 

1. V podokně **Uživatelé** vyberte **odkaz registrace**.

    ![Odkaz pro registraci studenta](./media/how-to-configure-student-usage/registration-link-button.png)

1. V okně **registrace uživatele** vyberte možnost **Kopírovat** a potom vyberte možnost **Hotovo**. 

    ![Okno "registrace uživatele"](./media/how-to-configure-student-usage/registration-link.png)

    Odkaz se zkopíruje do schránky. 
    
1. Do e-mailové aplikace vložte registrační odkaz a potom odešlete e-mail studentovi, aby se mohl student zaregistrovat pro třídu. 

## <a name="view-registered-users"></a>Zobrazit registrované uživatele

1. Přejít na web [Azure Lab Services](https://labs.azure.com) . 
1. Vyberte **Přihlásit** se a potom zadejte svoje přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft.
1. Na stránce **Moje cvičení** vyberte testovací prostředí, jehož využití chcete sledovat. 
1. V levém podokně vyberte **Uživatelé** nebo vyberte dlaždici **Uživatelé** . 

    V podokně **Uživatelé** se zobrazí seznam studentů, kteří se zaregistrovali v testovacím prostředí.  

    ![Seznam registrovaných uživatelů](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Nastavit kvóty pro uživatele

Pro každého studenta můžete nastavit hodinovou kvótu následujícím způsobem: 

1. V podokně **Uživatelé** vyberte možnost **kvóta na uživatele: \<number> hodiny** na panelu nástrojů. 
1. V okně **kvóta pro jednotlivé uživatele** zadejte počet hodin, který má každý student poskytnout mimo plánovanou dobu třídy, a pak vyberte **Uložit**.

    ![Okno kvóty na uživatele](./media/how-to-configure-student-usage/quota-per-user.png)    

    Změněné hodnoty se nyní zobrazují na **\<number of hours>** panelu nástrojů a v seznamu Uživatelé, jak je znázorněno zde:

    ![Doba kvóty pro jednotlivé uživatele](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Plánovaná doba spuštění virtuálních počítačů](how-to-create-schedules.md) se nepočítá s kvótou, která je přidělena studentovi. Kvóta je určena pro dobu mimo plánované hodiny, kterou student stráví na virtuálních počítačích. 

## <a name="set-additional-quotas-for-specific-users"></a>Nastavení dalších kvót pro konkrétní uživatele

Můžete určit kvóty pro určité studenty nad rámec běžných kvót, které byly nastaveny pro všechny uživatele v předchozí části. Pokud například jako instruktor nastavíte kvótu pro všechny studenty na 10 hodin a nastavíte pro konkrétního studenta dodatečnou kvótu 5 hodin, bude mít tento student 15 (10 + 5) hodin kvóty. Pokud tuto běžnou kvótu změníte později, například 15, Student získá 20 (15 + 5) hodin kvóty. Pamatujte, že tato celková kvóta je mimo naplánovaný čas. Čas, který student stráví na testovacím virtuálním počítači během naplánovaného času, se na tuto kvótu nepočítá. 

Pokud chcete nastavit další kvóty, udělejte toto:

1. V podokně **Uživatelé** vyberte ze seznamu studenta a pak na panelu nástrojů vyberte **Upravit kvótu** . 

    ![Tlačítko Upravit kvótu](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. V poli **kvóta úprav \<selected user or users email address> pro** zadejte počet dalších hodin testovacího prostředí, které chcete udělit vybraným studentům nebo studentům, a pak vyberte **použít**. 

    ![Upravit kvótu... okno](./media/how-to-configure-student-usage/additional-quota.png)

    Sloupec **využití** zobrazuje aktualizovanou kvótu pro vybrané studenty. 

    ![Nové použití pro uživatele](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Účty studenta

Pokud chcete přidat studenty do testovacího prostředí učebny, použijte jejich e-mailové účty. Studenti můžou mít tyto typy e-mailových účtů:

- E-mailový účet studenta poskytnutý instancí Azure Active Directory vaší univerzity
- E-mailový účet Microsoft-doména, například *Outlook.com*, *hotmail.com*, *MSN.com* nebo *Live.com*.
- E-mailový účet jiný než Microsoft, který poskytuje Yahoo! nebo Google. Tyto typy účtů však musí být propojeny s účet Microsoft.
- Účet GitHub. Tento účet musí být propojený s účet Microsoft.

### <a name="use-a-non-microsoft-email-account"></a>Používat e-mailový účet jiný než Microsoft

Studenti můžou k registraci a přihlášení do prostředí učebny používat e-mailové účty jiných společností než Microsoft.  Registrace ale vyžaduje, aby nejdřív vytvořily účet Microsoft propojené s jejich e-mailovými adresami, které nepoužívají Microsoftu.

Spousta studentů už může mít účet Microsoft propojená se svou e-mailovou adresou jinou než Microsoftu. Například studenti již mají účet Microsoft, pokud použili svou e-mailovou adresu s jinými produkty nebo službami společnosti Microsoft, jako je Office, Skype, OneDrive nebo Windows.  

Když studenti použijí registrační odkaz k přihlašování do učebny, zobrazí se jim výzva k zadání e-mailové adresy a hesla. Studentům, kteří se pokoušejí přihlašovat pomocí neúčet Microsoft, která není propojená s účet Microsoft, se zobrazí následující chybová zpráva: 

![Chybová zpráva při přihlášení](./media/how-to-configure-student-usage/cant-find-account.png)

Tady je odkaz pro studenty, kteří si můžou [zaregistrovat účet Microsoft](http://signup.live.com).  

> [!IMPORTANT]
> Když se studenti přihlásí do prostředí učebny, nebudou mít možnost vytvořit účet Microsoft. Z tohoto důvodu doporučujeme, abyste zahrnuli tento odkaz pro http://signup.live.com registraci do e-mailu s registračním prostředím učebny, které odesíláte studentům, kteří používají účty jiných společností než Microsoft.

### <a name="use-a-github-account"></a>Použití účtu GitHub

Studenti můžou k registraci a přihlašování do prostředí učebny použít taky existující účet GitHubu. Pokud již mají účet Microsoft propojeny se svým účtem GitHub, můžou se studenti přihlásit a zadat heslo, jak je znázorněno v předchozí části. 

Pokud ještě neodkazují svůj účet GitHubu na účet Microsoft, může to udělat takto:

1. Vyberte odkaz **Možnosti přihlášení** , jak je znázorněno zde:

    ![Odkaz Možnosti přihlášení](./media/how-to-configure-student-usage/signin-options.png)

1. V okně **Možnosti přihlášení** vyberte **Přihlásit se pomocí GitHubu**.

    ![Odkaz Přihlásit se pomocí GitHubu](./media/how-to-configure-student-usage/signin-github.png)

    Na příkazovém řádku Students vytvoří účet Microsoft propojená se svým účtem GitHubu. Propojení proběhne automaticky při výběru **Další**. Pak se hned přihlásí a připojí k vývojovému prostředí učebny.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Export seznamu uživatelů do souboru CSV

1. Přejít do podokna **Uživatelé** .
1. Na panelu nástrojů vyberte tři tečky (**...**) a pak vyberte **exportovat sdílený svazek clusteru**. 

    ![Tlačítko "exportovat CSV"](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)

## <a name="next-steps"></a>Další kroky

Viz následující články:

- Pro správce: [vytváření a Správa účtů testovacího prostředí](how-to-manage-lab-accounts.md)
- Pro vlastníky testovacích prostředí: [vytváření a Správa cvičení](how-to-manage-classroom-labs.md) a [nastavení a publikování šablon](how-to-create-manage-template.md)
- Pro uživatele testovacího prostředí: [Access Labs](how-to-use-classroom-lab.md)