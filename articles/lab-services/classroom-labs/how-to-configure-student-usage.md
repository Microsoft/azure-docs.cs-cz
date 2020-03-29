---
title: Konfigurace nastavení využití v laboratořích učebny služby Azure Lab Services
description: Naučte se, jak nakonfigurovat počet studentů pro testovací prostředí, získat je registrované v testovacím prostředí, řídit počet hodin, které můžou používat virtuální počítače a další.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159451"
---
# <a name="add-and-manage-lab-users"></a>Přidání a správa uživatelů testovacího prostředí

Tento článek popisuje, jak přidat studentuživatelů do testovacího prostředí, zaregistrovat je v testovacím prostředí, řídit počet dalších hodin, které můžete použít virtuální počítač (VM) a další. 

## <a name="add-users-to-a-lab"></a>Přidání uživatelů do testovacího prostředí

V této části přidáte studenty do testovacího prostředí ručně nebo nahráním souboru CSV. Udělejte toto:

1. V levém podokně vyberte **možnost Uživatelé**. 

    Ve výchozím nastavení je zapnutá možnost **Omezit přístup** a pokud nejsou v seznamu uživatelů, studenti se nemohou zaregistrovat v testovacím prostředí, i když mají registrační odkaz. Pouze uvedení uživatelé se mohou zaregistrovat v testovacím prostředí pomocí registračního odkazu, který odešlete. V tomto postupu přidáte uživatele do seznamu. Případně můžete vypnout **omezit přístup**, který umožňuje studentům zaregistrovat se v testovacím prostředí tak dlouho, dokud mají registrační odkaz. 

1. V horní části podokna **Uživatelé** vyberte **Přidat uživatele**a pak vyberte Přidat **podle e-mailové adresy**. 

    ![Tlačítko "Přidat uživatele"](../media/how-to-configure-student-usage/add-users-button.png)

