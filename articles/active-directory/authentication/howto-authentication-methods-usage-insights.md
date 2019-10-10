---
title: Použití metod ověřování & Insights Reporting (Preview) – Azure Active Directory
description: Vytváření sestav o samoobslužném resetování hesla služby Azure AD a Multi-Factor Authentication použití metody ověřování
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddfea07989f52c463816318276fd5b6643cb2041
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255061"
---
# <a name="authentication-methods-usage--insights-preview"></a>Použití metod ověřování & Insights (Preview)

Využití & Insights vám umožní pochopit, jak metody ověřování pro funkce, jako je Azure Multi-Factor Authentication a Samoobslužné resetování hesla, fungují ve vaší organizaci. Tato funkce vytváření sestav poskytuje vaší organizaci prostředky, které vám pomohou pochopit, jaké metody se registrují a jak se používají.

## <a name="permissions-and-licenses"></a>Oprávnění a licence

K využití a přehledům můžou získat přístup následující role:

- Globální správce
- Čtecí modul zabezpečení
- Správce zabezpečení
- Čtečka sestav

Pro přístup k používání a přehledům nejsou potřeba žádné další licence. Informace o licencování služby Azure Multi-Factor Authentication a Samoobslužné resetování hesla (SSPR) najdete na [webu Azure Active Directory s cenami](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Jak to funguje

Přístup k používání metod ověřování a přehledům:

1. Přejděte na web [Azure Portal](https://portal.azure.com).
1. Přejděte na **Azure Active Directory** > **resetování hesla** > **použití & přehledy**.
1. Z přehledů **registrace** nebo **využití** můžete zvolit otevření předem filtrovaných sestav, které se budou filtrovat podle vašich potřeb.

![Přehled využití & Insights](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Pokud chcete získat přístup k využití & Insights přímo, přejděte na [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Tento odkaz vás přenese na přehled registrace.

Uživatelé zaregistrovaní, uživatelé a uživatelé s podporou dlaždic zobrazí následující registrační data pro vaše uživatele:

- Registrováno: uživatel se považuje za registrovaný, pokud (nebo správce) zaregistroval dostatek metod ověřování pro splnění zásad SSPR nebo Multi-Factor Authentication vaší organizace.
- Povoleno: uživatel se považuje za povolený, pokud jsou v oboru pro zásady SSPR. Pokud je pro skupinu povolená možnost SSPR, bude uživatel považován za povolený, pokud se nachází v této skupině. Pokud je SSPR povolená pro všechny uživatele, považují se za povolené všechny uživatele v tenantovi (kromě hostů).
- Možnost: uživatel se považuje za schopný, pokud je registrovaný i povolený. Tento stav znamená, že v případě potřeby mohou SSPR kdykoli provádět.

Kliknutím na kteroukoli z těchto dlaždic nebo přehledů zobrazených v nich získáte předem filtrovaný seznam podrobností o registraci.

Graf **registrací** na kartě **registrace** zobrazuje počet úspěšných a neúspěšných zaregistrovaných metod ověřování podle metody ověřování. Graf resetování na kartě **využití** zobrazuje počet úspěšných a neúspěšných ověření během metody resetování hesla podle ověření.

Když kliknete na některé z grafů, zobrazí se předem filtrovaný seznam událostí registrace nebo resetování.

Pomocí ovládacího prvku v pravém horním rohu můžete změnit rozsah dat pro auditovaná data zobrazená v registracích a resetovat grafy na 24 hodin, 7 dní nebo 30 dní.

### <a name="registration-details"></a>Podrobnosti registrace

Kliknutím na **zaregistrované uživatele**, **povoleným uživatelům**nebo **uživatelům, kteří** můžou používat dlaždice nebo přehledy, vás přenese podrobnosti o registraci.

Sestava Podrobnosti o registraci zobrazuje pro každého uživatele následující informace:

- Name (Název)
- Uživatelské jméno
- Stav registrace (vše, registrováno, Neregistrováno)
- Povolený stav (vše, povoleno, Nepovoleno)
- Stav schopností (vše, schopný, neschopný)
- Metody (oznámení aplikace, kód aplikace, telefonní hovor, SMS, E-mail, bezpečnostní otázky)

Pomocí ovládacích prvků v horní části seznamu můžete vyhledat uživatele a filtrovat seznam uživatelů na základě zobrazených sloupců.

### <a name="reset-details"></a>Resetovat podrobnosti

Kliknutím na registrace nebo resetování grafů zobrazíte podrobnosti o resetování.

Sestava obnovit podrobnosti zobrazuje události registrace a resetování za posledních 30 dní, včetně:

- Name (Název)
- Uživatelské jméno
- Funkce (vše, registrace, resetování)
- Metoda ověřování (oznámení aplikace, kód aplikace, telefonní hovor, volání Office, SMS, E-mail, bezpečnostní otázky)
- Stav (vše, úspěch, selhání)

Pomocí ovládacích prvků v horní části seznamu můžete vyhledat uživatele a filtrovat seznam uživatelů na základě zobrazených sloupců.

## <a name="limitations"></a>Omezení

Data zobrazená v těchto sestavách se budou zpozdit až o 60 minut. V Azure Portal existuje pole Poslední aktualizace, které vám umožní určit, jak se vaše data nacházejí.

Data o využití a Insights nejsou náhradou za sestavy aktivit Azure Multi-Factor Authentication ani informace obsažené v sestavě přihlášení k Azure AD.

## <a name="next-steps"></a>Další kroky

- [Práce s rozhraním API pro sestavy využití metod ověřování](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Výběr metod ověřování pro vaši organizaci](concept-authentication-methods.md)
- [Prostředí kombinované registrace](concept-registration-mfa-sspr-combined.md)
