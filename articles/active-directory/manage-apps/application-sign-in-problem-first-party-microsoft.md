---
title: Potíže při přihlašování k aplikaci Microsoftu | Dokumentace Microsoftu
description: S řešením běžných problémů, kterým čelí při přihlašování k používání služby Azure AD (jako je Office 365) Applications Microsoft první strany
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: celested
ms.reviewer: asteen
ms.openlocfilehash: c569a8fa82e00e7a876c80e5ebc127426abfd1dc
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965201"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Potíže při přihlašování k aplikaci Microsoftu

Microsoft Applications (např. Office 365 Exchange, SharePoint, Yammer atd.) jsou přiřazeny a trochu jinak než 3. stran aplikace SaaS nebo k jiným aplikacím, které můžete integrovat s Azure AD pro jednotné přihlašování na spravované.

Existují tři hlavní způsoby, že uživatel může získat přístup k aplikaci publikované společností Microsoft.

-   Pro aplikace v Office 365 nebo jiné placené sad, mají uživatelé udělen přístup prostřednictvím **přiřazení licence** buď přímo do svého uživatelského účtu, nebo prostřednictvím skupiny pomocí našich přiřazení schopností skupinové licence.

-   Pro aplikace, které společnost Microsoft nebo třetích stran publikuje zpřístupněna bezplatně pro může je používat kdokoli, může být uživatelé udělili přístup prostřednictvím **souhlasu uživatele**. This0 znamená, že přihlášení k aplikaci pomocí svého účtu Azure AD práce nebo do školy a mohla mít přístup k některé omezenou sadu dat na svém účtu.

-   Pro aplikace, které společnost Microsoft nebo 3. stran publikuje zpřístupněna bezplatně pro může je používat kdokoli, uživatelé mohou také být udělen přístup prostřednictvím **souhlas správce**. To znamená, že správce určil, že aplikace může je používat kdokoli v organizaci, přihlaste se k aplikaci pomocí účtu globálního správce a udělit přístup všem uživatelům v organizaci.

