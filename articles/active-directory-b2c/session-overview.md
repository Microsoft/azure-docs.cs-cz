---
title: Relace jednotného přihlašování v Azure Active Directory B2C | Microsoft Docs
description: Přehled konfigurace chování relace v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e02323df3a12c4a74de1fb62b36762fc739e9e5
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750435"
---
# <a name="azure-ad-b2c-session"></a>Azure AD B2C relace

Jednotné přihlašování (SSO) zvyšuje zabezpečení a pohodlí, když se uživatelé přihlásí přes aplikace v Azure Active Directory B2C (Azure AD B2C). Tento článek popisuje metody jednotného přihlašování používané v Azure AD B2C a při konfiguraci zásad vám pomůže vybrat nejvhodnější metodu jednotného přihlašování.

S jednotným přihlašováním se uživatelé přihlásí jedním účtem a získají přístup k více aplikacím. Aplikace může být webová, mobilní nebo jednostránková aplikace, bez ohledu na platformu nebo název domény.

Když se uživatel poprvé přihlásí k aplikaci, Azure AD B2C trvá relaci založenou na souborech cookie. Po následné žádosti o ověření Azure AD B2C přečte a ověří relaci založenou na souborech cookie a vydá přístupový token bez vyzvání uživatele k opětovnému přihlášení. Pokud platnost relace na základě souborů cookie vyprší nebo se stala neplatnou, zobrazí se uživateli výzva k opětovnému přihlášení.  

## <a name="sso-session-types"></a>Typy relací jednotného přihlašování

Integrace s Azure AD B2C zahrnuje tři typy relací jednotného přihlašování:

- **Azure AD B2C** – relace spravovaná pomocí Azure AD B2C
- **Federovaný zprostředkovatel identity** – relace spravovaná zprostředkovatelem identity, například Facebook, Salesforce nebo účet Microsoft
- **Aplikace** – relace spravovaná webovými, mobilními nebo jednostránkové aplikací

