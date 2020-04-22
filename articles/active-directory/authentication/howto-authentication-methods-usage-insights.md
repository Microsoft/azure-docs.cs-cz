---
title: Využití metod ověřování & přehledy – Azure Active Directory
description: Vytváření sestav samoobslužných resetování hesla služby Azure AD a použití metody ověřování pomocí vícefaktorového ověřování
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053b052abf6f23c385dc7447639aa40b6c2c58a1
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680193"
---
# <a name="authentication-methods-usage--insights-preview"></a>Využití metod ověřování & přehledy (náhled)

Přehledy o využití & vám umožní pochopit, jak ve vaší organizaci fungují metody ověřování pro funkce, jako je azure vícefaktorové ověřování a samoobslužné resetování hesla. Tato funkce vykazování poskytuje vaší organizaci prostředky k pochopení, jaké metody jsou registrovány a jak jsou používány.

## <a name="permissions-and-licenses"></a>Oprávnění a licence

K využití a přehledům mají přístup následující role:

- Globální správce
- Čtečka zabezpečení
- Správce zabezpečení
- Čtečka sestav

Pro přístup k využití a přehledům není vyžadována žádná další licence. Informace o licencování Azure Multi-Factor Authentication a samoobslužné resetování hesla (SSPR) najdete na [cenovém webu Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Jak to funguje

Přístup k využití metody ověřování a přehledům:

1. Přejděte na [portál Azure](https://portal.azure.com).
1. Přejděte na **Azure Active Directory** > **Password reset** > **využití & přehledy**.
1. V přehledech **registrace** nebo **využití** můžete otevřít předfiltrované sestavy a filtrovat je podle svých potřeb.

![Přehled & využití](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Chcete-li získat přímý přístup [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)k informacím o využití &, přejděte na . Tento odkaz vás přenese do přehledu registrace.

Dlaždice Uživatelé, Uživatelé a Uživatelé, které jsou schopny, zobrazují uživatelům následující registrační data:

- Registrovaný: Uživatel je považován za registrovaného, pokud (nebo správce) zaregistroval dostatek metod ověřování, aby splnil zásady samodotykového pr nebo vícefaktorového ověřování vaší organizace.
- Povoleno: Uživatel je považován za povolený, pokud jsou v oboru zásady sspr. Pokud je povoleno přihlašování k tomuto číslu je povoleno pro skupinu, pak je uživatel považován za povolený, pokud jsou v této skupině. Pokud je povoleno přihlašování k internetu pro všechny uživatele, jsou všichni uživatelé v tenantovi (s výjimkou hostů) považováni za povolené.
- Schopný: Uživatel je považován za schopného, pokud jsou registrovány a povoleny. Tento stav znamená, že mohou v případě potřeby kdykoli provést sspr.

Kliknutím na některou z těchto dlaždic nebo na postřehy, které jsou v nich zobrazeny, se dostanete do předem filtrovaného seznamu registračních údajů.

Graf **Registrace** na kartě **Registrace** zobrazuje počet úspěšných a neúspěšných registrací metod ověřování metodou ověřování. Graf **Resetuje** na kartě **Využití** zobrazuje počet úspěšných a neúspěšných ověření během toku obnovení hesla metodou ověřování.

Kliknutím na některý z grafů se dostanete do předem filtrovaného seznamu událostí registrace nebo resetování.

Pomocí ovládacího prvku v pravém horním rohu můžete změnit rozsah dat auditu zobrazených v grafech Registrace a Resetuje na 24 hodin, 7 dní nebo 30 dní.

### <a name="registration-details"></a>Registrační údaje

Kliknutím na **uživatele registrované**, **Uživatelé povoleno**, nebo **Uživatelé schopný** dlaždice nebo přehledy vás přenese na registrační údaje.

Sestava podrobností o registraci zobrazuje pro každého uživatele následující informace:

- Název
- Uživatelské jméno
- Stav registrace (vše, registrováno, není registrováno)
- Stav Povolená (Vše, Povoleno, Nepovoleno)
- Schopný stav (vše, schopný, neschopný)
- Metody (oznámení aplikace, kód aplikace, telefonní hovor, SMS, e-mail, bezpečnostní otázky)

Pomocí ovládacích prvků v horní části seznamu můžete vyhledat uživatele a filtrovat seznam uživatelů na základě zobrazených sloupců.

### <a name="reset-details"></a>Obnovit podrobnosti

Kliknutím na grafy Registrace nebo Resetuje se dostanete k podrobnostem resetu.

Sestava podrobností o obnovení zobrazuje události registrace a obnovení za posledních 30 dní, včetně:

- Název
- Uživatelské jméno
- Funkce (vše, registrace, reset)
- Metoda ověřování (oznámení aplikace, kód aplikace, telefonní hovor, volání do Office, SMS, e-mail, bezpečnostní otázky)
- Stav (vše, úspěch, neúspěch)

Pomocí ovládacích prvků v horní části seznamu můžete vyhledat uživatele a filtrovat seznam uživatelů na základě zobrazených sloupců.

## <a name="limitations"></a>Omezení

Údaje uvedené v těchto zprávách budou zpožděny až o 60 minut. Na webu Azure Portal existuje pole "Poslední aktualizace", které identifikuje, jak poslední jsou vaše data.

Data o využití a přehledech nejsou náhradou za sestavy aktivit azure vícefaktorového ověřování nebo informace obsažené v sestavě přihlášení Azure AD.

Sestavu nelze aktuálně filtrovat, aby bylo možné vyloučit externí uživatele.

## <a name="next-steps"></a>Další kroky

- [Práce s rozhraním API pro použití metod ověřování](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Volba metod ověřování pro vaši organizaci](concept-authentication-methods.md)
- [Kombinovaná registrace](concept-registration-mfa-sspr-combined.md)
