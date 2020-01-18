---
title: Konfigurace nastavení využití v učebně Labs Azure Lab Services
description: Přečtěte si, jak nakonfigurovat počet uživatelů pro testovací prostředí, získat je zaregistrovaných v testovacím prostředí, určit počet hodin, po které může virtuální počítač používat, a další.
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 80e8bc47f6e6293d70bbc9fae888abdf5527fe93
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169212"
---
# <a name="add-and-manage-lab-users"></a>Přidání a správa uživatelů testovacího prostředí
Tento článek popisuje, jak přidat uživatele do testovacího prostředí, získat je zaregistrované v testovacím prostředí a určit počet hodin, po které může virtuální počítač používat, a další. 


## <a name="add-users-to-the-lab"></a>Přidat uživatele do testovacího prostředí

1. V nabídce vlevo vyberte **Uživatelé** . Ve výchozím nastavení je povolená možnost **omezit přístup** . Pokud je toto nastavení zapnuté, nemůže se uživatel zaregistrovat v testovacím prostředí, i když uživatel nemá odkaz na registraci, pokud se uživatel nezobrazuje v seznamu uživatelů. Pomocí registračního odkazu, který odešlete, se můžou v testovacím prostředí zaregistrovat jenom uživatelé v seznamu. V tomto postupu přidáte uživatele do seznamu. Alternativně můžete vypnout možnost **omezit přístup**, která umožňuje uživatelům registraci v testovacím prostředí, pokud mají odkaz na registraci. 
2. Na panelu nástrojů vyberte **Přidat uživatele** a pak vyberte **Přidat e-mailové adresy**. 

    ![Tlačítko Přidat uživatele](../media/how-to-configure-student-usage/add-users-button.png)
