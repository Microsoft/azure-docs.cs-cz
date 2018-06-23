---
title: Potíže s přihlášením k webu přístup panely | Microsoft Docs
description: Pokyny k odstraňování problémů, které můžete narazit při pokusu o přihlášení k použití na přístupovém panelu
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
ms.openlocfilehash: 84df32000892274c2b77b0bf5ce286bb94171494
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332571"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Potíže s přihlášením k webu přístup k panelu

Přístupový Panel je webový portál, který uživatel, který má pracovní nebo školní účet ve službě Azure Active Directory (Azure AD) umožňuje zobrazovat a spouštět cloudové aplikace, které je správce Azure AD udělil přístup. Uživatel, který má edice Azure AD můžete také skupiny samoobslužné služby a možnosti správy aplikace prostřednictvím na přístupovém panelu. Přístupový Panel je nezávislý na portálu Azure a nevyžaduje, aby uživatelé měli předplatné Azure.

Uživatele můžete přihlásit k přístupovému panelu případě, že mají pracovní nebo školní účet ve službě Azure AD.

-   Uživatelé se můžou ověřovat službou Azure AD přímo.

-   Uživatelé se můžou ověřovat pomocí služby Active Directory Federation Services (AD FS).

-   Uživatelé se můžou ověřovat pomocí služby Windows Server Active Directory.

Pokud je uživatel má předplatné Azure nebo Office 365 a používá portál Azure nebo aplikace Office 365, budou moci bez problémů používat na přístupovém panelu bez nutnosti se znovu přihlásit. Neověřené uživatele vyzváni k přihlášení pomocí uživatelského jména a hesla pro svůj účet ve službě Azure AD. Pokud organizace nemá nakonfigurovali federaci, zadejte uživatelské jméno je dostačující.

## <a name="general-issues-to-check-first"></a>Běžné problémy a proveďte nejprve kontrolu 

-   Ujistěte se, že uživatel je přihlášení k **opravte adresu URL**: <https://myapps.microsoft.com>

-   Zajistěte, aby prohlížeče uživatele přidala adresu URL na jeho **Důvěryhodné servery**

-   Zkontrolujte, zda je účet uživatele **povoleno** pro přihlášení.

-   Zkontrolujte, zda je účet uživatele **není uzamčen.**

-   Zkontrolujte, že uživatele **není platnost nebo zapomenete heslo.**

-   Zajistěte, aby **Multi-Factor Authentication** neblokuje přístup uživatelů.

-   Zajistěte, aby **zásady podmíněného přístupu** nebo **Identity Protection** zásad neblokuje přístup uživatelů.

-   Ujistěte se, že uživatele **ověřování kontaktní údaje** aktuální povolit Multi-Factor Authentication nebo podmíněného přístupu zásad, která budou vynucena.

-   Zkontrolujte, zda také zkuste vymazání souborů cookie v prohlížeči a zkusit se přihlásit znovu.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Splňuje požadavky na prohlížeč pro přístup k panelu

Přístupový Panel vyžaduje prohlížeč, který podporuje jazyk JavaScript a aktivoval šablon stylů CSS. Pokud chcete používat založené na heslech jednotné přihlašování (SSO) na přístupovém panelu, musí být nainstalované rozšíření přístupového panelu v prohlížeče uživatele. Toto rozšíření je stažen automaticky, když uživatel vybere aplikaci, která je nakonfigurována pro jednotné přihlašování založené na heslech.

Pro jednotné přihlašování založené na heslech může být prohlížeče koncového uživatele:

-   Internet Explorer 8, 9, 10, 11 – v systému Windows 7 nebo novější

-   Hraniční Windows 10 Anniversary Edition nebo novější. 

-   Chrome – V systému Windows 7 nebo novější a v systému MacOS X nebo novější

-   Firefox 26.0 nebo později – do systému Windows XP SP2 nebo novější a v systému Mac OS X 10,6 nebo novější


## <a name="problems-with-the-users-account"></a>Problémy s uživatelského účtu

Přístup k přístupovému panelu můžete blokovaný z důvodu problému s účtem uživatele. Následují některé způsoby řešení problémů a řešení problémů s uživateli a jejich nastavení účtu:

