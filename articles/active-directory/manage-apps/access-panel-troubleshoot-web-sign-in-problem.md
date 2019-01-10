---
title: Potížím při přihlašování k webu přístupového panelu | Dokumentace Microsoftu
description: Pokyny k odstraňování problémů, na které můžete narazit při pokusu o přihlášení k použití na přístupovém panelu
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviwer: japere,asteen
ms.openlocfilehash: d2670d8f018fc26b7017fba40b5aff4310900dab
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188687"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Potížím při přihlašování k webu přístupového panelu

Přístupový Panel je webový portál, který uživatel, který má pracovní nebo školní účet v Azure Active Directory (Azure AD) umožňuje zobrazovat a spouštět cloudové aplikace, které jim správce Azure AD udělil přístup k. Uživatel, který má edice Azure AD můžete použít rovněž samoobslužné skupiny a možnosti správy aplikací přes přístupový Panel. Přístupový Panel je nezávislý na webu Azure Portal a nevyžaduje, aby uživatelé měli předplatné Azure.

Uživatelé můžou přihlásit k přístupovému panelu Pokud mají pracovní nebo školní účet ve službě Azure AD.

-   Uživatelům můžete ověřit pomocí služby Azure AD přímo.

-   Uživatelé mohou být ověřeni pomocí služby Active Directory Federation Services (AD FS).

-   Uživatelé mohou být ověřeni pomocí služby Windows Server Active Directory.

Pokud uživatel má předplatné Azure nebo Office 365 a používá na webu Azure portal nebo aplikace Office 365, uvidí na přístupovém panelu použít bez problémů bez nutnosti znovu přihlásit. Uživatelé, kteří nejsou ověřené se výzva k přihlášení pomocí uživatelského jména a hesla pro svůj účet ve službě Azure AD. Pokud organizace má nakonfigurovanou federace, zadáním uživatelského jména je dostačující.

## <a name="general-issues-to-check-first"></a>Obecné problémy a proveďte nejprve kontrolu 

-   Ujistěte se, že uživatel přihlašuje k **opravit URL**: <https://myapps.microsoft.com>

-   Ujistěte se, že v prohlížeči uživatele byla přidána adresa URL pro její **Důvěryhodné servery**

-   Ujistěte se, že účet uživatele je **povolené** pro přihlášení.

-   Ujistěte se, že účet uživatele je **není uzamčen.**

-   Ujistěte se, že uživatele **není platnost vypršela nebo zapomněli heslo.**

-   Ujistěte se, že **ověřování službou Multi-Factor Authentication** neblokuje přístup uživatelů.

-   Ujistěte se, že **zásady podmíněného přístupu** nebo **Identity Protection** zásad neblokuje přístup uživatelů.

-   Ujistěte se, že uživatele **kontaktní údaje pro ověření** je aktuální. pro povolení služby Multi-Factor Authentication nebo podmíněného přístupu zásady vynucení.

-   Ujistěte se, že k také zkuste vymazání souborů cookie v prohlížeči a pokoušel se přihlásit znovu.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Splňuje požadavky na prohlížeč pro na přístupovém panelu

Přístupový Panel vyžaduje prohlížeč, který podporuje JavaScript a má povolený šablon stylů CSS. Chcete-li použít založené na heslech jednotné přihlašování (SSO) na přístupovém panelu, musí být nainstalovaná rozšíření přístupového panelu v prohlížeči uživatele. Toto rozšíření se automaticky stáhne, když uživatel vybere, který je nakonfigurovaný pro jednotné přihlašování pomocí hesla aplikace.

Pro jednotné přihlašování založené na heslech může být koncového uživatele prohlížeče:

-   Aplikace Internet Explorer 8, 9, 10, 11 – ve Windows 7 nebo novější

-   Microsoft Edge ve Windows 10 Anniversary Edition nebo novější 

-   Chrome – Ve Windows 7 nebo novější a v systému MacOS X nebo novější

-   Firefox 26.0 nebo později – na Windows XP SP2 nebo novější a v systému Mac OS X 10,6 nebo novější


## <a name="problems-with-the-users-account"></a>Problémy s účtem uživatele

Přístup k přístupovému panelu mohou být blokovány z důvodu problému s účtem uživatele. Toto jsou některé způsobů řešení problémů a řešení problémů s uživateli a jejich nastavení účtu:

-   [Zkontrolujte, jestli uživatelský účet existuje v Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Zkontrolujte stav účtu uživatele.](#check-a-users-account-status)

-   [Resetování hesla uživatele](#reset-a-users-password)

-   [Povolení samoobslužného resetování hesla](#enable-self-service-password-reset)

-   [Zkontrolujte stav služby Multi-Factor authentication uživatele.](#check-a-users-multi-factor-authentication-status)

-   [Zkontrolujte kontaktní údaje pro ověření uživatele](#check-a-users-authentication-contact-info)

-   [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Zkontrolujte přiřazené licence pro uživatele](#check-a-users-assigned-licenses)

-   [Přiřadit uživateli licenci](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Zkontrolujte, jestli uživatelský účet existuje v Azure Active Directory

Zkontrolujte, jestli účet uživatele je k dispozici, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Zkontrolujte vlastnosti objektu uživatele, abyste měli jistotu, že vypadají podle očekávání a nebyla nalezena žádná data.

### <a name="check-a-users-account-status"></a>Zkontrolujte stav účtu uživatele.

Pokud chcete zkontrolovat stav účtu uživatele, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **profilu**.

8.  V části **nastavení** Ujistěte se, že **zablokovat přihlášení** je nastavena na **ne**.

### <a name="reset-a-users-password"></a>Resetování hesla uživatele

Pokud chcete resetovat heslo uživatele, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **resetovat heslo** tlačítko v horní části podokna uživatele.

8.  Klikněte na tlačítko **resetovat heslo** tlačítko **resetovat heslo** podokno, které se zobrazí.

9.  Kopírovat **dočasné heslo** nebo **zadejte nové heslo** pro daného uživatele.

10. Komunikaci toto nové heslo pro uživatele, že být požádán o toto heslo změnit při dalším přihlášení ke službě Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Pokud chcete povolit samoobslužné resetování hesla, postupujte podle kroků nasazení:

-   [Povolit uživatelům resetovat svá hesla Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Uživatelům umožnit resetování nebo změnu hesel místní služby Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>Zkontrolujte stav služby Multi-Factor authentication uživatele.

Pokud chcete zkontrolovat stav služby Multi-Factor authentication, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  Klikněte na tlačítko **ověřování službou Multi-Factor Authentication** tlačítko v horní části podokna.

7.  Jednou **portál pro správu služby Multi-Factor Authentication** zatížením, ujistěte se na **uživatelé** kartu.

8.  Vyhledejte uživatele pomocí hledání, filtrování a řazení v seznamu uživatelů.

9.  Vyberte uživatele ze seznamu uživatelů a **povolit**, **zakázat**, nebo **vynutit** ověřování službou Multi-Factor Authentication podle potřeby.

   >[!NOTE]
   >Pokud je uživatel v **vynucené** stavu, může je nastavená na **zakázané** dočasně a informovat je zpátky do svého účtu. Poté, co jsou zpět v, můžete změnit jejich stav na **povoleno** znovu tak, aby vyžadovala je znovu zaregistrovat své kontaktní informace při jeho příštím přihlášení. Alternativně můžete podle kroků v [zkontrolujte kontaktní údaje pro ověření uživatele](#check-a-users-authentication-contact-info) ověření nebo pro ně nastavit tato data.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Zkontrolujte kontaktní údaje pro ověření uživatele

Pokud chcete zkontrolovat kontaktních informací pro uživatele ověření použít vícefaktorové ověřování, podmíněného přístupu, Identity Protection a resetování hesla, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **profilu**.

8.  Přejděte dolů k položce **kontaktní údaje pro ověření**.

9.  **Kontrola** data zaregistrovaný pro uživatele a aktualizovat podle potřeby.

### <a name="check-a-users-group-memberships"></a>Kontrola členství uživatele ve skupinách

Pokud chcete zkontrolovat členství uživatele ve skupinách, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **skupiny** zobrazíte, která seskupuje uživatel je členem skupiny.

### <a name="check-a-users-assigned-licenses"></a>Zkontrolujte přiřazené licence pro uživatele

Pokud chcete zkontrolovat přiřazené licence pro uživatele, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **licence** zobrazíte, která aktuálně licencí uživateli přiřadila.

### <a name="assign-a-user-a-license"></a>Přiřadit uživateli licenci 

Chcete-li přiřadit licenci uživateli, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **licence** zobrazíte, která aktuálně licencí uživateli přiřadila.

8.  Klikněte na tlačítko **přiřadit** tlačítko.

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelné** klikněte na tlačítko **možnosti přiřazení** položku, kterou chcete zásady přiřazovat produktů. Klikněte na tlačítko **Ok** po dokončení.

11. Klikněte na tlačítko **přiřadit** tlačítko tyto licence přiřadit k tomuto uživateli.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Pokud tyto kroky pro řešení potíží problém nevyřešil

vytvořit lístek podpory s použitím následujících informací, pokud je k dispozici:

-   Chyba ID korelace

-   Hlavní název uživatele (uživatel e-mailová adresa)

-   ID tenanta

-   Typ prohlížeče

-   Vyvolá se časové pásmo a čas nebo časový rámec při chybě

-   Trasování Fiddler

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)