1. Na stránce **Přidat uživatele** zadejte e-mailové adresy uživatelů na samostatných řádcích nebo na jeden řádek oddělený středníky. 

    ![Přidat e-mailové adresy uživatele](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Vyberte **Save** (Uložit). V seznamu se zobrazí e-mailové adresy uživatelů a jejich stavů (registrovaných nebo ne). 

    ![Seznam uživatelů](../media/how-to-configure-student-usage/users-list-new.png)

    > [!NOTE]
    > Po registraci do testovacího prostředí se v seznamu zobrazí jména uživatelů. Název zobrazený v seznamu je vytvořený pomocí jména a příjmení uživatele v Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Přidání uživatelů odesláním souboru CSV
Uživatele můžete také přidat tak, že nahrajete soubor CSV s e-mailovými adresami uživatelů.

1. Vytvořte soubor CSV s e-mailovými adresami uživatelů v jednom sloupci.

    ![Soubor CSV s uživateli](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na stránce **Uživatelé** testovacího prostředí vyberte **Přidat uživatele** na panelu nástrojů a pak vyberte **nahrát sdílený svazek clusteru**.

    ![Tlačítko nahrát sdílený svazek clusteru](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Vyberte soubor CSV s e-mailovými adresami uživatele. Po výběru možnosti **otevřít** po výběru souboru CSV se zobrazí následující okno **Přidat uživatele** . Seznam e-mailových adres je vyplněn e-mailovými adresami ze souboru CSV. 

    ![Přidat uživatele, který se naplní pomocí e-mailových adres ze souboru CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. V okně **Přidat uživatele** vyberte **Uložit** . 
5. Ověřte, že se v seznamu uživatelů zobrazí uživatelé. 

    ![Seznam přidaných uživatelů](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Odesílat pozvánky uživatelům
Chcete-li odeslat odkaz na registraci studentům, použijte jednu z následujících metod. První metoda ukazuje, jak odeslat e-maily studentům pomocí odkazu na registraci a volitelné zprávy. Druhá metoda vám ukáže, jak získat odkaz na registraci, který můžete sdílet s ostatními způsobem, jaký požadujete. 

Pokud je pro testovací prostředí povolený **přístup s omezením** , můžou k registraci do testovacího prostředí používat jenom uživatelé v seznamu uživatelů. Tato možnost je ve výchozím nastavení povolená. 

### <a name="invite-all-users"></a>Pozvat všechny uživatele
1. Pokud na stránce již nejste, přepněte do zobrazení **Uživatelé** a na panelu nástrojů vyberte **pozvat vše** . 

    ![Vybrat studenty](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Na stránce **Odeslat pozvánku podle e-mailu** zadejte volitelnou zprávu a pak vyberte **Odeslat**. E-mail automaticky obsahuje odkaz na registraci. Tuto registrační odkaz můžete získat tak, že vyberete **... (tři tečky)** na panelu nástrojů a na **odkaz pro registraci**. 

    ![Poslat odkaz na registraci e-mailem](../media/tutorial-setup-classroom-lab/send-email.png)
4. V seznamu **uživatelů** se zobrazí stav **Pozvánka** . Stav by se měl změnit na **odesílání** a pak na **> Datum \<** . 

    Další informace o tom, jak přidat studenty do třídy a jak spravovat jejich použití v testovacím prostředí, najdete v tématu [jak nakonfigurovat využití studenta](how-to-configure-student-usage.md).

### <a name="invite-selected-users"></a>Pozvat vybrané uživatele

1. V seznamu vyberte uživatele nebo více uživatelů. 
2. Pak vyberte ikonu **obálky** , kterou vidíte ve vybraném řádku (nebo) na panelu nástrojů vyberte **pozvat** . 

    ![Pozvat vybrané uživatele](../media/how-to-configure-student-usage/invite-selected-users.png)
3. V okně **poslat pozvánku e-mailem** zadejte volitelnou **zprávu**a pak vyberte **Odeslat**. 

    ![Odeslat e-mail vybraným uživatelům](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Stav této operace se zobrazí ve sloupci **Pozvánka** v seznamu **Uživatelé** . E-mail pozvánky zahrnuje registrační odkaz, který můžou uživatelé použít k registraci v testovacím prostředí.

1. Pokud se stránka už nezobrazuje, přepněte do zobrazení **Uživatelé** . 

## <a name="get-registration-link"></a>Získat odkaz na registraci
Můžete taky získat odkaz na registraci z portálu a odeslat ho pomocí vlastní e-mailové aplikace klienta. 

1. Přepněte do zobrazení **Uživatelé** výběrem možnosti **Uživatelé** v nabídce vlevo. 
2. Vybrat **... (tři tečky)** na panelu nástrojů a pak vyberte **odkaz registrace**.

    ![Odkaz pro registraci studenta](../media/how-to-configure-student-usage/registration-link-button.png)
1. V dialogovém okně **registrace uživatele** vyberte tlačítko **Kopírovat** . Odkaz se zkopíruje do schránky. Vložte ho do editoru e-mailů a e-mail odešlete studentovi. 

    ![Odkaz pro registraci studenta](../media/how-to-configure-student-usage/registration-link.png)
2. V dialogovém okně **registrace uživatele** vyberte možnost **Hotovo**. 
4. Pošlete **registrační odkaz** studentovi, aby se mohl student zaregistrovat pro třídu. 

## <a name="view-users-registered-with-the-lab"></a>Zobrazení uživatelů zaregistrovaných do testovacího prostředí

V nabídce vlevo vyberte **Uživatelé** a zobrazí se seznam uživatelů registrovaných v testovacím prostředí. 

![Seznam uživatelů registrovaných v testovacím prostředí](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>Nastavit kvóty pro uživatele
Kvóty na uživatele můžete nastavit pomocí následujících kroků: 

1. V nabídce vlevo vyberte **Uživatelé** , pokud už stránka není aktivní. 
2. Vybrat **kvótu pro uživatele: \<číslo > hodiny** na panelu nástrojů. 
3. Na stránce **kvóta na uživatele** zadejte počet hodin, který chcete každému uživateli (studentovi) poskytnout mimo plánovaný čas třídy, a pak vyberte **Uložit**.

    ![Kvóta na uživatele](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Změněné hodnoty na panelu nástrojů teď vidíte: **kvóta pro uživatele: \<počet hodin >** . 

    ![Kvóta na uživatele – po](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Plánovaná doba spuštění virtuálních počítačů](how-to-create-schedules.md) se nepočítá s kvótou přidělenou uživateli. Kvóta je určena pro dobu mimo plánované hodiny, kterou student stráví na virtuálních počítačích. 

## <a name="set-additional-quota-for-a-specific-user"></a>Nastavení dodatečné kvóty pro konkrétního uživatele
Pro uživatele můžete zadat další kvótu. Tato kvóta je kromě společné sady kvót pro všechny uživatele v předchozí části. Například pokud jste vy (jako instruktor) nastavili kvótu pro všechny uživatele na 10 hodin a pro konkrétní uživatele nastavíte kvótu o velikosti 5 hodin, budou mít uživatelé 15 (10 + 5) hodin kvóty. Pokud později změníte běžnou kvótu, například 15, pak uživatel získá 20 (15 + 5) hodin kvóty. Pamatujte, že tato celková kvóta je mimo naplánovaný čas. Čas, který student stráví na testovacím virtuálním počítači během plánování, se na tuto kvótu nepočítá. 

Chcete-li to provést, postupujte takto:

1. Vyberte uživatele (student) ze seznamu uživatelů na stránce **Uživatelé** .
2. Pak na panelu nástrojů vyberte **Upravit kvótu** . 

    ![Tlačítko Upravit kvótu](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Zadejte počet **dalších hodin** pro vybraného uživatele nebo uživatele a pak vyberte **použít**. 

    ![Dodatečná kvóta pro uživatele](../media/how-to-configure-student-usage/additional-quota.png)
4. Ve sloupci **využití** se zobrazí aktualizované využití pro uživatele. 

    ![Nové použití pro uživatele](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Účty studenta
Pokud chcete přidat studenty do testovacího prostředí učebny, použijte jejich e-mailové účty. Můžou se používat tyto typy e-mailových účtů:

- E-mailový účet studenta, který poskytuje Office 365 Azure Active Directory vaší univerzity (AAD). 
- E-mailový účet Microsoftu, například `@outlook.com`, `@hotmail.com`, `@msn.com`nebo `@live.com`.
- E-mailový účet, který není od Microsoftu, který poskytuje společnost Yahoo nebo Google. Tyto typy účtů však musí být propojeny s účet Microsoft.
- Účet GitHub. Tento účet musí být propojený s účet Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Použití e-mailového účtu jiného než Microsoftu
Studenti můžou k registraci a přihlášení do prostředí učebny používat e-mailové účty jiných společností než Microsoft.  Registrace ale vyžaduje, aby studenti nejprve vytvořili účet Microsoft, která je propojená s e-mailovou adresou od jiných společností než Microsoftu.

Mnoho studentů už může mít účet Microsoft propojených s vašimi e-mailovými adresami, které nejsou od Microsoftu. Například studenti již mají účet Microsoft, pokud používali svou e-mailovou adresu s jinými produkty nebo službami společnosti Microsoft, jako je Office, Skype, OneDrive nebo Windows.  

Když student klikne na registrační adresu URL pro přihlášení k učebně, zobrazí se výzva k zadání e-mailové adresy a hesla. Pokud se student pokusí přihlásit pomocí neúčet Microsoft, který nemá účet Microsoft propojený, student obdrží tuto chybovou zprávu: 

![Chybová zpráva](../media/how-to-configure-student-usage/cant-find-account.png)

Pokud si chcete zaregistrovat účet Microsoft, studenti by měli přejít na [http://signup.live.com](http://signup.live.com).  

> [!IMPORTANT]
> Když se studenti přihlásí do prostředí učebny, nezískají možnost vytvořit účet Microsoft. Proto doporučujeme, abyste do registračního e-mailu v testovacím prostředí pro učebnu zavedli tento odkaz pro registraci, který odesíláte studentům, kteří používají účty jiných společností než Microsoft.

### <a name="using-a-github-account"></a>Použití účtu GitHub
Studenti můžou k registraci a přihlašování do prostředí učebny použít taky existující účet GitHubu. Pokud má student již účet Microsoft propojený se svým účtem GitHubu, může se přihlásit a zadat heslo, jak je znázorněno v předchozí části. Pokud ještě neodkazují svůj účet GitHubu na účet Microsoft, měly by si vybrat **Možnosti přihlášení**:

![Odkaz Možnosti přihlášení](../media/how-to-configure-student-usage/signin-options.png)

Na stránce **Možnosti přihlášení** vyberte možnost **Přihlásit se pomocí GitHubu**.

![Přihlaste se pomocí odkazu na GitHub](../media/how-to-configure-student-usage/signin-github.png)

Nakonec se zobrazí výzva k vytvoření účet Microsoft, která je propojená s jejich účtem GitHubu. K tomu dochází automaticky, když student vybere **Next (další**).  Student se pak hned přihlásí a připojí k vývojovému prostředí učebny.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)