Váš problém vyřešit, začínat [obecné problémových oblastí pomocí přístupu k aplikacím, které byste měli zvážit](#general-problem-areas-with-application-access-to-consider) a pak si můžete přečíst návodu: Postup řešení potíží s přístupem Microsoft Application ponoříte do detailů.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Obecné problémových oblastí pomocí přístupu k aplikacím, které je třeba zvážit

Následuje seznam obecné problémových oblastí, které můžete zobrazit další podrobnosti Pokud máte představu, kde začít, ale doporučujeme, abyste že si přečíst Průvodce pro rychlé zahájení práce: Průvodce: Postup řešení potíží s přístupem Microsoft Application.

-   [Problémy s účtem uživatele](#problems-with-the-users-account)

-   [Problémy se skupinami](#problems-with-groups)

-   [Problémy se zásadami podmíněného přístupu](#problems-with-conditional-access-policies)

-   [Problémy s souhlasu s aplikací](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Postup řešení potíží s přístupem Microsoft Application

Toto jsou některé běžné problémy, které lidé tyto problémy při jejich uživatelé přihlásit k aplikaci Microsoftu.

-   Obecné problémy a proveďte nejprve kontrolu

  * Ujistěte se, že uživatel přihlašuje k **opravit URL** a ne adresu URL místní aplikace.

  * Ujistěte se, že účet uživatele je **není uzamčen.**

  * Ujistěte se, **uživatelský účet existuje** ve službě Azure Active Directory. [Zkontrolujte, jestli uživatelský účet existuje v Azure Active Directory](#problems-with-the-users-account)

  * Ujistěte se, že účet uživatele je **povolené** pro přihlášení. [Zkontrolujte stav účtu uživatele.](#problems-with-the-users-account)

  * Ujistěte se, že uživatele **není platnost vypršela nebo zapomněli heslo.** [Resetovat heslo uživatele](#reset-a-users-password) nebo [povolit samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * Ujistěte se, že **ověřování službou Multi-Factor Authentication** neblokuje přístup uživatelů. [Zkontrolujte stav služby Multi-Factor authentication](#check-a-users-multi-factor-authentication-status) nebo [zkontrolujte kontaktní údaje pro ověření uživatele](#check-a-users-authentication-contact-info)

   * Ujistěte se, že **zásady podmíněného přístupu** nebo **Identity Protection** zásad neblokuje přístup uživatelů. [Zkontrolujte zásady podmíněného přístupu konkrétní](#problems-with-conditional-access-policies) nebo [zkontrolujte zásady podmíněného přístupu pro konkrétní aplikaci](#check-a-specific-applications-conditional-access-policy) nebo [zakázat zásadu konkrétní podmíněného přístupu](#disable-a-specific-conditional-access-policy)

   * Ujistěte se, že uživatele **kontaktní údaje pro ověření** neustále aktuální, aby ověřování službou Multi-Factor Authentication nebo podmíněného přístupu zásady vynucení. [Zkontrolujte stav služby Multi-Factor authentication](#check-a-users-multi-factor-authentication-status) nebo [zkontrolujte kontaktní údaje pro ověření uživatele](#check-a-users-authentication-contact-info)

-   Pro **Microsoft** **aplikace, které vyžadují licenci** (např. Office 365), tady jsou některé konkrétní problémy, zkontrolujte po nevyloučí výše obecné problémy:

   * Zkontrolujte, že uživatel, nebo má **přiřazenou licenci.** [Zkontrolujte přiřazené licence uživatele](#check-a-users-assigned-licenses) nebo [Zkontrolujte skupiny přiřazené licence](#check-a-groups-assigned-licenses)

   * Pokud je licence **přiřazeno** **statická skupina**, ujistěte se, že **je uživatel členem** této skupiny. [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

   * Pokud je licence **přiřazeno** **dynamická skupina**, ujistěte se, že **dynamická skupina pravidla nastavena správně**. [Zkontrolujte kritéria členství dynamické skupiny](#check-a-dynamic-groups-membership-criteria)

   * Pokud je licence **přiřazená** **dynamická skupina**, ujistěte se, že dynamickou skupinu má **bylo dokončeno zpracování** členství a že **je uživatel členem**  (to může nějakou dobu trvat). [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

   *  Jakmile budete mít jistotu, přiřazené licence, ujistěte se, že je licence **nevypršela**.

   *  Ujistěte se, že je licence **aplikace** přistupují.

-   Pro **Microsoft** **aplikace, které nevyžadují licenci**, tady jsou některé další možnosti ke kontrole:

   * Pokud aplikace požaduje **oprávnění na úrovni uživatele** (například "přístup k poštovní schránky uživatele"), ujistěte se, že uživatel přihlásil k aplikaci a byla provedena **operace vyjádření souhlasu uživatele** umožňuje aplikaci získat přístup k jeho datům.

   * Pokud aplikace požaduje **oprávnění na úrovni správce** (například "přístup k poštovním schránkám všechny uživatele"), ujistěte se, že byla provedena jako globální správce **operace schválením na úrovni správce jménem všech uživatelů** v organizaci.

## <a name="problems-with-the-users-account"></a>Problémy s účtem uživatele

Přístup k aplikaci mohou být blokovány z důvodu problému s uživatelem, který je přiřazen k aplikaci. Toto jsou některé způsobů řešení problémů a řešení problémů s uživateli a jejich nastavení účtu:

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

10. Komunikaci toto nové heslo pro uživatele, že být požádán o toto heslo změnit při příštím přihlášení ve službě Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Pokud chcete povolit samoobslužné resetování hesla, postupujte podle následujících kroků nasazení:

-   [Povolit uživatelům resetovat svá hesla Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Uživatelům umožnit resetování nebo změnu hesel místní služby Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Zkontrolujte stav služby Multi-Factor authentication uživatele.

Pokud chcete zkontrolovat stav služby Multi-Factor authentication, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  Klikněte na tlačítko **ověřování službou Multi-Factor Authentication** tlačítko v horní části podokna.

7.  Jednou **portál pro správu ověřování službou Multi-Factor Authentication** zatížením, ujistěte se na **uživatelé** kartu.

8.  Vyhledejte uživatele pomocí hledání, filtrování a řazení v seznamu uživatelů.

9.  Vyberte uživatele ze seznamu uživatelů a **povolit**, **zakázat**, nebo **vynutit** ověřování službou Multi-Factor Authentication podle potřeby.

  * **Poznámka:** Pokud je uživatel v **vynucené** stavu, může je nastavená na **zakázané** dočasně a informovat je zpátky do svého účtu. Poté, co jsou zpět v, můžete změnit jejich stav na **povoleno** znovu, aby je znovu zaregistrovat svoje kontaktní údaje při příštím přihlášení v vyžadují. Alternativně můžete podle kroků v [zkontrolujte kontaktní údaje pro ověření uživatele](#check-a-users-authentication-contact-info) ověření nebo pro ně nastavit tato data.

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

## <a name="problems-with-groups"></a>Problémy se skupinami

Přístup k aplikaci mohou být blokovány z důvodu problému se skupinou, která je přiřazena k aplikaci. Toto jsou některé způsobů řešení problémů a řešení problémů s skupin a členství ve skupinách:

-   [Kontrola členství](#check-a-groups-membership)

-   [Zkontrolujte kritéria členství dynamické skupiny](#check-a-dynamic-groups-membership-criteria)

-   [Zkontrolujte skupiny přiřazené licence](#check-a-groups-assigned-licenses)

-   [Opětovné zpracování licencí skupiny](#reprocess-a-groups-licenses)

-   [Přiřazení licence skupině](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Kontrola členství

Pokud chcete zkontrolovat členství, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupiny, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **členy** chcete zobrazit seznam uživatelů přidružených k této skupině.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Zkontrolujte kritéria členství dynamické skupiny 

Pokud chcete zkontrolovat kritéria členství dynamické skupiny, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupiny, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **pravidla dynamického členství.**

8.  Zkontrolujte **jednoduché** nebo **pokročilé** pravidlo definované pro tuto skupinu a ověřte, zda mají být členem této skupiny uživatele splňuje tato kritéria.

### <a name="check-a-groups-assigned-licenses"></a>Zkontrolujte skupiny přiřazené licence

Pokud chcete zkontrolovat skupiny přiřazené licence, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupiny, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **licence** zobrazíte, které aktuálně licence skupině přiřadila.

### <a name="reprocess-a-groups-licenses"></a>Opětovné zpracování licencí skupiny

Za účelem opětovného zpracování skupiny přiřazené licence, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupiny, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **licence** zobrazíte, které aktuálně licence skupině přiřadila.

8.  Klikněte na tlačítko **znovu zpracovat** tlačítko a ujistěte se, že na licence přiřazené členům této skupiny jsou aktuální. To může trvat dlouhou dobu, v závislosti na velikosti a složitosti skupiny.

   >[!NOTE]
   >Provedete to tak rychlejší, vezměte v úvahu dočasně přiřazování licencí pro uživatele přímo. [Přiřadit uživateli licenci](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Přiřazení licence skupině

Chcete-li přiřadit licenci ke skupině, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupiny, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **licence** zobrazíte, které aktuálně licence skupině přiřadila.

8.  Klikněte na tlačítko **přiřadit** tlačítko.

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelné** klikněte na tlačítko **možnosti přiřazení** položku, kterou chcete zásady přiřazovat produktů. Klikněte na tlačítko **Ok** po dokončení.

11. Klikněte na tlačítko **přiřadit** tlačítko tyto licence přiřadit k této skupině. To může trvat dlouhou dobu, v závislosti na velikosti a složitosti skupiny.

   >[!NOTE]
   >Provedete to tak rychlejší, vezměte v úvahu dočasně přiřazování licencí pro uživatele přímo. [Přiřadit uživateli licenci](#problems-with-application-consent).
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>Problémy se zásadami podmíněného přístupu

### <a name="check-a-specific-conditional-access-policy"></a>Zkontrolujte zásady konkrétní podmíněného přístupu

Zkontrolujte nebo ověřit zásady podmíněného přístupu jeden:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** v navigační nabídce.

5.  Klikněte na tlačítko **podmíněného přístupu** navigační položka.

6.  Klikněte na zásadu, která vás zajímají kontrola.

7.  Zkontrolujte, že neexistují žádné zvláštní podmínky, přiřazení nebo jiné nastavení, která může blokovat přístup uživatelů.

   >[!NOTE]
   >Možná budete chtít dočasně zakázat tuto zásadu zajistit není by to ovlivnilo přihlášení. Chcete-li to provést, nastavte **povolit zásady** přepnutím **ne** a klikněte na tlačítko **Uložit** tlačítko.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Zkontrolujte zásady podmíněného přístupu pro konkrétní aplikaci

Zkontrolujte nebo ověřit jednu aplikaci aktuálně nakonfigurované zásady podmíněného přístupu:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** v navigační nabídce.

5.  Klikněte na tlačítko **všechny aplikace**.

6.  Vyhledejte aplikaci, která vás zajímají, nebo uživatel se pokouší přihlásit k pomocí zobrazovaný název aplikace nebo id aplikace.

     >[!NOTE]
     >Pokud nevidíte aplikaci, které hledáte, klikněte na tlačítko **filtr** tlačítko a rozbalte obor seznamu **všechny aplikace**. Pokud chcete zobrazit další sloupce, klikněte na tlačítko **sloupce** tlačítko pro přidání další podrobnosti pro vaše aplikace.
     >
     >

7.  Klikněte na tlačítko **podmíněného přístupu** navigační položka.

8.  Klikněte na zásadu, která vás zajímají kontrola.

9.  Zkontrolujte, že neexistují žádné zvláštní podmínky, přiřazení nebo jiné nastavení, která může blokovat přístup uživatelů.

     >[!NOTE]
     >Možná budete chtít dočasně zakázat tuto zásadu zajistit není by to ovlivnilo přihlášení. Chcete-li to provést, nastavte **povolit zásady** přepnutím **ne** a klikněte na tlačítko **Uložit** tlačítko.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Zakázat zásadu konkrétní podmíněného přístupu

Zkontrolujte nebo ověřit zásady podmíněného přístupu jeden:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** v navigační nabídce.

5.  Klikněte na tlačítko **podmíněného přístupu** navigační položka.

6.  Klikněte na zásadu, která vás zajímají kontrola.

7.  Zakázat zásadu tak, že nastavíte **povolit zásady** přepnutím **č** a klikněte na tlačítko **Uložit** tlačítko.

## <a name="problems-with-application-consent"></a>Problémy s souhlasu s aplikací

Přístup k aplikaci můžete blokovat, protože nedošlo k operace vyjádření souhlasu správná oprávnění. Toto jsou některé způsobů řešení problémů a řešení problémů v aplikacích souhlasu:

-   [Provedení operace vyjádření souhlasu uživatele](#perform-a-user-level-consent-operation)

-   [Provedení operace schválením na úrovni správce pro všechny aplikace](#perform-administrator-level-consent-operation-for-any-application)

-   [Provedení schválením na úrovni správce pro jednoho tenanta aplikaci](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Provedení schválením na úrovni správce pro aplikaci s více tenanty](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Provedení operace vyjádření souhlasu uživatele

-   Pro všechny Open ID Connect aplikace s povolenými, která požaduje oprávnění přejdete na přihlašovací obrazovce aplikace provede úrovně souhlasu uživatele k aplikaci pro přihlášeného uživatele.

-   Pokud budete chtít provést programově, naleznete v tématu [vyžádání souhlasu uživatele](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Provedení operace schválením na úrovni správce pro všechny aplikace

-   Pro **pouze aplikace vyvinuté pomocí modelu aplikace V1**, můžete vynutit této úrovně souhlas správce na výskyt tak, že přidáte "**? řádku = správce\_souhlas**" na konec objektu Přihlašovací adresa URL na aplikace.

-   Pro **jakékoli aplikace vyvinuté pomocí modelu aplikace V2**, můžete vynutit tato schválením na úrovni správce na výskyt podle pokynů v části **žádat oprávnění správce directory** část [pomocí koncového bodu souhlas správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Provedení schválením na úrovni správce pro jednoho tenanta aplikaci

-   Pro **aplikace s jedním tenantem** , který žádost o oprávnění (například vyvíjíte nebo vlastní ve vaší organizaci), můžete provést **správu úroveň souhlasu** operace jménem všech uživatelé přihlášení jako globální správce a kliknutím na **udělit oprávnění** tlačítko v horní části **registru aplikace –&gt; všechny aplikace –&gt; vyberte aplikaci, -&gt; Požadovaná oprávnění** podokně.

-   Pro **jakékoli aplikace vyvinuté pomocí modelu aplikace V1 nebo V2**, můžete vynutit tato schválením na úrovni správce na výskyt podle pokynů v části **požádat o oprávnění od správce adresáře**  část [pomocí koncového bodu souhlas správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Provedení schválením na úrovni správce pro aplikaci s více tenanty

-   Pro **aplikací s více tenanty** žádost o oprávnění (jako jsou aplikace třetích stran nebo sama vyvinula společnost Microsoft), můžete provést **správu úroveň souhlasu** operace. Přihlaste se jako globální správce a kliknete na **udělit oprávnění** tlačítko **podnikové aplikace –&gt; všechny aplikace -&gt; vyberte aplikaci, -&gt; oprávnění**  podokně (k dispozici brzy).

-   Můžete také vynutit tato schválením na úrovni správce na výskyt podle pokynů v části **požádat o oprávnění od správce adresáře** část [pomocí koncového bodu souhlas správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Další postup
[Pomocí koncového bodu souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

