---
title: Token, relace a konfigurace jednotného přihlašování v Azure Active Directory B2C | Dokumentace Microsoftu
description: Token, relace a konfigurace jednotného přihlašování v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 061e2257200b6d660a421a86c540f43597112c5e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337881"
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C: Token, relace a konfigurace jednotného přihlašování

Tato funkce umožňuje přesné řízení, na [podle zásad základ](active-directory-b2c-reference-policies.md), sady:

1. Životnost tokenů zabezpečení, protože ho vygeneroval Azure Active Directory (Azure AD) B2C.
2. Doby života relace webové aplikace spravované službou Azure AD B2C.
3. Formáty důležité deklarací identity v tokenech zabezpečení, protože ho vygeneroval Azure AD B2C.
4. Chování jednotného přihlašování (SSO) napříč více aplikacemi a zásadami v tenantovi B2C

Pro předdefinované zásady můžete tuto funkci ve vašem adresáři Azure AD B2C následujícím způsobem:

1. Postupujte podle těchto kroků [přejděte do nabídky funkce B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure portal.
2. Klikněte na tlačítko **zásady registrace nebo přihlášení**. * Poznámka: Pomocí této funkce na libovolný typ zásad, nejen na ** registrace nebo přihlašování zásady ***.
3. Otevřete zásadu kliknutím. Klikněte například na **B2C_1_SiUpIn**.
4. Klikněte na tlačítko **upravit** v horní nabídce.
5. Klikněte na tlačítko **Token, relace a konfigurace přihlašování**.
6. Proveďte požadované změny. Přečtěte si o dostupných vlastností v dalších částech.
7. Klikněte na **OK**.
8. Klikněte na tlačítko **Uložit** horní části nabídky.

## <a name="token-lifetimes-configuration"></a>Konfigurace životností tokenů

Azure AD B2C podporuje [protokol autorizace OAuth 2.0](active-directory-b2c-reference-protocols.md) pro zajišťují zabezpečený přístup k chráněným prostředkům. K implementaci této podpory, Azure AD B2C vysílá různých [tokeny zabezpečení](active-directory-b2c-reference-tokens.md). Toto jsou vlastnosti, které můžete použít ke správě životnosti tokenů zabezpečení, protože ho vygeneroval Azure AD B2C:

* **Přístup k tokenu a tokenu ID životnost (minuty)**: dobu života nosného tokenu OAuth 2.0 použít k získání přístupu k chráněnému prostředku.
  * Výchozí = 60 minut.
  * Minimální (včetně) = 5 minut.
  * Maximální (včetně) = 1 440 minut.
* **Doba života obnovovacího tokenu (dny)**: maximální dobu, před kterým obnovovací token slouží k získání nového přístupového tokenu nebo tokenu ID (a volitelně nového tokenu obnovení, pokud byl udělen vaší aplikace `offline_access` oboru).
  * Výchozí = 14 dní.
  * Minimální (včetně) = 1 den.
  * Maximální (včetně) = 90 dní.
* **Posuvné okno doba života obnovovacího tokenu (dny)**: po tomto časovém období uplyne uživatel bude muset znovu ověřovat, bez ohledu na období platnosti posledního obnovovací token získaný aplikace. To lze zadat pouze pokud přepínač nastavený na **ohraničená**. Musí být větší než nebo rovna hodnotě **aktualizace životnost tokenu (dny)** hodnotu. Pokud přepínač nastavený na **nástrojů Unbounded**, nemůže poskytnout konkrétní hodnotu.
  * Výchozí = 90 dní.
  * Minimální (včetně) = 1 den.
  * Maximální (včetně) = 365 dnů.

Těchto několik případů použití, které můžete povolit pomocí těchto vlastností:

* Umožní uživateli zůstat přihlášený do mobilní aplikace po neomezenou dobu, za předpokladu, uživatel je stále aktivní na aplikaci. Můžete to provést tak, že nastavíte **obnovovací token doba života posuvného okna (dny)** přepnout na **nástrojů Unbounded** v registrační zásady.
* Splňovat požadavky na dodržování předpisů zabezpečení vaše odvětví a nastavením životností tokenů odpovídající přístup.

    > [!NOTE]
    > Tato nastavení nejsou k dispozici pro zásady resetování hesla.
    > 
    > 

## <a name="token-compatibility-settings"></a>Nastavení kompatibility tokenu

Jsme provedli změny formátování důležité deklarací identity v tokenech zabezpečení, protože ho vygeneroval Azure AD B2C. To bylo provedeno vylepšení našich podpory standardního protokolu a pro lepší interoperabilitě s knihovnami identity třetí strany. Však pokud chcete vyhnout přerušení existující aplikace, jsme vytvořili, tak, aby zákazníci k vyjádření výslovného souhlasu podle potřeby následující vlastnosti:

* **Deklarace identity vystavitele (iss)**: Určuje tenanta Azure AD B2C, která token vydala.
  * `https://<domain>/{B2C tenant GUID}/v2.0/`: Toto je výchozí hodnota.
  * `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: Tato hodnota zahrnuje ID tenanta B2C a zásady používané v požadavku na token. Pokud vaše aplikace nebo knihovna musí Azure AD B2C má být zajištěn soulad [zjišťování OpenID Connect 1.0 specifikace](http://openid.net/specs/openid-connect-discovery-1_0.html), používejte tuto hodnotu.
* **Deklarace identity subjektu (sub)**: Určuje, entity, například uživatel, pro kterou token vyhodnocuje informace.
  * **ID objektu**: Jedná se o výchozí hodnotu. Naplní ID objektu uživatele v adresáři do `sub` deklarací identity v tokenu.
  * **Nepodporuje**: to je k dispozici pouze z důvodů zpětné kompatibility, a doporučujeme vám, že přejdete na **ObjectID** Jakmile máte možnost.
* **Deklarace identity představující ID zásady**: Určuje typ deklarace identity, do které se vyplní ID zásady použité v požadavku na token.
  * **tfp**: Jedná se o výchozí hodnotu.
  * **acr**: to je k dispozici pouze z důvodů zpětné kompatibility, a doporučujeme vám, že přejdete na `tfp` Jakmile máte možnost.

## <a name="session-behavior"></a>Chování relace

Azure AD B2C podporuje [ověřovacího protokolu OpenID Connect](active-directory-b2c-reference-oidc.md) pro povolení zabezpečené přihlašování k webovým aplikacím. Toto jsou vlastnosti, které můžete použít ke správě relací webových aplikací:

* **Webová aplikace životnost relace (minuty)**: dobu života souboru cookie relace Azure AD B2C uloženého v prohlížeči uživatele po úspěšném ověření.
  * Výchozí = 1 440 minut.
  * Minimální (včetně) = 15 minut.
  * Maximální (včetně) = 1 440 minut.
* **Časový limit relace webové aplikace**: Pokud je tento přepínač nastavený **absolutní**, uživatel bude muset znovu ověřit po době určené **webové aplikace životnost relace (minuty)** uplyne. Pokud tento přepínač nastavený **Hromadná** (výchozí nastavení), uživatel zůstane přihlášený, za předpokladu, uživatel je neustále aktivní ve webové aplikaci.

Těchto několik případů použití, které můžete povolit pomocí těchto vlastností:

* Splnění požadavků na váš obor zabezpečení a dodržování předpisů tím, že nastavíte relace webové aplikace životnosti.
* Vynuťte opětovné ověření po stanovené časové období, během interakce uživatele s vysokou úrovní zabezpečení součást webové aplikace. 

    > [!NOTE]
    > Tato nastavení nejsou k dispozici pro zásady resetování hesla.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Konfigurace jednotného přihlašování (SSO)
Pokud máte více aplikací a zásad ve vašem tenantovi B2C, můžete spravovat uživatelské interakce mezi nimi pomocí **Konfigurace jednotného přihlašování** vlastnost. Vlastnost můžete nastavit na jedno z následujících nastavení:

* **Tenant**: Jedná se o výchozí nastavení. Použití toto nastavení umožňuje více aplikací a zásad ve vašem tenantovi B2C sdílet stejné uživatelské relaci. Například Jakmile se uživatel přihlásí do aplikace, nákupní Contoso, nezíská můžete také bezproblémově přihlášení do jiného jeden, farmacie Contoso při přístupu k jeho.
* **Aplikace**: umožňuje udržovat relaci uživatele výhradně pro aplikaci, nezávisle na jiných aplikací. Například pokud chcete uživateli umožní přihlásit k farmacie Contoso (pomocí stejných přihlašovacích údajů), i v případě, že uživatel je už přihlásili do nákupního Contoso, jiná aplikace na stejném B2C tenanta. 
* **Zásady**: umožňuje udržovat relaci uživatele výhradně pro zásady, nezávisle na aplikace, které ji používají. Například pokud má uživatel již přihlášení a dokončení kroku více faktoru ověřování (MFA), uživatel může dostat přístup k různým částem vyšší zabezpečení více aplikací tak dlouho, dokud platnost pasu nevyprší relace vázané na zásady.
* **Zakázané**: To přinutí spustit prostřednictvím cesty celý uživatele na každé provedení zásad. Například to umožňuje více uživatelům k registraci do vaší aplikace (v případě sdílené klasické pracovní plochy), i při jeden uživatel zůstane přihlášený po celou dobu.

    > [!NOTE]
    > Tato nastavení nejsou k dispozici pro zásady resetování hesla.
    > 
    > 

