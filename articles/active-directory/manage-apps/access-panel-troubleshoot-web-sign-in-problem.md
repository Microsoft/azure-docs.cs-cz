---
title: Problém s přihlášením na web přístupového panelu | Dokumenty společnosti Microsoft
description: Pokyny k řešení problémů, se kterými se můžete setkat při pokusu o přihlášení k použití přístupového panelu
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c6a9c3f26c8939176197a2ecf2fcd6026e9928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784311"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problém s přihlášením na web přístupového panelu

Přístupový panel je webový portál, který umožňuje uživateli, který má pracovní nebo školní účet ve službě Azure Active Directory (Azure AD) zobrazit a spustit cloudové aplikace, které jim správce Azure AD udělil přístup. Uživatel, který má edice Azure AD, může také používat samoobslužné možnosti správy skupin a aplikací prostřednictvím přístupového panelu. Přístupový panel je oddělený od portálu Azure a nevyžaduje, aby uživatelé měli předplatné Azure.

Uživatelé se můžou přihlásit k přístupovému panelu, pokud mají pracovní nebo školní účet ve službě Azure AD.

-   Uživatelé mohou být ověřeni přímo službou Azure AD.

-   Uživatelé mohou být ověřováni pomocí služby AD FS (AD FS).

-   Uživatelé mohou být ověřeni službou Active Directory systému Windows Server.

Pokud má uživatel předplatné pro Azure nebo Office 365 a používá portál Azure nebo aplikaci Office 365, bude moct přístupový panel používat bez problémů, aniž by se musel znovu přihlašovat. Uživatelé, kteří nejsou ověřeni, jsou vyzváni k přihlášení pomocí uživatelského jména a hesla pro svůj účet ve službě Azure AD. Pokud organizace nakonfigurovala federaci, stačí zadat uživatelské jméno.

## <a name="general-issues-to-check-first"></a>Obecné problémy, které je třeba nejprve zkontrolovat 

-   Ujistěte se, že se uživatel přihlašuje ke **správné adrese URL**:<https://myapps.microsoft.com>

-   Zkontrolujte, zda prohlížeč uživatele přidal adresu URL na **důvěryhodné servery.**

-   Ujistěte se, že uživatelský účet je **povolen** pro přihlášení.

-   Ujistěte se, že uživatelský účet **není uzamčen.**

-   Ujistěte se, že vypršela platnost hesla uživatele nebo zda **není zapomenuto.**

-   Ujistěte se, že **vícefaktorové ověřování** neblokuje přístup uživatelů.

-   Ujistěte se, že **zásady podmíněného přístupu** nebo **zásady ochrany identity** neblokují přístup uživatelů.

-   Ujistěte se, že **kontaktní údaje** uživatele ověřování jsou aktuální, aby bylo možné vynucovat zásady vícefaktorového ověřování nebo podmíněného přístupu.

-   Zkuste také vymazat soubory cookie prohlížeče a znovu se přihlásit.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Splnění požadavků prohlížeče na přístupovém panelu

Přístupový panel vyžaduje prohlížeč, který podporuje JavaScript a má povoleno CSS. Chcete-li na přístupovém panelu používat jednotné přihlašování založené na heslech (SSO), musí být rozšíření přístupového panelu nainstalováno v prohlížeči uživatele. Toto rozšíření se stáhne automaticky, když uživatel vybere aplikaci, která je nakonfigurována pro zabezpečení založené na heslech.

Pro heslo založené na přisazože, prohlížeče koncového uživatele může být:

-   Internet Explorer 8, 9, 10, 11 -- ve Windows 7 nebo novějším

-   Microsoft Edge ve Windows 10 Anniversary Edition nebo novějším 

-   Chrome – ve Windows 7 nebo novějším a v MacOS X nebo novějším

-   Firefox 26.0 nebo novější – v systému Windows XP SP2 nebo novějším a v systému Mac OS X 10.6 nebo novějším


## <a name="problems-with-the-users-account"></a>Problémy s uživatelským účtem

Přístup k přístupovému panelu může být zablokován kvůli problému s uživatelským účtem. Níže jsou uvedeny některé způsoby, jak můžete řešit a řešit problémy s uživateli a jejich nastavení účtu:

-   [Kontrola, zda ve službě Azure Active Directory existuje uživatelský účet](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Kontrola stavu účtu uživatele](#check-a-users-account-status)

-   [Resetování hesla uživatele](#reset-a-users-password)

-   [Povolení samoobslužného resetování hesla](#enable-self-service-password-reset)

-   [Kontrola stavu vícefaktorového ověřování uživatele](#check-a-users-multi-factor-authentication-status)

-   [Kontrola kontaktních údajů uživatele pro ověřování](#check-a-users-authentication-contact-info)

-   [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Kontrola přiřazených licencí uživatele](#check-a-users-assigned-licenses)

-   [Přiřazení licence uživateli](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Kontrola, zda ve službě Azure Active Directory existuje uživatelský účet

Chcete-li zkontrolovat, zda je k dispozici uživatelský účet, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Zkontrolujte vlastnosti objektu uživatele a ujistěte se, že vypadají podle očekávání a chybí žádná data.

### <a name="check-a-users-account-status"></a>Kontrola stavu účtu uživatele

Chcete-li zkontrolovat stav účtu uživatele, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  klepněte na **položku Profil**.

8.  V části **Nastavení** zkontrolujte, zda je **přihlášení bloku** nastaveno na **ne**.

### <a name="reset-a-users-password"></a>Resetování hesla uživatele

Chcete-li obnovit heslo uživatele, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  klikněte na tlačítko **Obnovit heslo** v horní části podokna uživatele.

8.  Klepněte na tlačítko **Obnovit heslo** v podokně **Obnovit heslo,** které se zobrazí.

9.  Zkopírujte **dočasné heslo** nebo **zadejte nové heslo** pro uživatele.

10. Sdělte toto nové heslo uživateli, bude nutné změnit toto heslo během jejich dalšího přihlášení do služby Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Chcete-li povolit samoobslužné resetování hesla, postupujte takto:

-   [Povolení uživatelům resetovat hesla služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Povolení uživatelům resetovat nebo změnit místní hesla služby Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kontrola stavu vícefaktorového ověřování uživatele

Chcete-li zkontrolovat stav vícefaktorového ověřování uživatele, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Uživatelé a skupiny.**

5. klepněte na položku **Všichni uživatelé**.

6. klikněte na tlačítko **Vícefaktorové ověřování** v horní části podokna.

7. Jakmile se **portál pro správu vícefaktorového ověřování** načte, ujistěte se, že jste na kartě **Uživatelé.**

8. Vyhledejte uživatele v seznamu uživatelů vyhledáváním, filtrováním nebo řazením.

9. Vyberte uživatele ze seznamu uživatelů a **povolit**, **zakázat**nebo **vynutit** vícefaktorové ověřování podle potřeby.

   >[!NOTE]
   >Pokud je uživatel v **vynuceném** stavu, můžete jej nastavit na **zakázáno** dočasně, aby se vrátil do svého účtu. Jakmile jsou zpět, pak můžete změnit jejich stav **povoleno** znovu požadovat, aby znovu zaregistrovat své kontaktní informace během jejich další přihlášení. Případně můžete podle kroků v části [Kontrola ověřovacích kontaktních údajů uživatele](#check-a-users-authentication-contact-info) ověřit nebo nastavit tato data pro ně.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Kontrola kontaktních údajů uživatele pro ověřování

Chcete-li zkontrolovat kontaktní údaje uživatele pro ověřování pomocí vícefaktorového ověřování, podmíněného přístupu, ochrany identity a resetování hesla, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  klepněte na **položku Profil**.

8.  Posuňte se dolů na **Informace o autentizaci**.

9.  **Zkontrolujte** data registrovaná pro uživatele a podle potřeby aktualizujte.

### <a name="check-a-users-group-memberships"></a>Kontrola členství uživatele ve skupinách

Chcete-li zkontrolovat členství uživatele ve skupinách, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Kliknutím na **Skupiny** zobrazíte, ve kterých skupinách je uživatel členem.

### <a name="check-a-users-assigned-licenses"></a>Kontrola přiřazených licencí uživatele

Chcete-li zkontrolovat přiřazené licence uživatele, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Chcete-li zjistit, které licence uživatel aktuálně přiřadil, klepněte na **položku Licence.**

### <a name="assign-a-user-a-license"></a>Přiřazení licence uživateli 

Chcete-li uživateli přiřadit licenci, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Chcete-li zjistit, které licence uživatel aktuálně přiřadil, klepněte na **položku Licence.**

8.  klikněte na tlačítko **Přiřadit.**

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelně** klepněte na položku **možností přiřazení,** chcete-li granularly přiřadit produkty. Po dokončení klepněte na **tlačítko Ok.**

11. Klepnutím na tlačítko **Přiřadit** přiřadíte tyto licence tomuto uživateli.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Pokud tyto kroky řešení potíží problém nevyřeší

otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (e-mailová adresa uživatele)

-   ID tenanta

-   Typ prohlížeče

-   Časové pásmo a časový rámec během chyby

-   Fiddler stopy

## <a name="next-steps"></a>Další kroky
[Poskytněte svým aplikacím jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)
