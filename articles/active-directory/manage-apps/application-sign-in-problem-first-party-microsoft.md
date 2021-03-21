---
title: Problémy s přihlášením k aplikaci Microsoftu | Microsoft Docs
description: Řešení běžných problémů při přihlašování k aplikacím Microsoftu, které používají službu Azure AD (například Microsoft 365).
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79c8d6d072379853d6eca561d372f61dbb8acc8a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99260007"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problémy s přihlášením k aplikaci Microsoftu

Aplikace Microsoftu (jako Exchange, SharePoint, Yammer atd.) se přiřazují a spravují jinak než aplikace třetích stran SaaS nebo jiné aplikace, které integrujete se službou Azure AD pro jednotné přihlašování.

Existují tři hlavní způsoby, jak může uživatel získat přístup k aplikaci publikované v Microsoftu.

-   Pro aplikace v Microsoft 365 nebo jiné placené sady se uživatelům udělí přístup prostřednictvím **přiřazení licence** buď přímo ke svému uživatelskému účtu, nebo prostřednictvím skupiny pomocí možnosti přiřazení licencí na základě skupin.

-   Pro aplikace, které společnost Microsoft nebo třetí strana zveřejňuje volně pro použití kýmkoli, se uživatelům může udělit přístup prostřednictvím **souhlasu uživatele**. To znamená, že se přihlásí k aplikaci pomocí pracovního nebo školního účtu Azure AD a umožní, aby měl přístup k některé omezené sadě dat na svém účtu.

-   Pro aplikace, které společnost Microsoft nebo třetí strana zveřejňuje volně pro použití kýmkoli, můžou být uživatelům udělen i přístup prostřednictvím **souhlasu správce**. To znamená, že správce zjistil, že aplikace může být používána všemi uživateli v organizaci, aby se přihlásila k aplikaci pomocí účtu globálního správce a udělila přístup všem v organizaci.

