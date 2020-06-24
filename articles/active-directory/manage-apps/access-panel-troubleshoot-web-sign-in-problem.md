---
title: Potíže s přihlášením k webu přístupového panelu | Microsoft Docs
description: Pokyny k řešení problémů, se kterými se můžete setkat při pokusu o přihlášení k používání přístupového panelu
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f2ad4db231c616b3022ecafc62b12d6d81b67fc
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760809"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Potíže s přihlašováním k webu přístupového panelu

Přístupový panel je webový portál, který umožňuje uživateli, který má pracovní nebo školní účet ve službě Azure Active Directory (Azure AD) zobrazovat a spouštět cloudové aplikace, ke kterým správce Azure AD udělil přístup. Uživatel, který má edice Azure AD, může pomocí přístupového panelu použít taky samoobslužnou skupinu a možnosti správy aplikací. Přístupový panel je oddělený od Azure Portal a nevyžaduje, aby uživatelé měli předplatné Azure.

Uživatelé se můžou přihlásit k přístupovému panelu, pokud mají ve službě Azure AD pracovní nebo školní účet.

-   Uživatele je možné ověřit přímo pomocí Azure AD.

-   Uživatele lze ověřit pomocí Active Directory Federation Services (AD FS) (AD FS).

-   Uživatele lze ověřit pomocí služby Windows Server Active Directory.

Pokud má uživatel předplatné pro Azure nebo Office 365 a používá Azure Portal nebo aplikaci Office 365, bude moct přístupovou panel snadno používat, aniž by se musel znovu přihlašovat. Uživatelům, kteří nejsou ověřeni, se zobrazí výzva, abyste se přihlásili pomocí uživatelského jména a hesla pro svůj účet ve službě Azure AD. Pokud má organizace nakonfigurovanou federaci, stačí zadat uživatelské jméno.

## <a name="general-issues-to-check-first"></a>Obecné problémy k první kontrole 

-   Ujistěte se, že se uživatel přihlašuje ke **správné adrese URL**:<https://myapps.microsoft.com>

-   Ujistěte se, že prohlížeč uživatele přidal adresu URL ke svým **důvěryhodným webům** .

-   Ujistěte se, že je **povolený** účet uživatele pro přihlášení.

-   Ujistěte se, že účet uživatele není **uzamčený.**

-   Ujistěte se, že **heslo uživatele není prošlé nebo zapomenuté.**

-   Ujistěte se, že **Multi-Factor Authentication** neblokuje přístup uživatelů.

-   Zajistěte, aby **zásady podmíněného přístupu** nebo zásady **ochrany identit** neblokovaly přístup uživatelů.

-   Ujistěte se, že **kontaktní údaje pro ověření** uživatele jsou aktuální, aby bylo možné vyhovět Multi-Factor Authentication nebo zásadám podmíněného přístupu.

-   Nezapomeňte taky vymazat soubory cookie v prohlížeči a zkusit se znovu přihlásit.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Požadavky prohlížeče schůzky na přístupový panel

Přístupový panel vyžaduje prohlížeč, který podporuje jazyk JavaScript a je povolený pomocí šablon stylů CSS. Pokud chcete na přístupovém panelu použít jednotné přihlašování založené na heslech (SSO), musí se v prohlížeči uživatele nainstalovat rozšíření přístupového panelu. Toto rozšíření se stáhne automaticky, když uživatel vybere aplikaci, která je nakonfigurovaná pro jednotné přihlašování založené na heslech.

V případě jednotného přihlašování založeného na heslech můžou být prohlížeče koncového uživatele:

-   Internet Explorer 8, 9, 10, 11 – v systému Windows 7 nebo novějším

-   Microsoft Edge ve Windows 10 výročí Edition nebo novějších verzích 

-   Chrome – v systému Windows 7 nebo novějším a na MacOS X nebo novějším

-   Firefox 26,0 nebo novější – v systému Windows XP SP2 nebo novějším a v Mac OS X 10,6 nebo novějším


## <a name="problems-with-the-users-account"></a>Problémy s uživatelským účtem

Přístup k přístupovému panelu může být zablokován z důvodu problému s uživatelským účtem. Níže jsou uvedeny některé způsoby, jak můžete řešit problémy s uživateli a jejich nastavení účtu:

-   [Ověřte, zda uživatelský účet existuje v Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Zkontroluje stav účtu uživatele.](#check-a-users-account-status)

-   [Resetování hesla uživatele](#reset-a-users-password)

-   [Povolení samoobslužného resetování hesel](#enable-self-service-password-reset)

-   [Zkontroluje stav vícefaktorového ověřování uživatele.](#check-a-users-multi-factor-authentication-status)

-   [Ověřit kontaktní údaje pro ověření uživatele](#check-a-users-authentication-contact-info)

-   [Ověřit členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Ověření licencí přiřazených uživateli](#check-a-users-assigned-licenses)

-   [Přiřazení licence uživateli](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Ověřte, zda uživatelský účet existuje v Azure Active Directory

Pokud chcete zjistit, jestli je uživatelský účet přítomen, postupujte takto:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  Zkontrolujte vlastnosti objektu uživatele, abyste měli jistotu, že vypadají podle očekávání a že nechybí žádná data.

### <a name="check-a-users-account-status"></a>Zkontroluje stav účtu uživatele.

Chcete-li zjistit stav účtu uživatele, postupujte podle následujících kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  klikněte na **profil**.

8.  V části **Nastavení** zajistěte, aby se **blokování přihlášení** nastavilo na **ne**.

### <a name="reset-a-users-password"></a>Resetování hesla uživatele

Pokud chcete resetovat heslo uživatele, postupujte podle těchto kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  klikněte na tlačítko **resetovat heslo** v horní části podokna uživatele.

8.  klikněte na tlačítko **resetovat heslo** v podokně pro **resetování hesla** , které se zobrazí.

9.  Zkopírujte **dočasné heslo** nebo **Zadejte nové heslo** pro uživatele.

10. Sdělte tomuto uživateli nové heslo. při příštím přihlášení do Azure Active Directory je nutné toto heslo změnit.

### <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Pokud chcete povolit samoobslužné resetování hesla, postupujte podle těchto kroků nasazení:

-   [Povolit uživatelům resetovat hesla Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Povolit uživatelům resetovat nebo změnit místní hesla služby Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Zkontroluje stav vícefaktorového ověřování uživatele.

Chcete-li zjistit stav vícefaktorového ověřování uživatele, postupujte takto:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce klikněte na **Uživatelé a skupiny** .

5. klikněte na **Všichni uživatelé**.

6. v horní části podokna klikněte na tlačítko **Multi-Factor Authentication** .

7. Po načtení **portálu pro správu Multi-Factor Authentication** se ujistěte, že jste na kartě **Uživatelé** .

8. Vyhledejte uživatele v seznamu uživatelů hledáním, filtrováním nebo řazením.

9. Vyberte uživatele ze seznamu uživatelů a podle potřeby povolte, **zakažte** **nebo** **vypněte**službu Multi-Factor Authentication.

   >[!NOTE]
   >Pokud je uživatel v **vykonatelném** stavu, můžete ho nastavit tak, aby se dočasně **zakázal** , aby se mohl vrátit ke svému účtu. Až budou zpátky, můžete změnit jejich stav na **povoleno** , aby bylo možné znovu zaregistrovat své kontaktní údaje při příštím přihlášení. Případně můžete postupovat podle pokynů v [kontaktních informacích k ověření uživatele](#check-a-users-authentication-contact-info) a ověřit nebo nastavit tato data.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Ověřit kontaktní údaje pro ověření uživatele

Pokud chcete zjistit kontaktní údaje pro ověření uživatele, které se používají pro službu Multi-Factor Authentication, podmíněný přístup, ochranu identity a resetování hesel, postupujte podle těchto kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  klikněte na **profil**.

8.  Přejděte dolů na **kontaktní údaje pro ověření**.

9.  **Zkontrolujte** data zaregistrovaná pro uživatele a podle potřeby je aktualizujte.

### <a name="check-a-users-group-memberships"></a>Ověřit členství uživatele ve skupinách

Pokud chcete ověřit členství uživatele ve skupině, postupujte podle těchto kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  Kliknutím na **skupiny** zobrazíte skupiny, kterých je uživatel členem.

### <a name="check-a-users-assigned-licenses"></a>Ověření licencí přiřazených uživateli

Pokud chcete ověřit přiřazené licence uživatele, postupujte podle těchto kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  Kliknutím na **licence** zobrazíte licence, které uživatel aktuálně přiřadil.

### <a name="assign-a-user-a-license"></a>Přiřazení licence uživateli 

K přiřazení licence uživateli použijte následující postup:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  Kliknutím na **licence** zobrazíte licence, které uživatel aktuálně přiřadil.

8.  klikněte na tlačítko **přiřadit** .

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelné** kliknutím na položku **možnosti přiřazení** podrobně přiřaďte produkty. Po dokončení klikněte na **OK** .

11. Pro přiřazení těchto licencí tomuto uživateli klikněte na tlačítko **přiřadit** .

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Pokud tyto kroky pro řešení potíží problém nevyřeší

Otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (uživatelská e-mailová adresa)

-   ID tenanta

-   Typ prohlížeče

-   Časové pásmo a čas/časový rámec při výskytu chyby

-   Fiddler trasování

## <a name="next-steps"></a>Další kroky
[Zajištění jednotného přihlašování k aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)
