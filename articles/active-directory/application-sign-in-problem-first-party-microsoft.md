---
title: Potíže při přihlašování k aplikaci Microsoft | Microsoft Docs
description: Řešení běžných potíží potýkají při přihlašování k Applications Microsoft první strany pomocí služby Azure AD (např. Office 365)
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 1dc727f46785d2896544d8ef9098259f9ab994d1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29384207"
---
## <a name="problems-signing-in-to-a-microsoft-application"></a>Potíže při přihlašování k aplikaci Microsoft

Microsoft Applications (např. Office 365 Exchange, SharePoint, Yammer atd.) jsou přiřazeny a trochu jinak než 3. aplikace SaaS strany nebo jiné aplikace, které integrují s Azure AD pro jednotné přihlašování na spravovat.

Existují tři hlavní způsoby, že uživatel může získat přístup k aplikaci Microsoft publikovaná.

-   Pro aplikace v Office 365 nebo jiné placené sady, mají uživatelé udělen přístup prostřednictvím **přiřazení licence** buď přímo do uživatelského účtu, nebo prostřednictvím skupiny pomocí funkce přiřazení našeho na základě skupin licencí.

-   Pro aplikace, které Microsoftu nebo třetí strany publikuje pro všechny volně, může být uživatelé udělen přístup prostřednictvím **souhlas uživatele**. This0 znamená, že se přihlásit k aplikaci s jejich Azure AD pracovního nebo školního účtu a povolit ji tak, aby měl přístup k některé omezenou sadu dat na svůj účet.

-   Pro aplikace, které společnost Microsoft nebo 3rd strany publikuje pro všechny volně, uživatelé také udělit přístup přes **souhlas správce**. To znamená, že správce zjistí, že aplikace může být používán všichni uživatelé v organizaci, takže se přihlásit k aplikaci pomocí účtu globálního správce a udělit přístup všem uživatelům v organizaci.

