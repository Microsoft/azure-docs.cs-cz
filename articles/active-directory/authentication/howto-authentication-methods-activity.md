---
title: Aktivita metod ověřování – Azure Active Directory
description: Přehled metod ověřování, které uživatelé registrují pro přihlášení a resetování hesel.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/04/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0865fb2bda04f5a7e9ba2ef73a717946fa656a5
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175307"
---
# <a name="authentication-methods-activity"></a>Aktivita Metody ověřování 

Nový řídicí panel aktivity metody ověřování umožňuje správcům monitorovat registraci a využití metod ověřování v rámci organizace. Tato funkce vytváření sestav poskytuje vaší organizaci prostředky, které vám pomohou pochopit, jaké metody se registrují a jak se používají.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Oprávnění a licence

Integrované a vlastní role s následujícími oprávněními mají přístup k oknu aktivity metody ověřování a k rozhraním API:

- Microsoft. Directory/auditLogs/allProperties/Read
- Microsoft. Directory/signInReports/allProperties/Read

Požadovaná oprávnění jsou k dispozici v následujících rolích:

- Čtečka sestav
- Čtenář zabezpečení
- Globální čtenář
- Operátor zabezpečení
- Správce zabezpečení
- Globální správce

 Pro přístup k využití a přehledům se vyžaduje licence Azure AD Premium P1 nebo P2. Informace o licencování služby Azure AD Multi-Factor Authentication a samoobslužného resetování hesla (SSPR) najdete na [webu Azure Active Directory s cenami](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Jak to funguje

Přístup k používání metod ověřování a přehledům:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Klikněte na možnost **Azure Active Directory**  >    >    >  **aktivita** ověřování zabezpečení.
1. V sestavě jsou dvě karty: **registrace** a **použití**.

   ![Přehled aktivit metod ověřování](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Podrobnosti registrace

Na [**kartu registrace**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) můžete získat přístup, pokud chcete zobrazit počet uživatelů, kteří mají možnost ověřování Multi-Factor Authentication, ověřování passowordless a Samoobslužné resetování hesla. 

Pokud chcete předem vyfiltrovat seznam podrobností o registraci uživatele, klikněte na některou z následujících možností:

- Uživatelé, kteří jsou **schopni Multi-Factor Authentication Azure** , zobrazují členění uživatelů, kteří mají obojí:
  - Registrováno pro metodu silného ověřování 
  - Povolené zásadami pro použití této metody pro MFA 
  
  Toto číslo neodráží uživatele zaregistrované pro MFA mimo Azure AD. 
- **Uživatelé** , kteří mají ověřování bez hesla, zobrazují rozpis uživatelů registrovaných pro přihlášení bez hesla pomocí FIDO2, Windows Hello pro firmy nebo přihlášení bez hesla pomocí telefonického přihlášení pomocí aplikace Microsoft Authenticator. 
- Uživatelé, kteří si můžou **Samoobslužné resetování hesla** zobrazovat, budou mít přehled o uživatelích, kteří si můžou resetovat hesla. Uživatelé můžou resetovat svoje heslo, pokud jsou obě:
  - Registrováno pro dostatečnou metodu pro uspokojení zásad organizace pro Samoobslužné resetování hesla 
  - Povolení resetování hesla 

  ![Snímek obrazovky uživatelů, kteří se můžou zaregistrovat](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Uživatelé zaregistrované metodou ověřování** ukazují, kolik uživatelů je zaregistrovaných pro každou metodu ověřování. Kliknutím na metodu ověřování zjistíte, kdo je pro danou metodu zaregistrován.

![Snímek obrazovky registrovaných uživatelů](media/how-to-authentication-methods-usage-insights/users-registered.png)

**Poslední registrace metodou ověřování** ukazuje, kolik registrací bylo úspěšné a neúspěšné, seřazené podle metody ověřování. Kliknutím na metodu ověřování zobrazíte nedávné události registrace pro danou metodu.

![Snímek obrazovky s nedávno zaregistrovaným](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Podrobnosti o využití

Sestava **využití** ukazuje, které metody ověřování se používají k přihlášení a resetování hesel.

![Snímek obrazovky se stránkou použití](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Přihlášení podle požadavku na ověření** zobrazuje počet úspěšných interaktivních přihlášení uživatelů potřebných pro jednotné a vícefaktorové ověřování ve službě Azure AD. Nezahrnují se přihlášení, u kterých zprostředkovatel vícefaktorového ověřování (MFA) třetí strany vynutil ověřování.

![Snímek obrazovky s přihlášením podle požadavku na ověření](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Přihlášení metodou ověřování** se zobrazuje počet interaktivních přihlášení uživatelů (úspěšné a neúspěšné) podle použité metody ověřování. Nezahrnuje přihlášení, kde požadavek na ověření splnila deklarace identity v tokenu.

![Snímek obrazovky přihlášení podle metody](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**Počet resetování hesla a odemknutí účtu** zobrazuje počet úspěšných změn hesla a resetování hesla (Samoobslužná služba a správce) v průběhu času.

![Snímek obrazovky resetování a odemknutí](media/how-to-authentication-methods-usage-insights/password-changes.png)

Resetování **hesla podle metody ověřování** zobrazuje počet úspěšných a neúspěšných ověření během metody resetování hesla podle ověření.

![Snímek obrazovky s obnovením podle metody](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Podrobnosti registrace uživatele 

Pomocí ovládacích prvků v horní části seznamu můžete vyhledat uživatele a filtrovat seznam uživatelů na základě zobrazených sloupců.

Sestava Podrobnosti o registraci zobrazuje pro každého uživatele následující informace:

- Hlavní název uživatele
- Název
- Podporuje vícefaktorové ověřování (podporuje se, není možné)
- Podporující bez hesla (podporuje se, není možné)
- Registrovaný SSPR (registrováno, Neregistrováno)
- Povolený SSPR (povolený, nepovolený)
- Podporující SSPR (podporující, nepodporující) 
- Registrované metody (E-mail, mobilní telefon, alternativní mobilní telefon, telefon do kanceláře, Microsoft Authenticator push, software jednorázové heslo, FIDO2, bezpečnostní klíč, bezpečnostní otázky)

  ![Snímek obrazovky s podrobnostmi o registraci uživatele](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Události registrace a resetování 

**Události registrace a resetu** zobrazují události registrace a resetování za posledních 24 hodin, posledních sedm dní nebo posledních 30 dnů, včetně:

- Date (Datum)
- Uživatelské jméno
- User 
- Funkce (registrace, resetování)
- Použitá metoda (oznámení aplikace, kód aplikace, telefonní hovor, volání Office, alternativní mobilní volání, SMS, E-mail, bezpečnostní otázky)
- Stav (úspěch, selhání)
- Důvod selhání (vysvětlení)

  ![Snímek obrazovky s událostmi registrace a resetování](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Omezení

- Data v sestavě se v reálném čase neaktualizují a můžou odrážet latenci až po několik hodin.
- Dočasné registrace předávacího přístupu se na kartě registrace v sestavě neprojeví, protože jsou platné jenom pro krátkou dobu.

## <a name="next-steps"></a>Další kroky

- [Práce s rozhraním API pro sestavy využití metod ověřování](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Výběr metod ověřování pro vaši organizaci](concept-authentication-methods.md)
- [Prostředí kombinované registrace](concept-registration-mfa-sspr-combined.md)
