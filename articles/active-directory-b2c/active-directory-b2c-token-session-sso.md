---
title: Token, relace a konfiguraci jednoho přihlášení v Azure Active Directory B2C | Microsoft Docs
description: Token, relace a konfiguraci jednoho přihlášení v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0ee39f4b6f80d13cc0f71c77ae87b2a4ee74e390
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710630"
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C: Token, relace a jednotné přihlašování

Tato funkce poskytuje jemně odstupňovanou kontrolu na [-policy základ](active-directory-b2c-reference-policies.md), z:

1. Životnosti vysílaných Azure Active Directory (Azure AD) B2C tokenů zabezpečení.
2. Životnosti relací webové aplikace spravovat přes Azure AD B2C.
3. Formáty důležité deklarací identity v tokenech zabezpečení vygenerované pomocí Azure AD B2C.
4. Jednotné přihlašování (SSO) chování napříč více aplikací a zásad v svého klienta B2C.

Pro předdefinované zásady můžete tuto funkci v adresáři Azure AD B2C následujícím způsobem:

1. Postupujte podle těchto kroků [přejděte do nabídky funkce B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
2. Klikněte na tlačítko **zásady registrace nebo přihlášení**. * Poznámka: Můžete použít tuto funkci na libovolného typu zásady, nejen na ** registrace nebo přihlášení zásady ***.
3. Kliknutím otevřete zásadu. Klikněte například na **B2C_1_SiUpIn**.
4. Klikněte na tlačítko **upravit** v horní nabídce.
5. Klikněte na tlačítko **Token, relace a konfigurace přihlášení**.
6. Proveďte požadované změny. Další informace o dostupných vlastností v následujících částech.
7. Klikněte na **OK**.
8. Klikněte na tlačítko **Uložit** horní nabídce.

## <a name="token-lifetimes-configuration"></a>Konfigurace životnosti tokenu

Azure AD B2C podporuje [protokol autorizace OAuth 2.0](active-directory-b2c-reference-protocols.md) pro povolení zabezpečený přístup k chráněným prostředkům. Implementace této podpory, Azure AD B2C vysílá různé [tokeny zabezpečení](active-directory-b2c-reference-tokens.md). Jedná se o vlastnosti, které můžete použít ke správě životnosti vygenerované pomocí Azure AD B2C tokenů zabezpečení:

* **Přístup k & ID token životnosti (minuty)**: doba platnosti tokenu nosiče OAuth 2.0 použít k získání přístupu k chráněnému prostředku.
  * Výchozí = 60 minut.
  * Minimální (včetně) = 5 minut.
  * Maximální počet (včetně) = 1 440 minut.
* **Aktualizujte životnost tokenu (dny)**: maximální časové období, před kterou obnovovací token slouží k získání nového přístupu nebo ID token (a volitelně nový token obnovení, pokud byl udělen vaší aplikace `offline_access` oboru).
  * Výchozí = 14 dnů.
  * Minimální (včetně) = 1 den.
  * Maximální počet (včetně) = 90 dní.
* **Aktualizujte životnost tokenu posuvné okno (dny)**: po uplynutí tohoto časového období, uživatel bude muset znovu provést ověření, bez ohledu na dobu platnosti nejnovější aktualizace tokenu získali aplikací. Je možné poskytnout jenom Pokud je přepínač nastavený na **Bounded**. Musí být větší než nebo rovna hodnotě **životnost tokenu aktualizace (dny)** hodnotu. Pokud je přepínač nastavený na **nástrojů Unbounded**, nemůžete zadat konkrétní hodnotu.
  * Výchozí = 90 dní.
  * Minimální (včetně) = 1 den.
  * Maximální počet (včetně) = 365 dnů.

Toto je několik případů použití, které můžete povolit pomocí těchto vlastností:

* Tak dlouho, dokud se průběžně active na aplikaci, aby uživatel zůstane po neomezenou dobu, podepsaný do mobilní aplikace. To provedete nastavením **aktualizace posuvné okno dobu životnosti tokenu (dny)** přepnout **nástrojů Unbounded** v zásadách vaše přihlášení.
* Splňují vaše odvětví zabezpečení a požadavky na dodržování předpisů nastavením životnosti tokenu odpovídající přístup.

    > [!NOTE]
    > Tato nastavení nejsou k dispozici pro zásady resetování hesla.
    > 
    > 

## <a name="token-compatibility-settings"></a>Nastavení kompatibility tokenů

Jsme provedli změny formátování důležité deklarací identity v tokenech zabezpečení vygenerované pomocí Azure AD B2C. K tomu bylo potřeba pro zlepšení naše standardní protokol podporu a pro lepší spolupráci s knihovny identity jiných výrobců. Ale pokud chcete vyhnout pozastavení existující aplikace, jsme vytvořili umožňuje zákazníkům výslovný souhlas podle potřeby následující vlastnosti:

* **Deklarace identity vystavitele (iss)**: Toto identifikuje klienta Azure AD B2C, která vydala token.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`: Toto je výchozí hodnota.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: Tato hodnota zahrnuje ID pro klienta B2C a zásady používané v požadavku na token. Pokud vaše aplikace nebo knihovny musí Azure AD B2C splňovat [OpenID Connect 1.0 zjišťování specifikace](http://openid.net/specs/openid-connect-discovery-1_0.html), používejte tuto hodnotu.
* **Deklarace identity subjektu (pod)**: Toto identifikuje entity, například uživatele, u kterého je token vyhodnotí informace.
  * **ObjectID**: Toto je výchozí hodnota. Vyplní objekt ID uživatele v adresáři do `sub` deklarací identity v tokenu.
  * **Nepodporuje**: to je k dispozici pouze z důvodů zpětné kompatibility, a doporučujeme vám, že přejdete na **ObjectID** také budete moci.
* **Deklarace identity představující ID zásad**: Toto identifikuje typ deklarace identity, do kterého se naplní ID zásady použité v požadavku na token.
  * **tfp**: Toto je výchozí hodnota.
  * **acr**: to je k dispozici pouze z důvodů zpětné kompatibility, a doporučujeme vám, že přejdete na `tfp` také budete moci.

## <a name="session-behavior"></a>Chování relace

Azure AD B2C podporuje [ověřovacího protokolu OpenID Connect](active-directory-b2c-reference-oidc.md) pro povolení bezpečného přihlášení k webovým aplikacím. Jedná se o vlastnosti, které můžete použít ke správě relací webové aplikace:

* **Webovou aplikaci dobu platnosti relace (minuty)**: životnost uložené na prohlížeče uživatele po úspěšném ověření souboru cookie relace Azure AD B2C.
  * Výchozí = 1 440 minut.
  * Minimální (včetně) = 15 minut.
  * Maximální počet (včetně) = 1 440 minut.
* **Časový limit relace webové aplikace**: Pokud je tento přepínač nastavený na **absolutní**, uživatel bude muset znovu provést ověření po časové období určeného **webové aplikace dobu platnosti relace (minuty)** uplynutí. Pokud je tento přepínač nastavený na **kolejová** (výchozí nastavení), tak dlouho, dokud uživatel je ve vaší webové aplikaci neustále aktivní zůstane přihlášeného uživatele.

Toto je několik případů použití, které můžete povolit pomocí těchto vlastností:

* Splňovat požadavky na zabezpečení a dodržování předpisů vaší odvětví nastavením odpovídající webové aplikace relace životnosti.
* Vynutí opětovné ověření po nastaveném časovém období, během interakce uživatele s vysokou úrovní zabezpečení součást webové aplikace. 

    > [!NOTE]
    > Tato nastavení nejsou k dispozici pro zásady resetování hesla.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Konfigurace jednotného přihlašování (SSO)
Pokud máte víc aplikací a zásad v svého klienta B2C, můžete spravovat interakcí mezi nimi pomocí **konfigurace přihlášení** vlastnost. Vlastnost můžete nastavit na jedno z následujících nastavení:

* **Klienta**: Jedná se o výchozí nastavení. Použití tohoto nastavení umožňuje více aplikací a zásad v svého klienta B2C sdílet stejné uživatelské relace. Například Jakmile se uživatel přihlásí do aplikace, nákupního Contoso, ale můžete také bezproblémově Přihlaste se k jiné jeden, farmacie Contoso, při k ní přistupují.
* **Aplikace**: to umožňuje udržovat relaci uživatele výhradně pro aplikaci, nezávisle na jiné aplikace. Například pokud chcete uživateli přihlášení k farmacie Contoso (pomocí stejných přihlašovacích údajů), i v případě, že uživatel je již přihlášen k Contoso Shopping, jiná aplikace ve stejném B2C klienta. 
* **Zásady**: to umožňuje udržovat relaci uživatele výhradně pro zásadu, nezávisle na aplikace, které ji používají. Například pokud uživatel již přihlášení a dokončení krok více Multi-Factor authentication (MFA), uživatel může být poskytnut přístup k vyšší zabezpečení částí více aplikací tak dlouho, dokud nevyprší relace vázané na zásady.
* **Zakázané**: Vynutí uživatele k spuštění prostřednictvím celé uživatelské cesty na každé provedení zásad. Například to umožňuje více uživatelům zaregistrovat do vaší aplikace (ve scénáři sdílené klientů), i při jednoho uživatele zůstane přihlášeného během po celou dobu.

    > [!NOTE]
    > Tato nastavení nejsou k dispozici pro zásady resetování hesla.
    > 
    > 