1. V podokně **Přidat uživatele** zadejte e-mailové adresy studentů na samostatných řádcích nebo na jednom řádku odděleném středníky. 

    ![Přidání e-mailových adres uživatelů](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Vyberte **Uložit**. 

    V seznamu jsou uvedeny e-mailové adresy a stavy aktuálních uživatelů, ať už jsou zaregistrováni v testovacím prostředí nebo ne. 

    ![Seznam uživatelů](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Poté, co jsou studenti registrováni v laboratoři, seznam zobrazí jejich jména. Název, který je zobrazen v seznamu, se vytváří pomocí křestního jména a příjmení studentů ve službě Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Přidání uživatelů nahráním souboru CSV

Uživatele můžete přidat také nahráním souboru CSV, který obsahuje jejich e-mailové adresy.

1. V aplikaci Microsoft Excel vytvořte soubor CSV se seznamem e-mailových adres studentů v jednom sloupci.

    ![Seznam uživatelů v souboru CSV](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. V horní části podokna **Uživatelé** vyberte **Přidat uživatele**a pak vyberte **Nahrát soubor CSV**.

    ![Tlačítko "Nahrát CSV"](../media/how-to-configure-student-usage/upload-csv-button.png)

1. Vyberte soubor CSV, který obsahuje e-mailové adresy studentů, a pak vyberte **Otevřít**.

    V okně **Přidat uživatele** se zobrazí seznam e-mailových adres ze souboru CSV. 

    ![Okno "Přidat uživatele" s e-mailovými adresami ze souboru CSV](../media/how-to-configure-student-usage/add-users-window.png)

1. Vyberte **Uložit**. 

1. V podokně **Uživatelé** zobrazte seznam přidaných studentů. 

    ![Seznam přidaných uživatelů v podokně Uživatelé](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Odesílání pozvánek uživatelům

Chcete-li odeslat odkaz na registraci novým uživatelům, použijte jednu z následujících metod. 

Pokud je pro testovací prostředí povolena možnost **Omezit přístup,** mohou k registraci do testovacího prostředí použít pouze uvedení uživatelé. Tato možnost je ve výchozím nastavení povolená. 

### <a name="invite-all-users"></a>Pozvat všechny uživatele

Tato metoda ukazuje, jak odeslat e-mail s odkazem na registraci a volitelnou zprávou všem uvedeným studentům.

1. V podokně **Uživatelé** vyberte **Pozvat vše**. 

    ![Tlačítko "Pozvat vše"](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. V okně **Odeslat pozvánku e-mailem** zadejte volitelnou zprávu a pak vyberte **Odeslat**. 

    E-mail automaticky obsahuje registrační odkaz. Chcete-li získat a uložit odkaz pro registraci samostatně, vyberte tři tečky (**...**) v horní části podokna **Uživatelé** a pak vyberte **odkaz Registrace**. 

    ![Okno "Odeslat odkaz na registraci e-mailem"](../media/tutorial-setup-classroom-lab/send-email.png)

    Sloupec **Pozvánka** v seznamu **Uživatelé** zobrazuje stav pozvánky pro každého přidaného uživatele. Stav by se měl změnit na **Odeslat** a potom **odeslat \<k datu>**. 

### <a name="invite-selected-users"></a>Pozvat vybrané uživatele

Tato metoda vám ukáže, jak pozvat pouze určité studenty a získat registrační odkaz, který můžete sdílet s jinými lidmi.

1. V podokně **Uživatelé** vyberte v seznamu studenta nebo více studentů. 

1. V řádku vybraného studenta vyberte ikonu **obálky** nebo na panelu nástrojů vyberte **Pozvat**. 

    ![Pozvat vybrané uživatele](../media/how-to-configure-student-usage/invite-selected-users.png)

1. V okně **Odeslat pozvánku e-mailem** zadejte volitelnou **zprávu**a pak vyberte **Odeslat**. 

    ![Odeslání e-mailu vybraným uživatelům](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Podokno **Uživatelé** zobrazuje stav této operace ve sloupci **Pozvánka** v tabulce. E-mail s pozvánkou obsahuje registrační odkaz, který mohou studenti použít k registraci v testovacím prostředí.

## <a name="get-the-registration-link"></a>Získat registrační odkaz

V této sekci můžete získat registrační odkaz z portálu a odeslat jej pomocí vlastní e-mailové aplikace. 

1. V podokně **Uživatelé** vyberte **odkaz Registrace**.

    ![Odkaz pro registraci studenta](../media/how-to-configure-student-usage/registration-link-button.png)

1. V okně **Registrace uživatele** vyberte **Kopírovat**a pak vyberte **Hotovo**. 

    ![Okno "Registrace uživatele"](../media/how-to-configure-student-usage/registration-link.png)

    Odkaz se zkopíruje do schránky. 
    
1. Do e-mailové aplikace vložte registrační odkaz a odešlete e-mail studentovi, aby se student mohl zaregistrovat do kurzu. 

## <a name="view-registered-users"></a>Zobrazit registrované uživatele

1. Přejděte na web [Azure Lab Services.](https://labs.azure.com) 
1. Vyberte **Přihlásit**se a zadejte přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft.
1. Na stránce **Moje testovací prostředí** vyberte testovací prostředí, jehož využití chcete sledovat. 
1. V levém podokně vyberte **Uživatelé**nebo vyberte dlaždici **Uživatelé.** 

    Podokno **Uživatelé** zobrazuje seznam studentů, kteří se zaregistrovali ve vašem testovacím prostředí.  

    ![Seznam registrovaných uživatelů](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Nastavení kvót pro uživatele

Kvótu hodin pro každého studenta můžete nastavit takto: 

1. V podokně **Uživatelé** vyberte **kvótu \<pro jednotlivé uživatele: číslo> hodin na** panelu nástrojů. 
1. V okně **Kvóta na uživatele** zadejte počet hodin, které chcete každému studentovi přidělit mimo plánovaný čas kurzu, a pak vyberte **Uložit**.

    ![Okno "Kvóta na uživatele"](../media/how-to-configure-student-usage/quota-per-user.png)    

    Změněné hodnoty jsou nyní zobrazeny na **kvótě na \<uživatele: počet hodin>** tlačítko na panelu nástrojů a v seznamu uživatelů, jak je znázorněno zde:

    ![Kvóta hodin na uživatele](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Naplánovaná doba spuštění virtuálních disponecí](how-to-create-schedules.md) se nezapočítává do kvóty přidělené studentovi. Kvóta je pro čas mimo plánované hodiny, které student stráví na virtuálních počítačích. 

## <a name="set-additional-quotas-for-specific-users"></a>Nastavení dalších kvót pro konkrétní uživatele

Můžete určit kvóty pro určité studenty nad rámec běžných kvót, které byly nastaveny pro všechny uživatele v předchozím oddíle. Pokud například jako instruktor nastavíte kvótu pro všechny studenty na 10 hodin a nastavíte dodatečnou kvótu 5 hodin pro konkrétního studenta, tento student získá 15 (10 + 5) hodin kvóty. Pokud později změníte běžnou kvótu na řekněme na 15, student získá 20 (15 + 5) hodin kvóty. Nezapomeňte, že tato celková kvóta je mimo plánovaný čas. Čas, který student stráví na virtuálním počítači testovacího prostředí během naplánovaného času se nezapočítává do této kvóty. 

Chcete-li nastavit další kvóty, postupujte takto:

1. V podokně **Uživatelé** vyberte studenta ze seznamu a pak na panelu nástrojů vyberte **Upravit kvótu.** 

    ![Tlačítko "Upravit kvótu"](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. V>**e-mailové \<adresy Upravit kvótu pro vybraného uživatele nebo uživatele **zadejte počet dalších hodin v testovacím prostředí, které chcete udělit vybranému studentovi nebo studentům, a pak vyberte **Použít**. 

    !["Upravit kvótu ..." Okno](../media/how-to-configure-student-usage/additional-quota.png)

    Ve sloupci **Použití** se zobrazí aktualizovaná kvóta pro vybrané studenty. 

    ![Nové využití pro uživatele](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Studentské účty

Chcete-li přidat studenty do učebny, použijte jejich e-mailové účty. Studenti mohou mít následující typy e-mailových účtů:

- E-mailový účet pro studenty, který poskytuje instance Azure Active Directory vaší univerzity pro Office 365. 
- E-mailový účet domény Microsoft, například *outlook.com*, *hotmail.com*, *msn.com*nebo *live.com*.
- E-mailový účet jiných společností než Microsoft, například účet poskytovaný společností Yahoo! nebo Google. Tyto typy účtů však musí být propojeny s účtem Microsoft.
- Účet GitHub. Tento účet musí být propojen s účtem Microsoft.

### <a name="use-a-non-microsoft-email-account"></a>Použití e-mailového účtu jiného než Microsoft
Studenti se můžou zaregistrovat a přihlásit do učební laboratoře pomocí e-mailových účtů jiných společností než Microsoft.  Registrace však vyžaduje, aby nejprve vytvořili účet Microsoft, který je propojen s jejich e-mailovou adresou jiného než Microsoft.

Mnoho studentů už může mít účet Microsoft, který je propojený s jejich e-mailovou adresou, na které není Microsoft. Například studenti už mají účet Microsoft, pokud používají svou e-mailovou adresu s jinými produkty nebo službami Microsoftu, jako je Office, Skype, OneDrive nebo Windows.  

Když se studenti pomocí registračního odkazu přihlásí do třídy, zobrazí se jim výzva k zadání e-mailové adresy a hesla. Studenti, kteří se pokusí přihlásit pomocí účtu jiného, než je Microsoft, který není propojený s účtem Microsoft, obdrží následující chybovou zprávu: 

![Při přihlášení se zobrazí chybová zpráva](../media/how-to-configure-student-usage/cant-find-account.png)

Tady je odkaz, na který si studenti [mohou zaregistrovat účet Microsoft](http://signup.live.com).  

> [!IMPORTANT]
> Když se studenti přihlásí do učebny, nemají možnost si vytvořit účet Microsoft. Z tohoto důvodu doporučujeme zahrnout tento odkaz http://signup.live.compro registraci , do e-mailu s registrací laboratoře ve třídě, který odesíláte studentům, kteří používají účty jiných společností než Microsoft.

### <a name="use-a-github-account"></a>Použití účtu GitHub
Studenti můžou taky použít existující účet GitHub k registraci a přihlášení do učební laboratoře. Pokud už mají účet Microsoft propojený se svým účtem GitHub, studenti se můžou přihlásit a zadat své heslo, jak je znázorněno v předchozí části. 

Pokud ještě svůj účet GitHub nepropojili s účtem Microsoft, můžou udělat toto:

1. Vyberte odkaz **Možnosti přihlášení,** jak je znázorněno zde:

    ![Odkaz Možnosti přihlášení](../media/how-to-configure-student-usage/signin-options.png)

1. V okně **Možnosti přihlášení** vyberte **Přihlásit se pomocí GitHubu**.

    ![Odkaz "Přihlásit se pomocí GitHubu"](../media/how-to-configure-student-usage/signin-github.png)

    Na výzvu si studenti vytvoří účet Microsoft, který je propojený s jejich účtem GitHub. Propojení se stane automaticky, když vyberte **Další**. Jsou pak okamžitě přihlášeni a připojeni k laboratoři ve třídě.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Export seznamu uživatelů do souboru CSV

1. Přejděte do podokna **Uživatelé.**
1. Na panelu nástrojů vyberte tři tečky (**...**) a pak vyberte **Exportovat CSV**. 

    ![Tlačítko "Export OVAT CSV"](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Další kroky
Viz následující články:

- Pro správce: [Vytvoření a správa účtů testovacího prostředí](how-to-manage-lab-accounts.md)
- Pro vlastníky testovacího prostředí: [Vytváření a správa testovacích prostředí](how-to-manage-classroom-labs.md) a nastavení a publikování [šablon](how-to-create-manage-template.md)
- Pro uživatele testovacího prostředí: [Přístup k testovacím prostředím ve třídě](how-to-use-classroom-lab.md)