Chcete-li vyřešit problém, můžete začít s [obecné problémových oblastí aplikace přístup k zvažte](#general-problem-areas-with-application-access-to-consider) a přečtěte si [návod: postup řešení potíží s přístupem Microsoft Application](#walkthrough-steps-to-troubleshoot-microsoft-application-access) získat na podrobné informace.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Obecné problémových oblastí aplikace přístup ke zvážení

Následuje seznam obecné problémových oblastí, které můžete rozbalit Pokud máte představu o kde začít, ale doporučujeme, abyste si přečetli návodu tak, aby mohli rychle začít: [návod: postup řešení potíží s přístupem Microsoft Application](#walkthrough-steps-to-troubleshoot-microsoft-application-access).

-   [Problémy s uživatelského účtu](#problems-with-the-users-account)

-   [Problémy se skupinami](#problems-with-groups)

-   [Problémy se zásadami podmíněného přístupu](#problems-with-conditional-access-policies)

-   [Problémy s aplikací souhlasu](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Postup řešení potíží s přístupem Microsoft Application

Toto jsou některé běžné problémy, které zaměstnance spustit do, když uživatelé nemůžete se přihlásit k aplikaci společnosti Microsoft.

-   Běžné problémy a proveďte nejprve kontrolu

  * Ujistěte se, že uživatel je přihlášení k **opravte adresu URL** a ne místní aplikace adresy URL.

  * Zkontrolujte, zda je účet uživatele **není uzamčen.**

  * Zajistěte, aby **uživatelský účet existuje** v Azure Active Directory. [Zkontrolujte, jestli uživatelský účet existuje v Azure Active Directory](#problems-with-the-users-account)

  * Zkontrolujte, zda je účet uživatele **povoleno** pro přihlášení. [Zkontrolujte stav účtu uživatele](#problems-with-the-users-account)

  * Zkontrolujte, že uživatele **není platnost nebo zapomenete heslo.** [Resetovat heslo uživatele](#reset-a-users-password) nebo [povolit samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * Zajistěte, aby **Multi-Factor Authentication** neblokuje přístup uživatelů. [Zkontrolujte stav služby Multi-Factor authentication uživatele](#check-a-users-multi-factor-authentication-status) nebo [zkontrolovat kontaktní informace o ověřování uživatele](#check-a-users-authentication-contact-info)

   * Zajistěte, aby **zásady podmíněného přístupu** nebo **Identity Protection** zásad neblokuje přístup uživatelů. [Zkontrolujte zásady podmíněného přístupu konkrétním](#problems-with-conditional-access-policies) nebo [zkontrolujte zásady podmíněného přístupu pro konkrétní aplikaci](#check-a-specific-applications-conditional-access-policy) nebo [zakázat zásadu podmíněného přístupu konkrétním](#disable-a-specific-conditional-access-policy)

   * Ujistěte se, že uživatele **ověřování kontaktní údaje** je aktuální povolit Multi-Factor Authentication nebo podmíněného přístupu zásad, která budou vynucena. [Zkontrolujte stav služby Multi-Factor authentication uživatele](#check-a-users-multi-factor-authentication-status) nebo [zkontrolovat kontaktní informace o ověřování uživatele](#check-a-users-authentication-contact-info)

-   Pro **Microsoft** **aplikace, které vyžadují licenci** (např. Office 365), tady jsou některé specifické problémy zkontrolujte po nevyloučí obecné problémy výše:

   * Ujistěte se, uživatele nebo má **přiřazenou licenci.** [Zkontrolujte uživatele přiřazené licence](#check-a-users-assigned-licenses) nebo [Zkontrolujte skupiny přiřazené licence](#check-a-groups-assigned-licenses)

   * Pokud je licence **přiřazené** **statická skupina**, ujistěte se, že **uživatel je členem** této skupiny. [Zkontrolujte členství uživatele ve skupinách](#check-a-users-group-memberships)

   * Pokud je licence **přiřazené** **dynamická skupina**, ujistěte se, že **je dynamická skupina pravidlo správně nastavené**. [Zkontrolujte kritéria členství dynamické skupiny](#check-a-dynamic-groups-membership-criteria)

   * Pokud je licence **přiřazené** **dynamická skupina**, zkontrolujte, zda dynamická skupina má **bylo dokončeno zpracování** jeho členství a že **uživatel je členem** (to může trvat nějakou dobu). [Zkontrolujte členství uživatele ve skupinách](#check-a-users-group-memberships)

   *  Jakmile budete mít jistotu, je přiřazena licence, zkontrolujte, zda je licence **nevypršela**.

   *  Zkontrolujte, zda je licence **pro aplikaci** uživatelé přistupují.

-   Pro **Microsoft** **aplikace, které nevyžadují licenci**, zde jsou některé další body ke kontrole:

   * Pokud aplikace požaduje **oprávnění na úrovni uživatele** (například "přístup k poštovní schránka tohoto uživatele"), ujistěte se, že uživatel přihlásí k aplikaci a byla provedena **individuální souhlasu operaci** umožníte přístup k jeho data aplikace.

   * Pokud aplikace požaduje **oprávnění na úrovni správce** (například "přístup k poštovním schránkám všechny uživatelské"), ujistěte se, že byla provedena globálního správce **operace na úrovni správce souhlas jménem všichni uživatelé** v organizaci.

## <a name="problems-with-the-users-account"></a>Problémy s uživatelského účtu

Z důvodu problému s uživatelem, který je přiřazen k aplikaci lze blokovat přístup k aplikaci. Následují některé způsoby řešení problémů a řešení problémů s uživateli a jejich nastavení účtu:

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

10. Komunikaci tohoto nového hesla pro uživatele, budou muset změnit toto heslo při příštím přihlášení v do Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Povolit samoobslužné resetování hesla

Pokud chcete povolit samoobslužné resetování hesla, postupujte podle následujících kroků nasazení:

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

  * **Poznámka:**: Pokud je uživatel v **vynucené** stavu, může je nastaven **zakázané** dočasně a dát jim zpět do svého účtu. Jakmile se zpět, můžete změnit svůj stav na **povoleno** znovu, aby je znovu zaregistrovat své kontaktní informace při příštím přihlášení v vyžadují. Alternativně můžete podle kroků v [zkontrolovat kontaktní informace o ověřování uživatele](#check-a-users-authentication-contact-info) ověření nebo pro ně nastavit tato data.

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

## <a name="problems-with-groups"></a>Problémy se skupinami

Z důvodu problému s skupinu, která je přiřazena k aplikaci lze blokovat přístup k aplikaci. Následují některé způsoby řešení problémů a řešení problémů s skupin a členství ve skupinách:

-   [Zkontrolovat členství ve skupině](#check-a-groups-membership)

-   [Zkontrolujte kritéria členství dynamické skupiny](#check-a-dynamic-groups-membership-criteria)

-   [Zkontrolujte skupiny přiřazené licence](#check-a-groups-assigned-licenses)

-   [Znovu zpracovat skupině licencí](#reprocess-a-groups-licenses)

-   [Přiřazení skupiny licencí](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Zkontrolovat členství ve skupině

Pokud chcete zkontrolovat členství ve skupině, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupinu vás zajímá a **klikněte na řádek** vybrat.

7.  Klikněte na tlačítko **členy** zobrazíte seznam uživatelů přiřadí do této skupiny.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Zkontrolujte kritéria členství dynamické skupiny 

Pokud chcete zkontrolovat kritéria členství dynamické skupiny, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupinu vás zajímá a **klikněte na řádek** vybrat.

7.  Klikněte na tlačítko **pravidla dynamické členství.**

8.  Zkontrolujte **jednoduché** nebo **rozšířené** pravidlo definované pro tuto skupinu a zkontrolujte, zda mají být členem této skupiny uživatele splňuje tato kritéria.

### <a name="check-a-groups-assigned-licenses"></a>Zkontrolujte skupiny přiřazené licence

Pokud chcete zkontrolovat skupiny přiřazené licence, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupinu vás zajímá a **klikněte na řádek** vybrat.

7.  Klikněte na tlačítko **licence** zobrazíte, které skupiny licencí aktuálně jeho přiřazení.

### <a name="reprocess-a-groups-licenses"></a>Znovu zpracovat skupině licencí

Opětovné zpracování skupiny přiřazené licence, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupinu vás zajímá a **klikněte na řádek** vybrat.

7.  Klikněte na tlačítko **licence** zobrazíte, které skupiny licencí aktuálně jeho přiřazení.

8.  klikněte **znovu zpracovat** tlačítko zkontrolujte, zda mají členy této skupiny přiřazené licence jsou aktuální. To může trvat dlouhou dobu, v závislosti na velikost a složitost skupiny.

   >[!NOTE]
   >Uděláte to tak rychleji, vezměte v úvahu dočasně přiřazení licence uživatele přímo. [Přiřazení licence uživatele](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Přiřazení skupiny licencí

Přiřadit licenci pro skupinu, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupinu vás zajímá a **klikněte na řádek** vybrat.

7.  Klikněte na tlačítko **licence** zobrazíte, které skupiny licencí aktuálně jeho přiřazení.

8.  klikněte **přiřadit** tlačítko.

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelné** klikněte na tlačítko **přiřazení možností** položky granularly přiřadit produkty. Klikněte na tlačítko **Ok** po dokončení.

11. Klikněte **přiřadit** tlačítko přiřadit tyto licence do této skupiny. To může trvat dlouhou dobu, v závislosti na velikost a složitost skupiny.

   >[!NOTE]
   >Uděláte to tak rychleji, vezměte v úvahu dočasně přiřazení licence uživatele přímo. [Přiřazení licence uživatele](#problems-with-application-consent).
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>Problémy se zásadami podmíněného přístupu

### <a name="check-a-specific-conditional-access-policy"></a>Zkontrolujte zásady podmíněného přístupu konkrétním

Zkontrolujte nebo ověřit zásady jednoho podmíněného přístupu:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** v navigační nabídce.

5.  klikněte **podmíněného přístupu** navigační položka.

6.  Klikněte na zásadu, která vás zajímá kontroly.

7.  Zkontrolujte, že neexistují žádné konkrétní podmínky, přiřazení nebo jiná nastavení, které blokuje přístup uživatelů.

   >[!NOTE]
   >Chcete dočasně zakázat tuto zásadu a zajistit tak nemají vliv přihlášení. Chcete-li to provést, nastavte **povolit zásady** přepnutím **ne** a klikněte na tlačítko **Uložit** tlačítko.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Zkontrolujte zásady podmíněného přístupu pro konkrétní aplikace

Zkontrolujte nebo ověřit jednu aplikaci aktuálně nakonfigurované zásady podmíněného přístupu:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** v navigační nabídce.

5.  Klikněte na tlačítko **všechny aplikace**.

6.  Vyhledejte aplikace, které vás zajímají, nebo uživatel se pokouší přihlásit k aplikaci zobrazovaný název nebo id aplikace.

     >[!NOTE]
     >Pokud nevidíte aplikaci, kterou hledáte, klikněte **filtru** tlačítko a rozbalte oboru v seznamu **všechny aplikace**. Pokud chcete zobrazit více sloupců, klikněte **sloupce** tlačítko přidáte další podrobnosti pro vaše aplikace.
     >
     >

7.  klikněte **podmíněného přístupu** navigační položka.

8.  Klikněte na zásadu, která vás zajímá kontroly.

9.  Zkontrolujte, že neexistují žádné konkrétní podmínky, přiřazení nebo jiná nastavení, které blokuje přístup uživatelů.

     >[!NOTE]
     >Chcete dočasně zakázat tuto zásadu a zajistit tak nemají vliv přihlášení. Chcete-li to provést, nastavte **povolit zásady** přepnutím **ne** a klikněte na tlačítko **Uložit** tlačítko.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Zakázat zásadu podmíněného přístupu konkrétním

Zkontrolujte nebo ověřit zásady jednoho podmíněného přístupu:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** v navigační nabídce.

5.  klikněte **podmíněného přístupu** navigační položka.

6.  Klikněte na zásadu, která vás zajímá kontroly.

7.  Zakažte zásadu nastavením **povolit zásady** přepnutím **ne** a klikněte na tlačítko **Uložit** tlačítko.

## <a name="problems-with-application-consent"></a>Problémy s aplikací souhlasu

Přístup k aplikaci můžete blokovat, protože nedošlo k operaci souhlasu příslušná oprávnění. Následují některé způsoby řešení problémů a vyřešit problémy s aplikací souhlasu:

-   [Provedení operace individuální souhlasu](#perform-a-user-level-consent-operation)

-   [Provedení operace správce úroveň souhlasu pro žádnou aplikaci.](#perform-administrator-level-consent-operation-for-any-application)

-   [Provést na úrovni správce souhlasu pro jednoho klienta aplikace](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Provést na úrovni správce souhlasu pro víceklientské aplikace](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Provedení operace individuální souhlasu

-   Pro každou Open ID Connect-povolit aplikaci, která vyžaduje oprávnění přejdete na přihlašovací obrazovka, aplikace provádí úrovni souhlasu uživatele k aplikaci pro přihlášeného uživatele.

-   Pokud chcete provést programově, přečtěte si téma [vyžaduje souhlas jednotlivé uživatele](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Provedení operace správce úroveň souhlasu pro žádnou aplikaci.

-   Pro **jenom aplikace vyvinuté pomocí aplikačního modelu V1**, můžete vynutit této úrovně souhlasu správce proběhnout přidáním "**? řádku = správce\_souhlas**" na konec přihlašovací adresa URL aplikace.

-   Pro **všechny aplikace vyvinuté pomocí aplikačního modelu V2**, můžete vynutit svůj souhlas úrovni správce proběhnout podle pokynů v části **oprávnění požádat správce directory** části [pomocí koncový bod admin souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Provést na úrovni správce souhlasu pro jednoho klienta aplikace

-   Pro **jednoho klienta aplikace** , žádostí o oprávnění (jako jsou ty, které jste vývoji nebo vlastní ve vaší organizaci), můžete provést **správu úroveň souhlasu** operace jménem všechny uživatelé přihlášení jako globální správce a kliknutím na **udělit oprávnění** tlačítka v horní části **aplikace registru -&gt; všechny aplikace -&gt; vyberte aplikaci -&gt; Požadovaná oprávnění** podokně.

-   Pro **všechny aplikace vyvinuté pomocí aplikačního modelu V1 nebo V2**, můžete vynutit svůj souhlas úrovni správce proběhnout podle pokynů v části **oprávnění požádat správce directory** části [pomocí koncový bod admin souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Provést na úrovni správce souhlasu pro víceklientské aplikace

-   Pro **víceklientským aplikacím** této žádosti o oprávnění (jako jsou aplikace třetích stran nebo sama vyvinula společnost Microsoft,), můžete provést **správu úroveň souhlasu** operaci. Přihlaste se jako globální správce a kliknutím na **udělit oprávnění** tlačítko pod **podnikové aplikace –&gt; všechny aplikace -&gt; vyberte aplikaci -&gt; oprávnění**  podokně (k dispozici brzy).

-   Může také vynucovat svůj souhlas úrovni správce proběhnout podle pokynů v části **oprávnění požádat správce directory** části [pomocí koncový bod admin souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Další postup
[Pomocí koncový bod admin souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