-   [Zkontrolujte, jestli uživatelský účet existuje v Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Zkontrolujte stav účtu uživatele](#check-a-users-account-status)

-   [Resetovat heslo uživatele](#reset-a-users-password)

-   [Povolení samoobslužného resetování hesla](#enable-self-service-password-reset)

-   [Zkontrolujte stav služby Multi-Factor authentication uživatele](#check-a-users-multi-factor-authentication-status)

-   [Zkontrolovat kontaktní informace o ověřování uživatele](#check-a-users-authentication-contact-info)

-   [Zkontrolujte členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Zkontrolujte uživatele přiřazené licence](#check-a-users-assigned-licenses)

-   [Přiřazení licence uživatele](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Zkontrolujte, jestli uživatelský účet existuje v Azure Active Directory

Pokud chcete zkontrolovat, zda účet uživatele je přítomen, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na řádek** vybrat.

7.  Zkontrolujte vlastnosti objektu uživatele a ujistěte se, že zobrazují se podle očekávání a nebyla nalezena žádná data.

### <a name="check-a-users-account-status"></a>Zkontrolujte stav účtu uživatele

Pokud chcete zkontrolovat stav účtu uživatele, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na řádek** vybrat.

7.  Klikněte na tlačítko **profil**.

8.  V části **nastavení** Ujistěte se, že **bloku přihlásit** je nastaven na **ne**.

### <a name="reset-a-users-password"></a>Resetovat heslo uživatele

Pokud chcete resetovat heslo uživatele, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na řádek** vybrat.

7.  klikněte **resetovat heslo** tlačítka v horní části podokna uživatele.

8.  klikněte **resetovat heslo** na tlačítko **resetovat heslo** podokně, který se zobrazí.

9.  Kopírování **dočasné heslo** nebo **zadat nové heslo** pro uživatele.

10. Komunikaci tohoto nového hesla pro uživatele, se bude vyžadovat toto heslo změnit při jeho příštím přihlášení k Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Povolit samoobslužné resetování hesla

Pokud chcete povolit samoobslužné resetování hesla, postupujte podle těchto kroků nasazení:

-   [Umožnění resetování hesel služby Azure Active Directory uživatelům](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Povolit uživatelům resetovat nebo změnit své heslo místní služby Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>Zkontrolujte stav služby Multi-Factor authentication uživatele

Pokud chcete zkontrolovat stav uživatele služby Multi-Factor authentication, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  klikněte **Multi-Factor Authentication** tlačítka v horní části podokna.

7.  Jednou **portálu pro správu služby Multi-Factor Authentication** zatížení, ujistěte se, jsou na **uživatelé** kartě.

8.  Vyhledejte uživatele pomocí vyhledávání, filtrování a řazení v seznamu uživatelů.

9.  Vyberte uživatele ze seznamu uživatelů a **povolit**, **zakázat**, nebo **vynutit** služby Multi-Factor authentication podle potřeby.

   >[!NOTE]
   >Pokud je uživatel v **vynucené** stavu, může je nastaven **zakázané** dočasně a dát jim zpět do svého účtu. Jakmile se zpět, můžete změnit svůj stav na **povoleno** znovu a vyžadovat, aby znovu registrovat své kontaktní informace při jeho příštím přihlášení. Alternativně můžete podle kroků v [zkontrolovat kontaktní informace o ověřování uživatele](#check-a-users-authentication-contact-info) ověření nebo pro ně nastavit tato data.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Zkontrolovat kontaktní informace o ověřování uživatele

Pokud chcete zkontrolovat uživatele ověřování kontaktní údaje pro službu Multi-Factor authentication, podmíněného přístupu, ochrany identit a resetování hesla, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na řádek** vybrat.

7.  Klikněte na tlačítko **profil**.

8.  Přejděte dolů k položce **ověřování kontaktní údaje**.

9.  **Zkontrolujte** data zaregistrovaný pro uživatele a aktualizovat podle potřeby.

### <a name="check-a-users-group-memberships"></a>Zkontrolujte členství uživatele ve skupinách

Pokud chcete zkontrolovat členství uživatele ve skupinách, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na řádek** vybrat.

7.  Klikněte na tlačítko **skupiny** zobrazíte, které skupiny uživatel je členem skupiny.

### <a name="check-a-users-assigned-licenses"></a>Zkontrolujte uživatele přiřazené licence

Pokud chcete zkontrolovat uživatele přiřazené licence, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na řádek** vybrat.

7.  Klikněte na tlačítko **licence** zobrazíte, které uživatel aktuálně licence jeho přiřazení.

### <a name="assign-a-user-a-license"></a>Přiřazení licence uživatele 

Chcete-li přiřadit licenci pro uživatele, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na řádek** vybrat.

7.  Klikněte na tlačítko **licence** zobrazíte, které uživatel aktuálně licence jeho přiřazení.

8.  klikněte **přiřadit** tlačítko.

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelné** klikněte na tlačítko **přiřazení možností** položky granularly přiřadit produkty. Klikněte na tlačítko **Ok** po dokončení.

11. Klikněte **přiřadit** tlačítko tyto licence přiřadit tomuto uživateli.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Pokud tyto kroky řešení potíží problém nevyřeší

Otevřete lístek podpory s následujícími informacemi, pokud je k dispozici:

-   ID chyby korelace

-   Hlavní název uživatele (e-mailovou adresu uživatele)

-   ID tenanta

-   Typ prohlížeče

-   Dojde k časové pásmo a čas nebo období při chybě

-   Fiddler trasování

## <a name="next-steps"></a>Další postup
[Zadejte jednotné přihlašování pro vaše aplikace s Proxy aplikace](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
