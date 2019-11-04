---
title: Toky ověřování (Microsoft Authentication Library)
titleSuffix: Microsoft identity platform
description: Přečtěte si o tocích ověřování a grantech, které používá knihovna Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55d618a24b957fedb6fc2af3e75b7a7d2bd23d96
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473810"
---
# <a name="authentication-flows"></a>Toky ověřování

Tento článek popisuje různé toky ověřování, které poskytuje knihovna Microsoft Authentication Library (MSAL).  Tyto toky je možné používat v různých různých scénářích aplikací.

| Tok | Popis | Použito v|  
| ---- | ----------- | ------- | 
| [Interaktivní](#interactive) | Získá token prostřednictvím interaktivního procesu, který uživateli vyzve k zadání přihlašovacích údajů prostřednictvím prohlížeče nebo místního okna. | [Desktopové aplikace](scenario-desktop-overview.md), [mobilní aplikace](scenario-mobile-overview.md) |
| [Implicitní udělení](#implicit-grant) | Umožňuje aplikaci získat tokeny bez provedení výměny přihlašovacích údajů back-endu serveru. To umožňuje aplikaci přihlašovat uživatele, udržovat relaci a získávat tokeny do jiných webových rozhraní API, a to vše v kódu JavaScriptu klienta.| [Jednostránkové aplikace (SPA)](scenario-spa-overview.md) |
| [Autorizační kód](#authorization-code) | Používá se v aplikacích, které jsou nainstalované na zařízení, aby získal přístup k chráněným prostředkům, například k webovým rozhraním API. Díky tomu můžete přidat přihlašování a přístup k rozhraní API pro mobilní a desktopové aplikace. | [Desktopové aplikace](scenario-desktop-overview.md), [mobilní aplikace](scenario-mobile-overview.md), [webové aplikace](scenario-web-app-call-api-overview.md) | 
| [Jménem](#on-behalf-of) | Aplikace vyvolá službu nebo webové rozhraní API, které zase musí volat jinou službu nebo webové rozhraní API. Nápad je rozšířit identitu delegovaného uživatele a oprávnění prostřednictvím řetězce požadavků. | [Webová rozhraní API](scenario-web-api-call-api-overview.md) |
| [Přihlašovací údaje klienta](#client-credentials) | Umožňuje přístup k prostředkům hostovaným na webu pomocí identity aplikace. Běžně se používá pro interakce mezi servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. | [Aplikace démona](scenario-daemon-overview.md) |
| [Kód zařízení](#device-code) | Umožňuje uživatelům přihlašovat se ke vstupnímu zařízení s omezením, jako jsou například inteligentní televizní vysílání, zařízení IoT nebo tiskárna. | [Desktopové/mobilní aplikace](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Integrované ověřování systému Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Umožňuje aplikacím připojeným k doméně nebo Azure Active Directory (Azure AD) získat token v tichém režimu (bez interakce uživatelského rozhraní od uživatele).| [Desktopové/mobilní aplikace](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Uživatelské jméno a heslo](scenario-desktop-acquire-token.md#username--password) | Umožňuje aplikaci podepsat uživatele přímo pořízením hesla. Tento tok se nedoporučuje. | [Desktopové/mobilní aplikace](scenario-desktop-acquire-token.md#username--password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Jak každý tok generuje tokeny a kódy
 
V závislosti na tom, jak je váš klient sestavený, může použít jeden (nebo několik) toků ověřování podporovaných platformou Microsoft identity.  Tyto toky můžou vytvářet různé tokeny (id_tokens, aktualizovat tokeny, přístupové tokeny) a také autorizační kódy a při práci vyžadovat jiné tokeny. Tento graf znázorňuje přehled:
 
|Tok | Nutné | id_token | přístupový token | aktualizovat token | autorizační kód | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Tok autorizačního kódu](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicitní tok](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybridní tok OIDC](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Aktualizovat uplatnění tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | aktualizovat token | x | x | x| |
|[Tok On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) | přístupový token| x| x| x| |
|[Tok kódu zařízení](v2-oauth2-device-code.md) | | x| x| x| |
|[Přihlašovací údaje klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (jenom aplikace)| | |
 
Tokeny vydané prostřednictvím implicitního režimu mají omezení délky, protože se předává zpátky do prohlížeče přes adresu URL (kde `response_mode` je `query` nebo `fragment`).  Některé prohlížeče mají omezení velikosti adresy URL, kterou lze umístit do panelu prohlížeče, a selhání, pokud je příliš dlouhé.  Proto tyto tokeny nemají deklarace `groups` nebo `wids`.

## <a name="interactive"></a>Interaktivní

MSAL podporuje možnost interaktivně vyzvat uživatele k přihlášení k přihlašovacím údajům a získat token pomocí těchto přihlašovacích údajů.

![Diagram interaktivního toku](media/msal-authentication-flows/interactive.png)

Další informace o použití MSAL.NET k interaktivnímu získání tokenů na konkrétních platformách najdete v tématech:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Univerzální platforma Windows](msal-net-uwp-considerations.md)

Další informace o interaktivních voláních v MSAL. js najdete v tématu [chování výzvy v interaktivních požadavcích MSAL. js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicitní udělení

MSAL podporuje [tok implicitního udělení OAuth 2](v2-oauth2-implicit-grant-flow.md), který umožňuje aplikaci získat tokeny z platformy Microsoft Identity Platform bez provedení výměny přihlašovacích údajů back-endu serveru. To umožňuje aplikaci přihlašovat uživatele, udržovat relaci a získávat tokeny do jiných webových rozhraní API, a to vše v kódu JavaScriptu klienta.

![Diagram toku implicitního udělení](media/msal-authentication-flows/implicit-grant.svg)

Mnoho moderních webových aplikací je postavených jako aplikace na straně klienta, které jsou zapisovány pomocí jazyka JavaScript nebo rozhraní zabezpečeného hesla, jako je například úhlové Vue. js, a reaguje. js. Tyto aplikace běží ve webovém prohlížeči a mají různé charakteristiky ověřování než tradiční webové aplikace na straně serveru. Platforma Microsoft Identity Platform umožňuje uživatelům přihlašovat se pomocí jediné stránky a získat tokeny pro přístup k back-endové službě nebo webovým rozhraním API pomocí implicitního toku udělení. Implicitní tok umožňuje aplikaci získat tokeny ID, které reprezentují ověřeného uživatele, a také přístup k tokenům potřebným pro volání chráněných rozhraní API.

Tento tok ověřování neobsahuje scénáře aplikací, které používají rozhraní JavaScript pro různé platformy, jako jsou například elektronicky a reagující na nativní, protože vyžadují další možnosti pro interakci s nativními platformami.

## <a name="authorization-code"></a>autorizační kód

MSAL podporuje [udělení autorizačního kódu OAuth 2](v2-oauth2-auth-code-flow.md). Tento grant se dá použít v aplikacích, které jsou nainstalované na zařízení, aby získal přístup k chráněným prostředkům, například k webovým rozhraním API. Díky tomu můžete přidat přihlašování a přístup k rozhraní API pro mobilní a desktopové aplikace. 

Když se uživatelé přihlásí k webovým aplikacím (websites), obdrží webová aplikace autorizační kód.  Autorizační kód se považuje za získání tokenu pro volání webových rozhraní API. V ASP.NET a ASP.NET Core webové aplikace jediným cílem `AcquireTokenByAuthorizationCode` je přidat token do mezipaměti tokenů. Tento token pak může aplikace použít (obvykle v řadičích, což znamená, že jenom získá token pro rozhraní API pomocí `AcquireTokenSilent`).

![Diagram toku autorizačního kódu](media/msal-authentication-flows/authorization-code.png)

V předchozím diagramu aplikace:

1. Požádá o autorizační kód, který je uplatněn pro přístupový token.
2. Pomocí přístupového tokenu zavolá webové rozhraní API.

### <a name="considerations"></a>Požadavky

- Autorizační kód můžete použít jenom jednou pro uplatnění tokenu. Nepokoušejte se získat token několikrát se stejným autorizačním kódem (výslovně ho zakázal standardní specifikace protokolu). Pokud kód přiřadíte několikrát, nebo protože nevíte, že pro vás rámec provede, zobrazí se vám následující chyba: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Pokud zapisujete ASP.NET nebo ASP.NET Core aplikaci, může to nastat, pokud neznáte rozhraní, pro které jste už tento autorizační kód nepovažovali. V tomto případě je třeba volat metodu `context.HandleCodeRedemption()` obslužné rutiny události `AuthorizationCodeReceived`.

- Nepoužívejte sdílení přístupového tokenu s ASP.NET, což by mohlo zabránit správnému vykonání přírůstkového souhlasu. Další informace najdete v tématu věnovaném [vydávání #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>Jménem

MSAL podporuje [tok ověřování OAuth 2 pro uživatele](v2-oauth2-on-behalf-of-flow.md).  Tento tok se používá, když aplikace vyvolá službu nebo webové rozhraní API, které zase musí volat jinou službu nebo webové rozhraní API. Nápad je rozšířit identitu delegovaného uživatele a oprávnění prostřednictvím řetězce požadavků. Aby služba střední vrstvy prováděla ověřené požadavky na službu pro příjem dat, musí jménem uživatele zabezpečit přístupový token od platformy Microsoft identity.

![Diagram toku za běhu](media/msal-authentication-flows/on-behalf-of.png)

V předchozím diagramu:

1. Aplikace získá přístupový token pro webové rozhraní API.
2. Klient (webová, desktopová, mobilní nebo jednostránková aplikace) volá chráněné webové rozhraní API a v ověřovací hlavičce požadavku HTTP se přidá přístupový token jako nosný token. Webové rozhraní API ověřuje uživatele.
3. Když klient zavolá webové rozhraní API, webové rozhraní API požádá o další token jménem uživatele.  
4. Chráněné webové rozhraní API používá tento token k volání webového rozhraní API pro příjem dat v rámci uživatele.  Webové rozhraní API může také později žádat o tokeny pro jiná rozhraní API pro příjem dat (ale pořád za stejného uživatele).

## <a name="client-credentials"></a>Přihlašovací údaje klienta

MSAL podporuje [tok přihlašovacích údajů klienta OAuth 2](v2-oauth2-client-creds-grant-flow.md). Tento tok umožňuje přístup k prostředkům hostovaným na webu pomocí identity aplikace. Tento typ grantu se běžně používá pro interakce mezi servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. Tyto typy aplikací se často označují jako démoni nebo účty služeb. 

Tok udělení přihlašovacích údajů klienta umožňuje webové službě (důvěrnému klientovi) použít vlastní přihlašovací údaje místo zosobnění uživatele k ověření při volání jiné webové služby. V tomto scénáři je klient většinou webová služba střední vrstvy, služba démona nebo Web. Pro zajištění vyšší úrovně zabezpečení umožňuje volající službě také použít certifikát (namísto sdíleného tajného klíče) jako přihlašovací údaje.

> [!NOTE]
> Tok důvěrného klienta není dostupný na mobilních platformách (UWP, Xamarin. iOS a Xamarin. Android), protože podporují jenom veřejné klientské aplikace. Veřejné klientské aplikace nevědí, jak prokázat identitu aplikace zprostředkovateli identity. Zabezpečené připojení je možné dosáhnout v back-endu webové aplikace nebo webového rozhraní API nasazením certifikátu.

MSAL.NET podporuje dva typy přihlašovacích údajů klienta. Tyto přihlašovací údaje klienta musí být zaregistrované ve službě Azure AD. Přihlašovací údaje jsou předány do konstruktorů důvěrné klientské aplikace ve vašem kódu.

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
- Předáno při konstrukci aplikace důvěrného klienta ve vašem kódu.

## <a name="device-code"></a>Kód zařízení

MSAL podporuje [tok kódu zařízení OAuth 2](v2-oauth2-device-code.md), který umožňuje uživatelům přihlašovat se ke vstupním zařízením s omezením, jako jsou například inteligentní televizní vysílání, zařízení IoT nebo tiskárna. Interaktivní ověřování pomocí Azure AD vyžaduje webový prohlížeč. Tok kódu zařízení umožňuje uživateli použít jiné zařízení (například jiný počítač nebo mobilní telefon) k interaktivnímu přihlášení, kde zařízení nebo operační systém neposkytuje webový prohlížeč.

Pomocí toku kódu zařízení aplikace získá tokeny prostřednictvím procesu se dvěma kroky, hlavně navržených pro tato zařízení nebo operační systémy. Mezi takové aplikace patří například ty, které běží na zařízeních IoT nebo nástrojích příkazového řádku (CLI). 

![Diagram toku kódu zařízení](media/msal-authentication-flows/device-code.png)

V předchozím diagramu:

1. Kdykoli se vyžaduje ověření uživatele, aplikace poskytne kód a vyzve uživatele k použití jiného zařízení (například smartphone připojeného k Internetu) k přechodu na adresu URL (například https://microsoft.com/devicelogin). Uživatel se pak vyzve k zadání kódu a pokračuje v běžném prostředí ověřování, včetně výzev k vyjádření souhlasu a vícefaktorového ověřování v případě potřeby.

2. Po úspěšném ověření obdrží aplikace příkazového řádku požadované tokeny prostřednictvím zpětného kanálu a použije je k provedení volání webového rozhraní API.

### <a name="constraints"></a>Omezení

- Tok kódu zařízení je k dispozici pouze pro veřejné klientské aplikace.
- Autorita předaná při sestavování veřejné klientské aplikace musí být jedna z následujících:
  - Tenant (z formuláře `https://login.microsoftonline.com/{tenant}/`, kde `{tenant}` je buď identifikátor GUID, který představuje ID tenanta nebo doménu přidruženou k tenantovi).
  - Pro všechny pracovní a školní účty (`https://login.microsoftonline.com/organizations/`).
- Osobní účty Microsoft ještě nejsou podporované koncovým bodem Azure AD v 2.0 (nemůžete použít klienty `/common` ani `/consumers`).

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows

MSAL podporuje integrované ověřování systému Windows (IWA) pro stolní nebo mobilní aplikace, které běží na připojené doméně nebo počítači s Windows připojeném k Azure AD. Pomocí IWA můžou tyto aplikace v tichém režimu získat token (bez interakce uživatelského rozhraní od uživatele). 

![Diagram integrovaného ověřování systému Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

V předchozím diagramu aplikace:

1. Získá token pomocí integrovaného ověřování systému Windows.
2. Používá token k vytváření žádostí o prostředek.

### <a name="constraints"></a>Omezení

IWA podporuje jenom federované uživatele, což znamená uživatele vytvořeného v Active Directory a zálohovaných službou Azure AD. Uživatelé, kteří vytvořili přímo ve službě Azure AD, nemůžou používat tento tok ověřování, aniž by museli provádět službu Active Directory (spravované uživatele). Toto omezení neovlivňuje [tok uživatelského jména a hesla](#usernamepassword).

IWA je pro aplikace napsané pro .NET Framework, .NET Core a Univerzální platforma Windows platformy.

IWA neobejde službu Multi-Factor Authentication. Pokud je nakonfigurováno Multi-Factor Authentication, může IWA selhat, pokud je vyžadována výzva služby Multi-Factor Authentication. Multi-Factor Authentication vyžaduje zásah uživatele.

Neřídíte, kdy zprostředkovatel identity požaduje provedení dvojúrovňového ověřování. Správce tenanta. Při přihlášení z jiné země se obvykle vyžaduje dvojúrovňové ověřování, když nejste připojení přes síť VPN k podnikové síti a někdy i když jste připojení přes VPN. Azure AD používá AI k nepřetržitému učení, jestli je potřeba dvojúrovňové ověřování. Pokud IWA neproběhne úspěšně, měli byste se vrátit na [interaktivní uživatelské výzvy] (#interactive).

Autorita předaná při sestavování veřejné klientské aplikace musí být jedna z následujících:
- Tenant (z formuláře `https://login.microsoftonline.com/{tenant}/`, kde `tenant` je buď identifikátor GUID, který představuje ID tenanta nebo doménu přidruženou k tenantovi).
- Pro všechny pracovní a školní účty (`https://login.microsoftonline.com/organizations/`). Osobní účty Microsoft nejsou podporované (nemůžete použít klienty `/common` ani `/consumers`).

Vzhledem k tomu, že IWA je tichý tok, musí být jedna z následujících podmínek:
- Uživatel vaší aplikace musí mít dřív souhlas s používáním aplikace. 
- Správce tenanta musí předtím, než se aplikace přihlásí všem uživatelům v tenantovi, aby aplikaci používal.

To znamená, že jedna z následujících možností je pravdivá:
- Jako vývojář jste vybrali možnost **udělit** Azure Portal pro sebe.
- Správce tenanta vybral v registraci aplikace v rámci karty **oprávnění rozhraní API** pro tuto aplikaci **souhlas správce grant/REVOKE pro doménu {tenant}** (viz [Přidání oprávnění pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Poskytli jste způsob, jak uživatelům udělit souhlas s aplikací (viz [vyžádání souhlasu jednotlivého uživatele](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Poskytli jste možnost souhlasu správce tenanta s aplikací (viz téma [souhlas správce](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

Tok IWA je povolený pro aplikace .NET Desktop, .NET Core a Windows Universal Platform. V .NET Core musíte zadat uživatelské jméno, které se IWA, protože .NET Core nemůže získat uživatelská jména z operačního systému.
  
Další informace o souhlasu najdete v tématu [oprávnění a vyjádření souhlasu v 2.0](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Uživatelské jméno a heslo

MSAL podporuje [udělení přihlašovacích údajů pro heslo vlastníka prostředku OAuth 2](v2-oauth-ropc.md), což umožňuje aplikaci podepsat uživatele přímo zpracováním hesla. V desktopové aplikaci můžete použít tok uživatelského jména a hesla k tiché získání tokenu. Při použití aplikace není vyžadováno žádné uživatelské rozhraní.

![Diagram toku uživatelského jména a hesla](media/msal-authentication-flows/username-password.png)

V předchozím diagramu aplikace:

1. Získá token odesláním uživatelského jména a hesla do poskytovatele identity.
2. Volá webové rozhraní API pomocí tokenu.

> [!WARNING]
> Tento tok se nedoporučuje. Vyžaduje vysoký stupeň důvěry a ohrožení uživatele.  Tento tok byste měli používat jenom v případě, že se jedná o jiné, bezpečnější toky, které se nedají použít. Další informace najdete v tématu [co je řešení rostoucího problému s hesly?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Upřednostňovaný tok pro získání tokenu v tichém režimu na počítačích připojených k doméně systému Windows je [integrované ověřování systému Windows](#integrated-windows-authentication). V opačném případě můžete použít také [tok kódu zařízení](#device-code).

I když je to užitečné v některých případech (DevOps scénáře), pokud chcete použít uživatelské jméno a heslo v interaktivních scénářích, kde poskytujete vlastní uživatelské rozhraní, zkuste to vyhnout. Pomocí uživatelského jména a hesla:
- Uživatelé, kteří se potřebují ke službě Multi-Factor Authentication, se nebudou moct přihlásit (protože žádná interakce není).
- Uživatelé nebudou moct provádět jednotné přihlašování.

### <a name="constraints"></a>Omezení

Kromě [omezení integrovaného ověřování systému Windows](#integrated-windows-authentication)platí i následující omezení:

- Tok uživatelského jména a hesla není kompatibilní s podmíněným přístupem a službou Multi-Factor Authentication. V důsledku toho, pokud vaše aplikace běží v tenantovi Azure AD, kde správce tenanta vyžaduje vícefaktorové ověřování, nemůžete tento tok použít. Mnoho organizací to udělat.
- Funguje pouze pro pracovní a školní účty (nikoli účty Microsoft).
- Tok je k dispozici na platformě .NET Desktop a .NET Core, ale ne na Univerzální platforma Windows.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C specifické

Další informace o používání MSAL.NET a Azure AD B2C najdete v tématu [použití ROPC s Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
