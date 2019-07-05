---
title: Používání metody ověřování & insights vytváření sestav (preview) – Azure Active Directory
description: Vytváření sestav v Azure AD hesla pomocí samoobslužné služby resetování a použití metody ověřování služby Multi-Factor Authentication
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
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561030"
---
# <a name="authentication-methods-usage--insights-preview"></a>Používání metody ověřování & insights (preview)

Využití & insights umožňuje porozumět fungování metod ověřování pro funkce, jako je Azure Multi-Factor Authentication a samoobslužné resetování hesla ve vaší organizaci. Tato funkce generování sestav poskytuje způsob, jak pochopit, jaké metody jsou registrovány a jak se právě používá vaše organizace.

## <a name="permissions-and-licenses"></a>Oprávnění a licencí

Tyto role můžete přístup k využití a insights:

- Globální správce
- Čtenář zabezpečení
- Správce zabezpečení
- Čtečka sestav

Žádné další licence se vyžaduje přístup k využití a přehledy. Azure Multi-Factor Authentication a informace o licencování samoobslužného resetování hesla můžete najít na [cenami služby Azure Active Directory web](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Jak to funguje

Pro přístup k použití metody ověřování a insights:

1. Přejděte na web [Azure Portal](https://portal.azure.com).
1. Přejděte do **Azure Active Directory** > **resetování hesla** > **dat o využití a insights**.
1. Z **registrace** nebo **využití** přehledy, můžete také otevřít předem filtrované sestavy pro filtrování podle svých potřeb.

![Přehled dat o využití a přehledy](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Přímý přístup dat o využití a přehledy, přejděte na [ https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Tento odkaz přejdete na přehled registrace.

Uživatelé zaregistrovaný, uživatelé povoleni a dlaždice umožňující uživatelům zobrazují následující registrační data pro vaše uživatele:

- Zaregistrovaný: Uživatel se považuje za zaregistrovaný, pokud jsou (nebo správcem) jste se zaregistrovali dostatek metody ověřování pro splnění zásad vaší organizace samoobslužné resetování HESLA nebo ověřování službou Multi-Factor Authentication.
- Povoleno: Uživatel se považuje za povolená, pokud jsou v oboru pro zásad samoobslužného resetování HESLA. Pokud je samoobslužné resetování HESLA je povoleno pro skupinu, uživatel se považuje za povolená, pokud jsou v této skupině. Pokud je samoobslužné resetování HESLA je povoleno pro všechny uživatele, všechny uživatele v tenantovi (s výjimkou Hosté) jsou považovány za povolena.
- Podporuje: Uživatel se považuje za podporuje, pokud jsou obě zaregistrovaný a povolený. Tento stav znamená, že mohou provádět samoobslužné resetování HESLA v každém okamžiku v případě potřeby.

Kliknutím na některý z těchto dlaždic nebo takové informace uvedené v nich vás přivedou k předem filtrovaný seznam podrobnosti registrace.

**Registrace** na graf **registrace** karta zobrazuje počet ověření úspěšné i neúspěšné metoda registrace metodou ověřování. **Resetuje** na graf **využití** karta zobrazuje počet úspěšných a neúspěšných ověření během heslo resetovat tok ověřování metodou.

Kliknutím na některý z grafů přivedou k předem filtrovaný seznam registraci nebo resetování události.

Použití ovládacího prvku v horním horní, pravé, můžete změnit období pro audit dat zobrazí v grafech registrace a resetování až 24 hodin, 7 dní nebo 30 dní.

Registrační data z 

### <a name="registration-details"></a>Podrobnosti registrace

Kliknutím na **uživatele registrované**, **uživatele s povoleným**, nebo **umožňující uživatelům** dlaždice nebo insights přejdete na podrobnosti registrace.

Sestava podrobnosti o registraci se zobrazí následující informace pro každého uživatele:

- Název
- Uživatelské jméno
- Stav registrace (všechny, zaregistrovat, není registrován.)
- Povolený stav (všechny, povolené, nejsou povolena)
- Podporuje stav (všechny, podporující, není schopné)
- Metody (oznámení aplikace, kód aplikace, telefonátu nebo SMS, e-mailu, bezpečnostní otázky)

Použití ovládacích prvků v horní části seznamu, můžete vyhledat uživatele a filtrovat seznam uživatelů, které jsou založeny na sloupcích zobrazeny.

### <a name="reset-details"></a>Podrobnosti o resetování

Kliknutím na grafy registrace nebo obnovení nastavení přejdete na podrobnosti o obnovení.

Sestava podrobnosti o resetování ukazuje registraci a resetování události za posledních 30 dní, včetně:

- Název
- Uživatelské jméno
- Funkce (vše, registraci, obnovení)
- Metoda ověřování (oznámení aplikace, kód aplikace, telefonního hovoru, volání do kanceláře, SMS, e-mailu, bezpečnostní otázky)
- Stav (vše, úspěch, chyba)

Použití ovládacích prvků v horní části seznamu, můžete vyhledat uživatele a filtrovat seznam uživatelů, které jsou založeny na sloupcích zobrazeny.

## <a name="limitations"></a>Omezení

Data zobrazená v těchto sestavách bude zpozdit až 60 minut. Pole "Poslední aktualizace" existuje na webu Azure Portal k identifikaci posledních jsou vaše data.

Data o využití a insights není to náhrada sestavy o aktivitách Azure Multi-Factor Authentication nebo informací obsažených v sestavě přihlášení Azure AD.

## <a name="next-steps"></a>Další postup

- [Práce s sestavu využití metody ověřování rozhraní API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Volba metody ověřování pro vaši organizaci](concept-authentication-methods.md)
- [Kombinované způsobu registrace.](concept-registration-mfa-sspr-combined.md)
