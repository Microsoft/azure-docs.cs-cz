---
title: Token, relace a konfigurace jednotného přihlašování v Azure Active Directory B2C | Dokumentace Microsoftu
description: Token, relace a konfigurace jednotného přihlašování v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c93bc018aea92a63adac4889d9496356543c1e52
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842726"
---
# <a name="token-session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Token, relace a konfigurace jednotného přihlašování v Azure Active Directory B2C

Tato funkce umožňuje přesné řízení, na [tok jednotlivých uživatelů](active-directory-b2c-reference-policies.md), sady:

- Životnost tokenů zabezpečení, protože ho vygeneroval Azure Active Directory (Azure AD) B2C.
- Doby života relace webové aplikace spravované službou Azure AD B2C.
- Formáty důležité deklarací identity v tokenech zabezpečení, protože ho vygeneroval Azure AD B2C.
- Jednotné přihlašování (SSO) chování napříč více aplikacemi a toky uživatelů ve vašem tenantovi Azure AD B2C.

Tuto funkci můžete použít na libovolný typ zásad, ale v tomto příkladu ukazují, jak použít funkci s tokem registrace nebo přihlašování uživatelů. Pro toky uživatelů můžete tuto funkci ve vašem adresáři Azure AD B2C následujícím způsobem:

1. Klikněte na tlačítko **toky uživatelů**.
2. Kliknutím otevřete tok uživatele. Klikněte například na **B2C_1_SiUpIn**.
3. Klikněte na **Vlastnosti**.
4. V části **Token nastavení kompatibility**, proveďte požadované změny. Přečtěte si o dostupných vlastností v dalších částech.
5. Klikněte na tlačítko **Uložit** horní části nabídky.

## <a name="token-lifetimes-configuration"></a>Konfigurace životností tokenů

Azure AD B2C podporuje [protokol autorizace OAuth 2.0](active-directory-b2c-reference-protocols.md) pro zajišťují zabezpečený přístup k chráněným prostředkům. K implementaci této podpory, Azure AD B2C vysílá různých [tokeny zabezpečení](active-directory-b2c-reference-tokens.md). 

Následující vlastnosti se používají ke správě životnosti tokenů zabezpečení, protože ho vygeneroval Azure AD B2C:

- **Přístup k tokenu a tokenu ID životnost (minuty)** – dobu života nosného tokenu OAuth 2.0, který se používá k získání přístupu k chráněnému prostředku.
    - Výchozí = 60 minut.
    - Minimální (včetně) = 5 minut.
    - Maximální (včetně) = 1 440 minut.
- **Doba života obnovovacího tokenu (dny)** – maximální dobu, před kterým obnovovací token slouží k získání nového přístupového tokenu nebo tokenu ID (a volitelně nového tokenu obnovení, pokud byl udělen vaší aplikace `offline_access` oboru).
    - Výchozí = 14 dní.
    - Minimální (včetně) = 1 den.
    - Maximální (včetně) = 90 dní.
- **Posuvné okno doba života obnovovacího tokenu (dny)** – po uplynutí tohoto času období uživatel bude muset znovu ověřovat, bez ohledu na období platnosti posledního obnovovací token získaný aplikace. To lze zadat pouze pokud přepínač nastavený na **ohraničená**. Musí být větší než nebo rovna hodnotě **aktualizace životnost tokenu (dny)** hodnotu. Pokud přepínač nastavený na **nástrojů Unbounded**, nemůže poskytnout konkrétní hodnotu.
    - Výchozí = 90 dní.
    - Minimální (včetně) = 1 den.
    - Maximální (včetně) = 365 dnů.

Tyto případy použití jsou povolené použití těchto vlastností:

- Umožní uživateli se zachovat po neomezenou dobu, přihlášení k mobilní aplikaci, jako je uživatel stále aktivní na aplikaci. Můžete nastavit **obnovovací token doba života posuvného okna (dny)** k **nástrojů Unbounded** ve svém toku přihlášení uživatele.
- Splňovat požadavky na dodržování předpisů zabezpečení vaše odvětví a nastavením životností tokenů odpovídající přístup.

Tato nastavení nejsou k dispozici pro toky uživatelů pro resetování hesla. 

## <a name="token-compatibility-settings"></a>Nastavení kompatibility tokenu

Tyto vlastnosti umožňují zákazníkům se vyjádřit výslovný souhlas podle potřeby:

