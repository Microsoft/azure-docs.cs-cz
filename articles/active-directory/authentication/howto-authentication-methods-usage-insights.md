---
title: Aktivita metod ověřování – Azure Active Directory
description: Přehled metod ověřování registrovaných a používaných vaší organizací pro přihlášení a resetování hesla.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6566ee7e744411fc3b7005938f95181e5c5ec94d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645137"
---
# <a name="authentication-methods-activity"></a>Aktivita Metody ověřování 

Nový řídicí panel aktivity metody ověřování umožňuje správcům monitorovat registraci a využití metod ověřování v rámci organizace. Tato funkce vytváření sestav poskytuje vaší organizaci prostředky, které vám pomohou pochopit, jaké metody se registrují a jak se používají.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Oprávnění a licence

K využití a přehledům můžou získat přístup následující role:

- Čtečka sestav
- Čtenář zabezpečení
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

Klikněte na **Uživatelé s Multi-Factor Authentication Azure**, **Uživatelé, kteří** mají možnost **Samoobslužné resetování hesla nebo uživatelé s možností samoobslužného resetování hesla**, nebo přehledy, abyste předem vyfiltroval seznam podrobností o registraci uživatele.

- **Uživatelé, kteří jsou schopni azure Multi-Factor Authentication** zobrazuje rozpis uživatelů schopných MFA ve službě Azure AD. Uživatelé se považují za dostupné, pokud jsou registrovaný pro metodu silného ověřování a jsou povolené pomocí zásad, aby mohli použít tuto metodu k provedení VÍCEFAKTOROVÉHO ověřování. Toto číslo neodráží uživatele zaregistrované pro MFA mimo Azure AD. 
- **Uživatelé, kteří jsou schopni ověřování bez hesla** , zobrazují členění uživatelů, kteří se můžou přihlašovat bez hesla. To zahrnuje uživatele zaregistrované pro FIDO2, Windows Hello pro firmy a přihlašování bez hesla pomocí aplikace Microsoft Authenticator. 
- **Uživatelé, kteří mají Samoobslužné resetování hesla** , zobrazují rozpis uživatelů s možností samoobslužného resetování hesla. Uživatelé se považují za možné SSPR, pokud jsou oba registrováni pro dostatečnou metodu pro splnění zásad SSPR organizace a povolení k provádění SSPR. 

  ![Snímek obrazovky uživatelů s možností registrace](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Uživatelé zaregistrované metodou ověřování** ukazují, kolik uživatelů je zaregistrovaných pro každou metodu ověřování. Klikněte na metodu ověřování a zjistěte, kteří uživatelé jsou pro danou metodu zaregistrovaní. 

![Snímek obrazovky registrovaných uživatelů](media/how-to-authentication-methods-usage-insights/users-registered.png)

**Poslední registrace pomocí metody ověřování** zobrazuje počet úspěšných a neúspěšných zaregistrovaných metod ověřování podle metody ověřování. Kliknutím na metodu ověřování zobrazíte nedávné události registrace pro danou metodu.

![Snímek obrazovky s nedávno zaregistrovaným](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Podrobnosti o využití

Sestava **využití** ukazuje, které metody ověřování používají uživatelé k přihlášení a resetování hesel.

![Snímek obrazovky se stránkou použití](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Přihlášení podle požadavku na ověření** zobrazuje počet úspěšných interaktivních přihlášení uživatelů, která byla nutná k provádění vícefaktorového ověřování ve službě Azure AD. V takovém případě se nereflektují přihlášení, u kterých bylo uplatněno ověřování MFA poskytovatelem vícefaktorového ověřování třetí strany.

![Snímek obrazovky s přihlášením podle požadavku na ověření](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Přihlášení metodou ověřování** se zobrazuje počet interaktivních přihlášení uživatelů (úspěšné a neúspěšné) podle použité metody ověřování. Nezahrnuje přihlášení, kde požadavek na ověření byl splněn deklarací identity v tokenu.

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

  ![Snímek obrazovky se stránkou použití](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Omezení

Zápisy dočasného přístupu (klepnutím) se na kartě registrace v sestavě neprojeví, protože jsou platné jenom pro krátkou dobu.

## <a name="next-steps"></a>Další kroky

- [Práce s rozhraním API pro sestavy využití metod ověřování](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Výběr metod ověřování pro vaši organizaci](concept-authentication-methods.md)
- [Prostředí kombinované registrace](concept-registration-mfa-sspr-combined.md)