Pokud chcete tento problém vyřešit, začněte tím, že začnete s [obecnými oblastmi problému s přístupem k aplikacím](#general-problem-areas-with-application-access-to-consider) a pak si přečtěte návod: kroky pro řešení potíží s aplikací Microsoft Access, abyste se dostali k podrobnostem.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Obecné oblasti problému s přístupem k aplikacím, které je potřeba zvážit

V následujícím seznamu jsou uvedeny obecné oblasti problémů, na které můžete přejít, pokud máte představu o tom, kde začít, ale doporučujeme vám přečíst si návod, který vám pomůže rychle se pustit: Návod: kroky pro řešení potíží s aplikací Microsoft Access.

-   [Problémy s uživatelským účtem](#problems-with-the-users-account)

-   [Problémy se skupinami](#problems-with-groups)

-   [Problémy se zásadami podmíněného přístupu](#problems-with-conditional-access-policies)

-   [Problémy s souhlasem aplikace](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Postup odstraňování potíží s aplikací Microsoft Access

Tady jsou některé běžné problémy, ke kterým lidé běžet, když se jejich uživatelé nebudou moct přihlašovat k aplikaci Microsoftu.

- Obecné problémy k první kontrole

  * Ujistěte se, že se uživatel přihlašuje ke **správné adrese URL** , a ne k místní adrese URL aplikace.

  * Ujistěte se, že účet uživatele není **uzamčený.**

  * Ujistěte se, že **uživatelský účet existuje** v Azure Active Directory. [Ověřte, zda uživatelský účet existuje v Azure Active Directory](#problems-with-the-users-account)

  * Ujistěte se, že je **povolený** účet uživatele pro přihlášení. [Zkontroluje stav účtu uživatele](#problems-with-the-users-account) .

  * Ujistěte se, že **heslo uživatele není prošlé nebo zapomenuté.** [Resetování hesla uživatele](#reset-a-users-password) nebo [Povolení samoobslužného resetování hesla](../authentication/tutorial-enable-sspr.md)

  * Ujistěte se, že **Multi-Factor Authentication** neblokuje přístup uživatelů. [Kontrolovat stav vícefaktorového ověřování uživatele](#check-a-users-multi-factor-authentication-status) nebo [zjistit kontaktní údaje pro ověření uživatele](#check-a-users-authentication-contact-info)

  * Zajistěte, aby **zásady podmíněného přístupu** nebo zásady **ochrany identit** neblokovaly přístup uživatelů. [Ověřte konkrétní zásady podmíněného přístupu](#problems-with-conditional-access-policies) nebo [Ověřte zásadu podmíněného přístupu konkrétní aplikace](#check-a-specific-applications-conditional-access-policy) nebo [zakažte konkrétní zásadu podmíněného](#disable-a-specific-conditional-access-policy) přístupu.

  * Ujistěte se, že **kontaktní údaje pro ověření** uživatele jsou aktuální, aby bylo možné vyhovět Multi-Factor Authentication nebo zásadám podmíněného přístupu. [Kontrolovat stav vícefaktorového ověřování uživatele](#check-a-users-multi-factor-authentication-status) nebo [zjistit kontaktní údaje pro ověření uživatele](#check-a-users-authentication-contact-info)

- Pro aplikace **Microsoftu** **, které vyžadují licenci** (například Office 365), zde najdete některé konkrétní problémy, které se kontrolují, když jste si vyřadíte Obecné problémy výše:

  * Ujistěte se, že uživatel nebo má **přiřazenou licenci.** [Ověření licencí přiřazených uživateli](#check-a-users-assigned-licenses) nebo [ověření licencí přidělených skupině](#check-a-groups-assigned-licenses)

  * Pokud je licence **přiřazena ke** **statické skupině**, ujistěte se, že **je uživatel členem** této skupiny. [Ověřit členství uživatele ve skupinách](#check-a-users-group-memberships)

  * Pokud je licence **přiřazená k** **dynamické skupině**, ujistěte se, že **je správně nastavené pravidlo dynamické skupiny**. [Zkontroluje kritéria členství v dynamické skupině.](#check-a-dynamic-groups-membership-criteria)

  * Pokud je licence **přiřazena** **dynamické skupině**, ujistěte se, že dynamická skupina **dokončila zpracování** členství a že **uživatel je členem** (může to trvat delší dobu). [Ověřit členství uživatele ve skupinách](#check-a-users-group-memberships)

  *  Jakmile se zajistěte, aby byla licence přiřazena, zajistěte, aby licence **nevypršela**.

  *  Ujistěte se, že je licence určena **pro aplikaci** , ke které přistupuje.

- Pro aplikace **Microsoftu** **, které nevyžadují licenci**, je tady několik věcí, které byste měli kontrolovat:

  * Pokud aplikace požaduje oprávnění na **úrovni uživatele** (například "přístup k poštovní schránce uživatele"), ujistěte se, že se uživatel přihlásil k aplikaci a provedl **operaci souhlasu na úrovni uživatele** , která aplikaci umožní přístup k datům.

  * Pokud aplikace požaduje **oprávnění na úrovni správce** (například přístup ke všem poštovním schránkám uživatelů), ujistěte se, že globální správce provedl **jménem všech uživatelů v organizaci operaci souhlasu na úrovni správce** .

## <a name="problems-with-the-users-account"></a>Problémy s uživatelským účtem

Přístup k aplikaci lze zablokovat kvůli problému s uživatelem přiřazeným k aplikaci. Níže jsou uvedeny některé způsoby, jak můžete řešit problémy s uživateli a jejich nastavení účtu:

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

10. Sdělit tomuto uživateli nové heslo, musí si toto heslo během příštího přihlašování k Azure Active Directory změnit.

### <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Pokud chcete povolit samoobslužné resetování hesla, postupujte podle následujících kroků nasazení:

-   [Povolit uživatelům resetovat hesla Azure Active Directory](../authentication/tutorial-enable-sspr.md)

-   [Povolit uživatelům resetovat nebo změnit místní hesla služby Active Directory](../authentication/tutorial-enable-sspr.md)

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

9. Vyberte uživatele ze seznamu uživatelů a podle potřeby povolte, **zakažte** **nebo** **vypněte** službu Multi-Factor Authentication.

   * **Poznámka**: Pokud je uživatel ve stavu **vykonatelné** , můžete ho nastavit tak, aby se dočasně **zakázal** , aby se mohl vrátit ke svému účtu. Jakmile se znovu vrátí, můžete změnit jejich stav na **povoleno** , aby bylo možné znovu zaregistrovat své kontaktní údaje při příštím přihlášení. Případně můžete postupovat podle pokynů v [kontaktních informacích k ověření uživatele](#check-a-users-authentication-contact-info) a ověřit nebo nastavit tato data.

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

## <a name="problems-with-groups"></a>Problémy se skupinami

Přístup k aplikaci lze zablokovat kvůli problému se skupinou, která je přiřazena k aplikaci. Níže jsou uvedené některé způsoby, jak můžete řešit problémy se skupinami a členstvím ve skupinách:

-   [Ověřit členství ve skupině](#check-a-groups-membership)

-   [Zkontroluje kritéria členství v dynamické skupině.](#check-a-dynamic-groups-membership-criteria)

-   [Ověření licencí přidělených skupině](#check-a-groups-assigned-licenses)

-   [Znovu zpracovat licence skupiny](#reprocess-a-groups-licenses)

-   [Přiřazení licence skupiny a](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Ověřit členství ve skupině

Chcete-li ověřit členství ve skupině, postupujte podle následujících kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **všechny skupiny**.

6.  **Vyhledejte** skupinu, kterou vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  Kliknutím na **Členové** zkontrolujte seznam uživatelů přiřazených k této skupině.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Zkontroluje kritéria členství v dynamické skupině. 

Pokud chcete ověřit kritéria členství v dynamické skupině, postupujte podle těchto kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **všechny skupiny**.

6.  **Vyhledejte** skupinu, kterou vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  klikněte na **dynamická pravidla členství.**

8.  Zkontrolujte **jednoduché** nebo **Rozšířené** pravidlo definované pro tuto skupinu a ujistěte se, že uživatel, který chcete být členem této skupiny, splňuje tato kritéria.

### <a name="check-a-groups-assigned-licenses"></a>Ověření licencí přidělených skupině

Chcete-li ověřit přiřazené licence skupiny, postupujte podle těchto kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **všechny skupiny**.

6.  **Vyhledejte** skupinu, kterou vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  Kliknutím na **licence** zobrazíte licence, které skupina aktuálně přiřadila.

### <a name="reprocess-a-groups-licenses"></a>Znovu zpracovat licence skupiny

Chcete-li znovu zpracovat přiřazené licence skupiny, postupujte podle těchto kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce klikněte na **Uživatelé a skupiny** .

5. klikněte na **všechny skupiny**.

6. **Vyhledejte** skupinu, kterou vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7. Kliknutím na **licence** zobrazíte licence, které skupina aktuálně přiřadila.

8. klikněte na tlačítko znovu **zpracovat** a ujistěte se, že licence přiřazené členům této skupiny jsou aktuální. V závislosti na velikosti a složitosti skupiny to může trvat dlouhou dobu.

   >[!NOTE]
   >Chcete-li to provést rychleji, zvažte možnost dočasného přiřazení licence přímo uživateli. [Přiřaďte uživateli licenci](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Přiřazení licence skupiny a

Pokud chcete přiřadit licenci ke skupině, postupujte podle těchto kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce klikněte na **Uživatelé a skupiny** .

5. klikněte na **všechny skupiny**.

6. **Vyhledejte** skupinu, kterou vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7. Kliknutím na **licence** zobrazíte licence, které skupina aktuálně přiřadila.

8. klikněte na tlačítko **přiřadit** .

9. Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelné** kliknutím na položku **možnosti přiřazení** podrobně přiřaďte produkty. Po dokončení klikněte na **OK** .

11. Kliknutím na tlačítko **přiřadit** jim přiřadíte tyto licence k této skupině. V závislosti na velikosti a složitosti skupiny to může trvat dlouhou dobu.

    >[!NOTE]
    >Chcete-li to provést rychleji, zvažte možnost dočasného přiřazení licence přímo uživateli. [Přiřaďte uživateli licenci](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problémy se zásadami podmíněného přístupu

### <a name="check-a-specific-conditional-access-policy"></a>Ověřte konkrétní zásady podmíněného přístupu.

Kontrola nebo ověření jedné zásady podmíněného přístupu:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce klikněte na **podnikové aplikace** .

5. klikněte na položku navigace **podmíněného přístupu** .

6. klikněte na zásadu, které vás zajímá při kontrole.

7. Zkontrolujte, že neexistují žádné konkrétní podmínky, přiřazení nebo jiná nastavení, která by mohla blokovat přístup uživatelů.

   >[!NOTE]
   >Možná budete chtít dočasně zakázat tuto zásadu, aby nedošlo k ovlivnění přihlášení. Uděláte to tak, že nastavíte přepínač **Povolit zásadu** na **ne** a kliknete na tlačítko **Uložit** .
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Zkontroluje zásadu podmíněného přístupu konkrétní aplikace.

Chcete-li zkontrolovat nebo ověřit aktuálně nakonfigurovanou zásadu podmíněného přístupu jedné aplikace:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **podnikové aplikace** .

5.  klikněte na **všechny aplikace**.

6.  Vyhledejte aplikaci, kterou vás zajímá, nebo se uživatel pokouší přihlásit pomocí zobrazovaného názvu aplikace nebo ID aplikace.

     >[!NOTE]
     >Pokud nevidíte aplikaci, kterou hledáte, klikněte na tlačítko **Filtr** a rozbalte rozsah seznamu do **všech aplikací**. Chcete-li zobrazit více sloupců, klikněte na tlačítko **sloupce** a přidejte další podrobnosti pro aplikace.
     >
     >

7.  klikněte na položku navigace **podmíněného přístupu** .

8.  klikněte na zásadu, které vás zajímá při kontrole.

9.  Zkontrolujte, zda nejsou k dispozici žádné konkrétní podmínky, přiřazení nebo jiná nastavení, která mohou blokovat přístup uživatelů.

     >[!NOTE]
     >Možná budete chtít dočasně zakázat tuto zásadu, aby nedošlo k ovlivnění přihlášení. Uděláte to tak, že nastavíte přepínač **Povolit zásadu** na **ne** a kliknete na tlačítko **Uložit** .
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Zakázání specifických zásad podmíněného přístupu

Kontrola nebo ověření jedné zásady podmíněného přístupu:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **podnikové aplikace** .

5.  klikněte na položku navigace **podmíněného přístupu** .

6.  klikněte na zásadu, které vás zajímá při kontrole.

7.  Zakažte zásadu tak, že nastavíte přepínač **Povolit zásadu** na **ne** a kliknete na tlačítko **Uložit** .

## <a name="problems-with-application-consent"></a>Problémy s souhlasem aplikace

Přístup k aplikaci je možné zablokovat, protože nedošlo k operaci souhlasu se správnými oprávněními. Tady je několik způsobů, jak můžete řešit problémy s vydáním aplikace a řešit je:

-   [Provedení operace souhlasu na úrovni uživatele](#perform-a-user-level-consent-operation)

-   [Provedení operace souhlasu na úrovni správce u libovolné aplikace](#perform-administrator-level-consent-operation-for-any-application)

-   [Provedení souhlasu na úrovni správce pro aplikaci s jedním klientem](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Provádění souhlasu se správcem na úrovni správce pro aplikaci s více klienty](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Provedení operace souhlasu na úrovni uživatele

-   U jakékoli otevřené aplikace s podporou připojení, která žádá o oprávnění, přejde na přihlašovací obrazovce aplikace souhlas na úrovni uživatele k aplikaci pro přihlášeného uživatele.

-   Pokud to chcete provést programově, přečtěte si téma [vyžádání souhlasu jednotlivých uživatelů](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Provedení operace souhlasu na úrovni správce u libovolné aplikace

-   **Jenom pro aplikace vyvíjené pomocí aplikačního modelu v1** můžete tomuto souhlasu s oprávněními na úrovni správce přinutit tak, že na konec adresy URL pro přihlášení k aplikaci přidáte "**? prompt = \_ souhlasu správce**".

-   U **všech aplikací vyvinutých pomocí aplikačního modelu v2** můžete vyhovět tomuto souhlasu na úrovni správce, a to podle pokynů v části **vyžádání oprávnění z Správce adresáře** v tématu [použití koncového bodu souhlasu správce](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Provedení souhlasu na úrovni správce pro aplikaci s jedním klientem

-   Pro **aplikace pro jednoho tenanta** , které požadují oprávnění (například ty, které vyvíjíte nebo vlastníte ve vaší organizaci), můžete provádět operace **souhlasu s správní úrovní** jménem všech uživatelů přihlášením jako globální správce a kliknutím na tlačítko **udělit oprávnění** v horní části **registru aplikace – &gt; všechny aplikace – &gt; Vyberte podokno &gt; oprávnění požadovaná aplikací** .

-   U **všech aplikací vyvinutých pomocí aplikačního modelu v1 nebo v2** můžete vyhovět tomuto souhlasu na úrovni správce, a to podle pokynů v části **vyžádání oprávnění z Správce adresáře** v tématu [použití koncového bodu souhlasu správce](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Provádění souhlasu se správcem na úrovni správce pro aplikaci s více klienty

-   Pro **víceklientské aplikace** , které vyžadují oprávnění (například aplikace, kterou třetí strana nebo společnost Microsoft), můžete provést operaci **souhlasu s správní úrovní** . Přihlaste se jako globální správce a klikněte na tlačítko **udělit oprávnění** v části **podnikové aplikace – &gt; všechny aplikace – &gt; Vyberte podokno &gt; oprávnění aplikace** (brzy bude k dispozici).

-   Tento souhlas na úrovni správce můžete taky vyžádat podle pokynů v části **vyžádání oprávnění z Správce adresáře** v tématu [použití koncového bodu souhlasu správce](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Další kroky
[Použití koncového bodu souhlasu správce](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)