- **Deklarace identity vystavitele (iss)** – tato vlastnost identifikuje tenanta Azure AD B2C, která token vydala.
    - `https://<domain>/{B2C tenant GUID}/v2.0/` – Toto je výchozí hodnota.
    - `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` – Tato hodnota zahrnuje ID tenanta B2C a uživatelského tok používaný v požadavku na token. Pokud vaše aplikace nebo knihovna musí Azure AD B2C má být zajištěn soulad [zjišťování OpenID Connect 1.0 specifikace](https://openid.net/specs/openid-connect-discovery-1_0.html), používejte tuto hodnotu.
- **Deklarace identity subjektu (sub)** – tato vlastnost identifikuje entitu, pro kterou token vyhodnocuje informace.
    - **ID objektu** – tato vlastnost je výchozí hodnota. Naplní ID objektu uživatele v adresáři do `sub` deklarací identity v tokenu.
    - **Nepodporuje** – tato vlastnost je poskytována pouze z důvodů zpětné kompatibility, a doporučujeme vám, že přejdete na **ObjectID** Jakmile máte možnost.
- **Deklarace identity představující ID zásady** – tato vlastnost identifikuje typ deklarace identity, do které se vyplní ID zásady použité v požadavku na token.
    - **tfp** – tato vlastnost je výchozí hodnota.
    - **acr** – tato vlastnost je poskytována pouze z důvodů zpětné kompatibility.

## <a name="session-behavior"></a>Chování relace

Azure AD B2C podporuje [ověřovacího protokolu OpenID Connect](active-directory-b2c-reference-oidc.md) pro povolení zabezpečené přihlašování k webovým aplikacím. Chcete-li spravovat relace webové aplikace můžete použít následující vlastnosti:

- **Webová aplikace životnost relace (minuty)** – dobu života souboru cookie relace Azure AD B2C uloženého v prohlížeči uživatele po úspěšném ověření.
    - Výchozí = 1 440 minut.
    - Minimální (včetně) = 15 minut.
    - Maximální (včetně) = 1 440 minut.
- **Časový limit relace webové aplikace** – Pokud je tento přepínač nastavený **absolutní**, že uživatel musí k ověření znovu po době určené **webové aplikace životnost relace (minuty)** uplyne. Pokud tento přepínač nastavený **Hromadná** (výchozí nastavení), uživatel zůstane přihlášený, za předpokladu, uživatel je neustále aktivní ve webové aplikaci.

Tyto případy použití jsou povolené použití těchto vlastností:

- Splnění požadavků na váš obor zabezpečení a dodržování předpisů tím, že nastavíte relace webové aplikace životnosti.
- Vynutit ověřování po stanovené časové období, během interakce uživatele s vysokou úrovní zabezpečení součást webové aplikace. 

Tato nastavení nejsou k dispozici pro toky uživatelů pro resetování hesla.

## <a name="single-sign-on-sso-configuration"></a>Konfigurace jednotného přihlašování (SSO)

Pokud máte více aplikacemi a toky uživatelů ve vašem tenantovi B2C, můžete spravovat uživatelské interakce mezi nimi pomocí **Konfigurace jednotného přihlašování** vlastnost. Vlastnost můžete nastavit na jedno z následujících nastavení:

- **Tenant** – toto nastavení je výchozí nastavení. Použití toto nastavení umožňuje více aplikací a uživatel toků ve vašem tenantovi B2C sdílet stejné uživatelské relaci. Například Jakmile se uživatel přihlásí do aplikace, uživatel může také bezproblémově přihlášení do jiného jeden, farmacie Contoso při přístupu k jeho.
- **Aplikace** – toto nastavení umožňuje udržovat relaci uživatele výhradně pro aplikaci, nezávisle na jiných aplikací. Například pokud chcete uživateli umožní přihlásit k farmacie Contoso (pomocí stejných přihlašovacích údajů), i když už je uživatel přihlášený do nákupního Contoso, jiná aplikace na stejném B2C tenanta. 
- **Zásady** – toto nastavení umožňuje udržovat relaci uživatele výhradně pro tok uživatele, nezávisle na aplikace, které ji používají. Například pokud má uživatel již přihlášení a dokončení kroku více faktoru ověřování (MFA), uživatel může dostat přístup k různým částem vyšší zabezpečení více aplikací tak dlouho, dokud platnost pasu nevyprší relace vázané na tok uživatele.
- **Zakázané** – toto nastavení přinutí spustit prostřednictvím toku celé uživatelské na každé provedení zásad. Například to umožňuje více uživatelům k registraci do vaší aplikace (v případě sdílené klasické pracovní plochy), i při jeden uživatel zůstane přihlášený po celou dobu.

Tato nastavení nejsou k dispozici pro toky uživatelů pro resetování hesla. 

