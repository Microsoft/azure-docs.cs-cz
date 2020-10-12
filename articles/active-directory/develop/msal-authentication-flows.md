---
title: Toky ověřování MSAL | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o tocích ověřování a grantech, které používá knihovna Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/08/2020
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 4a902ed53e92cd073d81626e80bdb3c8629ad072
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89437866"
---
# <a name="authentication-flows"></a>Toky ověřování

Knihovna Microsoft Authentication Library (MSAL) podporuje několik toků ověřování pro použití v různých scénářích aplikací.

| Tok | Popis | Použito v |
|--|--|--|
| [Autorizační kód](#authorization-code) | Používá se v aplikacích, které jsou nainstalované na zařízení, aby získal přístup k chráněným prostředkům, například k webovým rozhraním API. Umožňuje přidat přihlašování a přístup k rozhraní API k mobilním a desktopovým aplikacím. | [Desktopové aplikace](scenario-desktop-overview.md), [mobilní aplikace](scenario-mobile-overview.md), [webové aplikace](scenario-web-app-call-api-overview.md) |
| [Přihlašovací údaje klienta](#client-credentials) | Umožňuje přístup k prostředkům hostovaným na webu pomocí identity aplikace. Běžně se používá pro interakce mezi servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. | [Aplikace démonů](scenario-daemon-overview.md) |
| [Kód zařízení](#device-code) | Umožňuje uživatelům přihlašovat se ke vstupnímu zařízení s omezením, jako jsou například inteligentní televizní vysílání, zařízení IoT nebo tiskárna. | [Desktopové/mobilní aplikace](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Implicitní udělení](#implicit-grant) | Umožňuje aplikaci získat tokeny bez provedení výměny přihlašovacích údajů back-endu serveru. Umožňuje aplikaci přihlašovat uživatele, udržovat relaci a získávat tokeny do jiných webových rozhraní API, a to vše v kódu JavaScriptu klienta. | [Jednostránkové aplikace (SPA)](scenario-spa-overview.md) |
| [Jménem](#on-behalf-of) | Aplikace vyvolá službu nebo webové rozhraní API, které zase musí volat jinou službu nebo webové rozhraní API. Nápad je rozšířit identitu delegovaného uživatele a oprávnění prostřednictvím řetězce požadavků. | [Webová rozhraní API](scenario-web-api-call-api-overview.md) |
| [Uživatelské jméno a heslo](#usernamepassword) | Umožňuje aplikaci podepsat uživatele přímo pořízením hesla. Tento tok se nedoporučuje. | [Desktopové/mobilní aplikace](scenario-desktop-acquire-token.md#username-and-password) |
| [Integrované ověřování systému Windows](#integrated-windows-authentication) | Umožňuje aplikacím připojeným k doméně nebo Azure Active Directory (Azure AD) získat token v tichém režimu (bez interakce uživatelského rozhraní od uživatele). | [Desktopové/mobilní aplikace](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Jak každý tok generuje tokeny a kódy

V závislosti na tom, jak je vytvořená klientská aplikace, může použít jeden nebo více toků ověřování podporovaných platformou Microsoft identity. Tyto toky mohou vytvořit několik typů tokenů a také autorizační kódy a vyžadovat, aby jejich fungování vyžadovalo jiné tokeny.

| Tok                                                                               | Nutné            | id_token | přístupový token | aktualizovat token | autorizační kód |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [Tok autorizačního kódu](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [Přihlašovací údaje klienta](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (jenom aplikace) |               |                    |
| [Tok kódu zařízení](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [Implicitní tok](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [Tok On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md)                                | přístupový token        | x        | x            | x             |                    |
| [Uživatelské jméno a heslo](v2-oauth-ropc.md) (ROPC)                                       | uživatelské jméno & heslo | x        | x            | x             |                    |
| [Hybridní tok OIDC](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [Aktualizovat uplatnění tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | aktualizovat token       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>Interaktivní a neinteraktivní ověřování

Některé z těchto toků podporují získání interaktivního i neinteraktivního tokenu.

  - **Interactive** znamená, že uživatel může být vyzván k zadání vstupu. Například vyzvat uživatele k přihlášení, provést vícefaktorové ověřování (MFA) nebo udělit dalšímu souhlasu k prostředkům.
  - **Neinteraktivní**nebo *tiché*ověřování se pokusí získat token způsobem, ve kterém přihlašovací server *nemůže* vyzvat uživatele k zadání dalších informací.

Vaše aplikace založená na MSAL by se nejdřív měla pokusit o získání tokenu v *tichém režimu*a pak interaktivně jenom v případě, že neinteraktivní metoda nefunguje. Další informace o tomto modelu naleznete v tématu [získání a mezipaměť tokenů pomocí knihovny Microsoft Authentication Library (MSAL)](msal-acquire-cache-tokens.md).

## <a name="authorization-code"></a>Autorizační kód

[Udělení autorizačního kódu OAuth 2](v2-oauth2-auth-code-flow.md) se dá použít v aplikacích, které jsou nainstalované na zařízení, aby získal přístup k chráněným prostředkům, jako jsou webová rozhraní API. Díky tomu můžete přidat přihlašování a přístup k rozhraní API pro mobilní a desktopové aplikace.

Když se uživatelé přihlásí k webovým aplikacím (websites), obdrží webová aplikace autorizační kód. Autorizační kód se považuje za získání tokenu pro volání webových rozhraní API.

![Diagram toku autorizačního kódu](media/msal-authentication-flows/authorization-code.png)

V předchozím diagramu aplikace:

1. Požádá o autorizační kód, který je uplatněn pro přístupový token.
2. Pomocí přístupového tokenu zavolá webové rozhraní API.

### <a name="considerations"></a>Důležité informace

- Autorizační kód můžete použít jenom jednou pro uplatnění tokenu. Nepokusit se vícekrát získat token se stejným autorizačním kódem, protože je výslovně zakázaný standardní specifikací protokolu. Pokud kód několikrát přiřadíte, ať už úmyslně, nebo vzhledem k tomu, že si nejste vědomi, že to architektura provede za vás, zobrazí se tato chyba:

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>Přihlašovací údaje klienta

[Tok přihlašovacích údajů klienta OAuth 2](v2-oauth2-client-creds-grant-flow.md) umožňuje přístup k prostředkům hostovaným na webu pomocí identity aplikace. Tento typ grantu se běžně používá pro interakce mezi servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. Tyto typy aplikací se často označují jako démoni nebo účty služeb.

Tok udělení přihlašovacích údajů klienta umožňuje webové službě (důvěrnému klientovi) použít vlastní přihlašovací údaje místo zosobnění uživatele k ověření při volání jiné webové služby. V tomto scénáři je klient většinou webová služba střední vrstvy, služba démona nebo Web. Pro zajištění vyšší úrovně zabezpečení umožňuje volající službě také použít certifikát (namísto sdíleného tajného klíče) jako přihlašovací údaje.

> [!NOTE]
> Důvěrný tok klienta není dostupný na mobilních platformách, jako jsou UWP, Xamarin. iOS a Xamarin. Android, protože podporují jenom veřejné klientské aplikace. Veřejné klientské aplikace nevědí, jak prokázat identitu aplikace zprostředkovateli identity. Zabezpečené připojení můžete dosáhnout v back-endu webové aplikace nebo rozhraní Web API na konci nasazením certifikátu.

### <a name="application-secrets"></a>Tajné klíče aplikace

![Diagram důvěrného klienta s heslem](media/msal-authentication-flows/confidential-client-password.png)

V předchozím diagramu aplikace:

1. Načte token pomocí tajného klíče aplikace nebo přihlašovacích údajů k heslu.
2. Používá token k vytváření žádostí o prostředek.

### <a name="certificates"></a>Certifikáty

![Diagram důvěrného klienta s certifikátem](media/msal-authentication-flows/confidential-client-certificate.png)

V předchozím diagramu aplikace:

1. Získá token pomocí pověření certifikátu.
2. Používá token k vytváření žádostí o prostředek.

Tyto přihlašovací údaje klienta musí být:

- Registrováno ve službě Azure AD.
- Předáno při vytváření objektu důvěrné klientské aplikace ve vašem kódu.

## <a name="device-code"></a>Kód zařízení

[Tok kódu zařízení OAuth 2](v2-oauth2-device-code.md) umožňuje uživatelům přihlašovat se ke vstupnímu zařízení s omezeními, jako jsou chytré televizory, zařízení IoT a tiskárny. Interaktivní ověřování pomocí Azure AD vyžaduje webový prohlížeč. Když zařízení nebo operační systém neposkytují webový prohlížeč, tok kódu zařízení umožňuje uživateli použít jiné zařízení jako počítač nebo mobilní telefon k interaktivnímu přihlášení.

Pomocí toku kódu zařízení aplikace získá tokeny prostřednictvím procesu se dvěma kroky, který je navržený pro tato zařízení a operační systémy. Mezi takové aplikace patří například ty, které běží na zařízeních IoT a v nástrojích rozhraní příkazového řádku (CLI).

![Diagram toku kódu zařízení](media/msal-authentication-flows/device-code.png)

V předchozím diagramu:

1. Kdykoli se vyžaduje ověření uživatele, aplikace poskytne kód a požádá uživatele, aby k návštěvě adresy URL používal jiné zařízení, jako je například telefon připojený k Internetu `https://microsoft.com/devicelogin` . Uživatel se pak vyzve k zadání kódu a pokračuje v normálním prostředí ověřování, včetně výzev k vyjádření souhlasu a [vícefaktorového ověřování](../authentication/concept-mfa-howitworks.md)(v případě potřeby).
1. Po úspěšném ověření obdrží aplikace příkazového řádku požadované tokeny prostřednictvím zpětného kanálu a použije je k provedení volání webového rozhraní API.

### <a name="constraints"></a>Omezení

- Tok kódu zařízení je k dispozici pouze ve veřejných klientských aplikacích.
- Autorita předaná při sestavování veřejné klientské aplikace musí být jedna z následujících:
  - Tenant ve formátu, `https://login.microsoftonline.com/{tenant}/,` kde `{tenant}` je buď identifikátor GUID představující ID tenanta, nebo název domény přidružené k tenantovi.
  - Pro pracovní a školní účty ve formuláři `https://login.microsoftonline.com/organizations/` .

## <a name="implicit-grant"></a>Implicitní udělení

Tok [implicitního udělení OAuth 2](v2-oauth2-implicit-grant-flow.md) umožňuje, aby aplikace získala tokeny od platformy Microsoft identity, aniž by prováděla výměnu přihlašovacích údajů back-endu serveru. Tento tok umožňuje aplikaci přihlásit uživatele, udržovat relaci a získat tokeny pro další webová rozhraní API, a to vše v kódu JavaScriptu klienta.

![Diagram toku implicitního udělení](media/msal-authentication-flows/implicit-grant.svg)

Mnoho moderních webových aplikací je postavených na straně klienta s jednou stránkou (SPA), které jsou napsané v jazyce JavaScript nebo v architektuře SPA, jako je například úhlové, Vue.js a React.js. Tyto aplikace běží ve webovém prohlížeči a mají různé charakteristiky ověřování než tradiční webové aplikace na straně serveru. Platforma Microsoft Identity Platform umožňuje uživatelům přihlašovat se pomocí jediné stránky a získat tokeny pro přístup k back-endové službě nebo webovým rozhraním API pomocí implicitního toku udělení. Implicitní tok umožňuje aplikaci získat tokeny ID, které reprezentují ověřeného uživatele, a také přístup k tokenům potřebným pro volání chráněných rozhraní API.

Tento tok ověřování neobsahuje scénáře aplikací, které používají rozhraní JavaScript pro různé platformy, jako je například elektronicky nebo React-Native, protože vyžadují další možnosti pro interakci s nativními platformami.

Tokeny vydané prostřednictvím implicitního režimu toku mají **omezení délky** , protože jsou vraceny do prohlížeče podle adresy URL (kde `response_mode` je buď `query` nebo `fragment` ). Některé prohlížeče omezují délku adresy URL v panelu prohlížeče a selžou, pokud je příliš dlouhá. Proto tyto implicitní tokeny toků neobsahují `groups` ani `wids` nedeklarují deklarace identity.

## <a name="on-behalf-of"></a>Jménem

Tok [toku ověřování OAuth 2](v2-oauth2-on-behalf-of-flow.md) se používá, když aplikace vyvolá službu nebo webové rozhraní API, které zase potřebuje volat jinou službu nebo webové rozhraní API. Nápad je rozšířit identitu delegovaného uživatele a oprávnění prostřednictvím řetězce požadavků. Aby služba střední vrstvy prováděla ověřené požadavky na službu pro příjem dat, musí *jménem* uživatele zabezpečit přístupový token od platformy Microsoft identity.

![Diagram toku za běhu](media/msal-authentication-flows/on-behalf-of.png)

V předchozím diagramu:

1. Aplikace získá přístupový token pro webové rozhraní API.
2. Klient (webová, desktopová, mobilní nebo jednostránková aplikace) volá chráněné webové rozhraní API a v ověřovací hlavičce požadavku HTTP se přidá přístupový token jako nosný token. Webové rozhraní API ověřuje uživatele.
3. Když klient zavolá webové rozhraní API, webové rozhraní API požádá o další token jménem uživatele.
4. Chráněné webové rozhraní API používá tento token k volání webového rozhraní API pro příjem dat v rámci uživatele. Webové rozhraní API může také později žádat o tokeny pro jiná rozhraní API pro příjem dat (ale pořád za stejného uživatele).

## <a name="usernamepassword"></a>Uživatelské jméno a heslo

Udělení [přihlašovacích údajů pro heslo vlastníka prostředku OAuth 2](v2-oauth-ropc.md) (ROPC) umožňuje aplikaci podepsat uživatele přímo zpracováním hesla. V desktopové aplikaci můžete použít tok uživatelského jména a hesla k tiché získání tokenu. Při použití aplikace není vyžadováno žádné uživatelské rozhraní.

![Diagram toku uživatelského jména a hesla](media/msal-authentication-flows/username-password.png)

V předchozím diagramu aplikace:

1. Získá token odesláním uživatelského jména a hesla do poskytovatele identity.
2. Volá webové rozhraní API pomocí tokenu.

> [!WARNING]
> Tento tok se nedoporučuje. Vyžaduje vysoký stupeň důvěry a ohrožení přihlašovacích údajů. Tento tok byste měli použít *pouze* v případě, že nelze použít více zabezpečených toků. Další informace najdete v tématu [co je řešení rostoucího problému s hesly?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Upřednostňovaný tok pro získání tokenu v tichém režimu na počítačích připojených k doméně systému Windows je [integrované ověřování systému Windows](#integrated-windows-authentication). V jiných případech použijte [tok kódu zařízení](#device-code).

I když tok uživatelského jména a hesla může být užitečný v některých scénářích, jako je DevOps, vyhněte se tomu, pokud chcete použít uživatelské jméno a heslo v interaktivních scénářích, kde zadáváte vlastní uživatelské rozhraní.

Pomocí uživatelského jména a hesla:

- Uživatelé, kteří se potřebují k ověřování Multi-Factor Authentication, se nebudou moct přihlásit, protože neexistují žádné interakce.
- Uživatelé nebudou moct provádět jednotné přihlašování.

### <a name="constraints"></a>Omezení

Kromě [omezení integrovaného ověřování systému Windows](#integrated-windows-authentication)platí i následující omezení:

- Tok uživatelského jména a hesla není kompatibilní s podmíněným přístupem a službou Multi-Factor Authentication. V důsledku toho, pokud vaše aplikace běží v tenantovi Azure AD, kde správce tenanta vyžaduje vícefaktorové ověřování, nemůžete tento tok použít. Mnoho organizací to udělat.
- ROPC funguje jenom pro pracovní a školní účty. ROPC pro účty Microsoft (MSA) nemůžete použít.
- Tok je k dispozici na platformě .NET Desktop a .NET Core, ale ne na Univerzální platforma Windows.
- V Azure AD B2C tok ROPC funguje jenom pro místní účty. Informace o ROPC v MSAL.NET a Azure AD B2C najdete v tématu [použití ROPC s Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows

MSAL podporuje integrované ověřování systému Windows (IWA) pro stolní a mobilní aplikace, které běží na počítači s Windows připojeném k doméně nebo Azure AD. Pomocí IWA můžou tyto aplikace v tichém získání tokenu bez nutnosti zásahu uživatelského rozhraní.

![Diagram integrovaného ověřování systému Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

V předchozím diagramu aplikace:

1. Získá token pomocí integrovaného ověřování systému Windows.
2. Používá token k vytváření žádostí o prostředek.

### <a name="constraints"></a>Omezení

Integrované ověřování systému Windows (IWA) podporuje *pouze* federované uživatele – uživatele vytvořené ve službě Active Directory a zálohované službou Azure AD. Uživatelé, kteří vytvořili přímo ve službě Azure AD bez zálohování služby Active Directory (spravované uživatele), nemůžou tento tok ověřování používat. Toto omezení neovlivňuje [tok uživatelského jména a hesla](#usernamepassword).

IWA je pro aplikace .NET Framework, .NET Core a Univerzální platforma Windows.

IWA neobejde službu Multi-Factor Authentication. Pokud je nakonfigurováno Multi-Factor Authentication, může IWA selhat, pokud je vyžadována výzva služby Multi-Factor Authentication. Multi-Factor Authentication vyžaduje zásah uživatele.

Neřídíte, kdy zprostředkovatel identity požaduje provedení dvojúrovňového ověřování. Správce tenanta. Při přihlášení z jiné země nebo oblasti se obvykle vyžaduje dvojúrovňové ověřování, když nejste připojení přes síť VPN k podnikové síti a někdy i když jste připojení přes VPN. Azure AD používá AI k nepřetržitému učení, jestli je potřeba dvojúrovňové ověřování. Pokud se IWA nepovede, měli byste se vrátit na [interaktivní výzvu uživatele](#interactive-and-non-interactive-authentication).

Autorita předaná při sestavování veřejné klientské aplikace musí být jednou z těchto:

- Tenant ve formátu, `https://login.microsoftonline.com/{tenant}/,` kde `{tenant}` je buď identifikátor GUID představující ID tenanta, nebo název domény přidružené k tenantovi.
- Pro všechny pracovní a školní účty ( `https://login.microsoftonline.com/organizations/` ). Osobní účty Microsoft (MSA) nejsou podporované; nemůžete použít `/common` ani `/consumers` tenanta.

Vzhledem k tomu, že IWA je tichý tok, musí být jedna z následujících podmínek:

- Uživatel vaší aplikace musí mít dřív souhlas s používáním aplikace.
- Správce tenanta musí předtím, než se aplikace přihlásí všem uživatelům v tenantovi, aby aplikaci používal.

To znamená, že jedna z následujících možností je pravdivá:

- Jako vývojář jste zvolili možnost **udělit** v Azure Portal pro sebe.
- Správce tenanta vybral na kartě **oprávnění rozhraní API** registrace aplikace v tomto Azure Portal **souhlas správce grant/REVOKE pro doménu {tenant}** (viz [Přidání oprávnění pro přístup k webovému rozhraní API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)).
- Poskytli jste způsob, jak uživatelům udělit souhlas s aplikací. Přečtěte si téma [vyžádání souhlasu jednotlivých uživatelů](v2-permissions-and-consent.md#requesting-individual-user-consent).
- Poskytli jste způsob, jak může správce tenanta pro aplikaci vyjádřit souhlas. viz [souhlas správce](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

Tok IWA je povolený pro aplikace .NET Desktop, .NET Core a Windows Universal Platform. V .NET Core musíte zadat uživatelské jméno, které se IWA, protože .NET Core nemůže získat uživatelská jména z operačního systému.

Další informace o souhlasu najdete v tématu [oprávnění a vyjádření souhlasu v 2.0](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali toky ověřování podporované knihovnou Microsoft Authentication Library (MSAL), přečtěte si, jak získat a ukládat do mezipaměti tokeny používané v těchto tocích:

[Získání a ukládání tokenů do mezipaměti pomocí knihovny Microsoft Authentication Library (MSAL)](msal-acquire-cache-tokens.md)