![Relace SSO](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C jednotného přihlašování 

Když se uživatel úspěšně ověřuje pomocí místního nebo sociálního účtu, Azure AD B2C ukládá relaci založenou na souborech cookie v prohlížeči uživatele. Soubor cookie je uložen pod názvem Azure AD B2C domény klienta, například `https://contoso.b2clogin.com` .

Pokud se uživatel poprvé přihlásí pomocí federovaného účtu a v časovém intervalu relace (Time-to-Live nebo TTL) se přihlásí ke stejné aplikaci nebo jiné aplikaci, Azure AD B2C se pokusí získat nový přístupový token od zprostředkovatele federovaných identit. Pokud vypršela platnost relace federovaného zprostředkovatele identity nebo je neplatný, vyzve se k zadání přihlašovacích údajů k uživateli federovaný zprostředkovatel identity. Pokud je relace stále aktivní (nebo pokud se uživatel přihlásil pomocí místního účtu místo federovaného účtu), Azure AD B2C autorizuje uživatele a eliminuje další výzvy.

Můžete nakonfigurovat chování relace, včetně TTL relace a způsobu, jakým Azure AD B2C sdílet relaci napříč zásadami a aplikacemi.

### <a name="federated-identity-provider-sso"></a>Jednotné přihlašování pro federované identity provider

Poskytovatel identity pro sociální sítě nebo organizace spravuje svou vlastní relaci. Soubor cookie je uložen pod názvem domény poskytovatele identity, například `https://login.salesforce.com` . Azure AD B2C neřídí relaci federovaného zprostředkovatele identity. Místo toho se chování relace určuje podle federovaného zprostředkovatele identity. 

Představte si následující scénář:

1. Uživatel se přihlásí do Facebooku, aby zkontroloval svůj informační kanál.
2. Později uživatel otevře vaši aplikaci a spustí proces přihlášení. Aplikace přesměruje uživatele na Azure AD B2C k dokončení procesu přihlašování.
3. Na stránce Azure AD B2C registrace nebo přihlášení se uživatel rozhodne přihlašovat pomocí účtu Facebook. Uživatel se přesměruje na Facebook. Pokud je aktivní relace na Facebooku, uživateli se nezobrazí výzva k zadání přihlašovacích údajů a okamžitě se přesměruje na Azure AD B2C s tokenem Facebooku.

### <a name="application-sso"></a>Jednotné přihlašování aplikace

Aplikaci pro web, mobilní nebo jednoduchou stránku lze chránit pomocí přístupu OAuth, tokeny ID nebo tokeny SAML. Když se uživatel pokusí o přístup k chráněnému prostředku v aplikaci, aplikace zkontroluje, jestli je na straně aplikace aktivní relace. Pokud nedošlo k žádné relaci aplikace nebo vypršela platnost relace, aplikace převezme uživatele, aby Azure AD B2C přihlašovací stránku.

Relace aplikace může být relace založená na souborech cookie uložená v názvu domény aplikace, jako je například `https://contoso.com` . Mobilní aplikace můžou relaci ukládat jiným způsobem, ale s podobným přístupem.

## <a name="azure-ad-b2c-session-configuration"></a>Konfigurace relace Azure AD B2C

### <a name="session-scope"></a>Rozsah relace

Relaci Azure AD B2C lze nakonfigurovat s následujícími rozsahy:

- **Tenant** – toto nastavení je výchozí. Pomocí tohoto nastavení může více aplikací a uživatelských toků v tenantovi B2C sdílet stejnou relaci uživatele. Když se například uživatel přihlásí do aplikace, může se uživatel při přístupu k němu bez problémů přihlásit i k jinému.
- **Aplikace** – toto nastavení umožňuje udržovat uživatelskou relaci exkluzivně pro aplikaci, nezávisle na jiných aplikacích. Toto nastavení můžete například použít, pokud chcete, aby se uživatel přihlásil ke společnosti Contoso farmacie bez ohledu na to, jestli je uživatel už přihlášený k nákupům společnosti Contoso.
- **Zásady** – toto nastavení umožňuje udržovat relaci uživatele výhradně pro uživatelský tok, a to nezávisle na aplikacích, které ji používají. Pokud se například uživatel už přihlásil a dokončil krok vícefaktorového ověřování (MFA), může se uživateli udělit přístup k několika částem aplikace s vyšším zabezpečením, pokud relace vázaná na tok uživatele nevyprší.
- **Disabled** – toto nastavení vynutí, aby uživatel při každém spuštění zásady spouštěl celý tok uživatele.

### <a name="session-life-time"></a>Doba života relace

**Doba životnosti relace** je doba, po kterou se soubor cookie relace Azure AD B2C po úspěšném ověření uloží do prohlížeče uživatele. Dobu života relace můžete nastavit na hodnotu od 15 do 720 minut.

### <a name="keep-me-signed-in"></a>Zůstat přihlášeni

Funkce [Keep jsem přihlášená](custom-policy-keep-me-signed-in.md) prodlouží dobu života relace pomocí trvalého souboru cookie. Relace zůstane aktivní, jakmile uživatel zavře a znovu otevře prohlížeč. Relace se odvolá jenom v případě, že se uživatel odhlásí. Funkce Keep jsem přihlášená platí jenom pro přihlášení pomocí místních účtů.

Funkce Keep jsem přihlášená má přednost před časem životnosti relace. Pokud je zapnutá funkce zůstat přihlášená a uživatel ji vybere, tato funkce určí, kdy vyprší platnost relace. 

### <a name="session-expiry-type"></a>Typ vypršení platnosti relace

**Typ vypršení platnosti relace** indikuje, jak je relace rozšířena nastavením doby života relace nebo nastavením zůstat přihlášení.

- **Rolling** Označuje, že relace je rozšířená pokaždé, když uživatel provede ověřování na základě souborů cookie (výchozí).
- **Absolutní** – označuje, že se uživatel bude nucen znovu ověřit po zadaném časovém období.

## <a name="sign-out"></a>Odhlášení

Pokud chcete uživatele podepsat z aplikace, nestačí vymazat soubory cookie aplikace nebo jinak ukončit relaci s uživatelem. Chcete-li se odhlásit, je nutné přesměrovat uživatele na Azure AD B2C. V opačném případě se může stát, že uživatel bude moci znovu ověřit aplikace bez zadání přihlašovacích údajů.

Po žádosti o odhlášení Azure AD B2C:

1. Zruší platnost Azure AD B2C relace na základě souborů cookie.
1. Pokusy o odhlášení od federovaných zprostředkovatelů identity:
   - OpenId Connect – Pokud se ve správném koncovém bodu konfigurace poskytovatele identity Určuje `end_session_endpoint` umístění.
   - OAuth2 – Pokud [metadata zprostředkovatele identity](oauth2-technical-profile.md#metadata) obsahují `end_session_endpoint` umístění.
   - SAML – Pokud [metadata zprostředkovatele identity](saml-identity-provider-technical-profile.md#metadata) obsahují `SingleLogoutService` umístění.
1. Volitelně můžete odhlásit z jiných aplikací. Další informace najdete v části věnované [jednotnému odhlašování](#single-sign-out) .

> [!NOTE]
> Pomocí [vlastních zásad](custom-policy-overview.md)můžete zakázat odhlašování od federovaných zprostředkovatelů identity tím, že nastavíte metadata technického profilu zprostředkovatele identity `SingleLogoutEnabled` na `false` .

Odhlášení vymaže stav jednotného přihlašování uživatele s Azure AD B2C, ale nemusí uživatele podepsat z relace sociální identity Provider. Pokud uživatel vybere stejného poskytovatele identity během následného přihlášení, může se znovu ověřit bez zadání přihlašovacích údajů. Pokud se uživatel chce z aplikace odhlásit, neznamená to nutně, že se chce odhlásit z účtu Facebook. Pokud se ale použijí místní účty, relace uživatele se ukončí správně.

### <a name="single-sign-out"></a>Jednotné odhlašování 


> [!NOTE]
> Tato funkce je omezená na [vlastní zásady](custom-policy-overview.md).

Když uživatele přesměrujete na koncový bod Azure AD B2Cho odhlašování (pro protokoly OAuth2 i SAML), Azure AD B2C vymaže relaci uživatele z prohlížeče. Uživatel se však může stále přihlašovat k jiným aplikacím, které používají Azure AD B2C pro ověřování. Pokud chcete těmto aplikacím povolit, aby si vypnuli uživatele současně, Azure AD B2C odešle požadavek HTTP GET na zaregistrované `LogoutUrl` všechny aplikace, ke kterým je uživatel aktuálně přihlášený.


Aplikace musí na tuto žádost reagovat vymazáním jakékoli relace, která uživatele identifikuje a vrátí `200` odpověď. Pokud chcete v aplikaci podporovat jednotné přihlašování, musíte implementovat `LogoutUrl` v kódu vaší aplikace. 

## <a name="next-steps"></a>Další kroky

- Naučte se [Konfigurovat chování relace v toku uživatele](session-behavior.md).
- Naučte se [Konfigurovat chování relace ve vlastních zásadách](session-behavior-custom-policy.md).
