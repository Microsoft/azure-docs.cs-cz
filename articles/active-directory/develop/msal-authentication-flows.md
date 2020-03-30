---
title: Toky ověřování msal | Azure
titleSuffix: Microsoft identity platform
description: Informace o tocích ověřování a grantech používaných knihovnou Ověřování společnosti Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 25c219bedbbbec9fbc0c5617c7bd9fc482faf49a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050515"
---
# <a name="authentication-flows"></a>Toky ověřování

Tento článek popisuje různé toky ověřování poskytované knihovnou Ověřování společnosti Microsoft (MSAL).  Tyto toky lze použít v různých scénářích aplikace.

| Tok | Popis | Používá se v|  
| ---- | ----------- | ------- | 
| [Interaktivní](#interactive) | Získá token prostřednictvím interaktivního procesu, který vyzve uživatele k zadání přihlašovacích údajů prostřednictvím prohlížeče nebo automaticky otevírané okno. | [Desktopové aplikace](scenario-desktop-overview.md), [mobilní aplikace](scenario-mobile-overview.md) |
| [Implicitní grant](#implicit-grant) | Umožňuje aplikaci získat tokeny bez provedení výměny pověření serveru back-end. To umožňuje aplikaci přihlásit se k uživateli, udržovat relaci a získat tokeny do jiných webových rozhraní API, to vše v rámci kódu javascriptového klienta.| [Jednostránkové aplikace (SPA)](scenario-spa-overview.md) |
| [Autorizační kód](#authorization-code) | Používá se v aplikacích nainstalovaných v zařízení, aby získal přístup k chráněným prostředkům, jako jsou webová rozhraní API. To vám umožní přidat přihlášení a přístup k rozhraní API do mobilních aplikací a aplikací pro počítače. | [Desktopové aplikace](scenario-desktop-overview.md), [mobilní aplikace](scenario-mobile-overview.md), [webové aplikace](scenario-web-app-call-api-overview.md) | 
| [Jménem](#on-behalf-of) | Aplikace vyvolá službu nebo webové rozhraní API, které zase potřebuje volat jinou službu nebo webové rozhraní API. Cílem je šířit delegovanou identitu uživatele a oprávnění prostřednictvím řetězce požadavků. | [Webová api](scenario-web-api-call-api-overview.md) |
| [Přihlašovací údaje klienta](#client-credentials) | Umožňuje přístup k webovým hostovaným prostředkům pomocí identity aplikace. Běžně se používá pro interakce mezi servery, které musí být spuštěny na pozadí, bez okamžité interakce s uživatelem. | [Aplikace démonů](scenario-daemon-overview.md) |
| [Kód zařízení](#device-code) | Umožňuje uživatelům přihlásit se k zařízením s omezenýmvstupem, jako je například smart TV, zařízení IoT nebo tiskárna. | [Desktopové/mobilní aplikace](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Integrované ověřování systému Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Umožňuje aplikacím v doméně nebo Azure Active Directory (Azure AD) připojil počítače získat token tiše (bez jakékoli interakce uživatelského rozhraní od uživatele).| [Desktopové/mobilní aplikace](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Uživatelské jméno a heslo](scenario-desktop-acquire-token.md#username-and-password) | Umožňuje aplikaci přihlásit uživatele přímo zpracováním jeho hesla. Tento tok se nedoporučuje. | [Desktopové/mobilní aplikace](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Jak každý tok vyzařuje tokeny a kódy
 
V závislosti na tom, jak je váš klient sestaven, může používat jeden (nebo několik) toků ověřování podporovaných platformou identit microsoftu.  Tyto toky můžete vytvářet různé tokeny (id_tokens, obnovovací tokeny, přístupové tokeny) a také autorizační kódy a vyžadují různé tokeny, aby fungovaly. Tento graf poskytuje přehled:
 
|Tok | Vyžaduje | id_token | přístupový token | obnovovací token | autorizační kód | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Tok autorizačního kódu](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicitní tok](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybridní TOK OIDC](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Obnovení uplatnění tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | obnovovací token | x | x | x| |
|[Tok On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) | přístupový token| x| x| x| |
|[Tok kódu zařízení](v2-oauth2-device-code.md) | | x| x| x| |
|[Přihlašovací údaje klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (pouze aplikace)| | |
 
Tokeny vydané prostřednictvím implicitního režimu mají omezení délky kvůli předání `response_mode` `query` zpět `fragment`do prohlížeče prostřednictvím adresy URL (kde je nebo ).  Některé prohlížeče mají limit na velikost url, které mohou být uvedeny v panelu prohlížeče a selhání, když je příliš dlouhá.  Proto tyto tokeny nemají `groups` `wids` nebo deklarace identity.

## <a name="interactive"></a>Interaktivní

MSAL podporuje možnost interaktivně vyzvat uživatele k jejich pověření k přihlášení a získat token pomocí těchto pověření.

![Diagram interaktivního toku](media/msal-authentication-flows/interactive.png)

Další informace o použití MSAL.NET k interaktivnímu získání tokenů na konkrétních platformách naleznete v tématu:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Univerzální platforma Windows](msal-net-uwp-considerations.md)

Další informace o interaktivních hovorech v souboru MSAL.js naleznete [v tématu Prompt behavior in MSAL.js interactive requests](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicitní grant

MSAL podporuje [oauth 2 implicitní tok udělení podpory](v2-oauth2-implicit-grant-flow.md), který umožňuje aplikaci získat tokeny z platformy identit y Microsoft bez provedení výměny pověření serveru back-end. To umožňuje aplikaci přihlásit se k uživateli, udržovat relaci a získat tokeny do jiných webových rozhraní API, to vše v rámci kódu javascriptového klienta.

![Diagram implicitního toku grantu](media/msal-authentication-flows/implicit-grant.svg)

Mnoho moderních webových aplikací je vytvořeno jako jednostránkové aplikace na straně klienta, které jsou napsané pomocí JavaScriptu nebo spa frameworku, například Angular, Vue.js a React.js. Tyto aplikace jsou spuštěny ve webovém prohlížeči a mají jiné charakteristiky ověřování než tradiční webové aplikace na straně serveru. Platforma identit společnosti Microsoft umožňuje jednostránkovým aplikacím přihlašovat uživatele a získat tokeny pro přístup k back-endovým službám nebo webovým rozhraním API pomocí implicitního toku udělení. Implicitní tok umožňuje aplikaci získat id tokeny reprezentovat ověřeného uživatele a také přístup tokeny potřebné k volání chráněných rozhraní API.

Tento tok ověřování nezahrnuje scénáře aplikací, které používají architektury JavaScript napříč platformami, jako jsou Electron a React-Native, protože vyžadují další funkce pro interakci s nativními platformami.

## <a name="authorization-code"></a>Autorizační kód

MSAL podporuje [udělení autorizačního kódu OAuth 2](v2-oauth2-auth-code-flow.md). Tento grant lze použít v aplikacích, které jsou nainstalovány na zařízení získat přístup k chráněným prostředkům, jako jsou například webové rozhraní API. To vám umožní přidat přihlášení a přístup k rozhraní API do mobilních aplikací a aplikací pro počítače. 

Když se uživatelé přihlašují k webovým aplikacím (webům), webová aplikace obdrží autorizační kód.  Autorizační kód je uplatněn k získání tokenu pro volání webových api. Ve ASP.NET a ASP.NET webových `AcquireTokenByAuthorizationCode` aplikací core je jediným cílem přidat token do mezipaměti tokenů. Token pak může použít aplikace (obvykle v řadičích, které právě získat token `AcquireTokenSilent`pro rozhraní API pomocí ).

![Diagram toku autorizačního kódu](media/msal-authentication-flows/authorization-code.png)

V předchozím diagramu aplikace:

1. Požaduje autorizační kód, který je uplatněn pro přístupový token.
2. Používá přístupový token k volání webového rozhraní API.

### <a name="considerations"></a>Požadavky

- Autorizační kód můžete použít pouze jednou k uplatnění tokenu. Nepokoušejte se získat token vícekrát se stejným autorizačním kódem (je výslovně zakázánstandardní specifikací protokolu). Pokud kód uplatníte několikrát úmyslně nebo protože si nejste vědomi toho, že rámec to také dělá za vás, zobrazí se následující chyba:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Pokud píšete ASP.NET nebo ASP.NET základní aplikaci, může k tomu dojít, pokud nesdělíte rámci, že jste již autorizační kód uplatnili. K tomu je třeba `context.HandleCodeRedemption()` volat metodu obslužné rutiny `AuthorizationCodeReceived` události.

- Vyhněte se sdílení přístupového tokenu s ASP.NET, což může zabránit správnému souhlasu. Další informace naleznete v [tématu problém #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>Jménem

MSAL podporuje [OAuth 2 jménem toku ověřování](v2-oauth2-on-behalf-of-flow.md).  Tento tok se používá, když aplikace vyvolá službu nebo webové rozhraní API, které zase potřebuje volat jinou službu nebo webové rozhraní API. Cílem je šířit delegovanou identitu uživatele a oprávnění prostřednictvím řetězce požadavků. Aby služba střední vrstvy mohla provádět ověřené požadavky na službu navazujícím vysílání, musí jménem uživatele zabezpečit přístupový token z platformy identit microsoftu.

![Diagram toku](media/msal-authentication-flows/on-behalf-of.png)

V předchozím schématu:

1. Aplikace získá přístupový token pro webové rozhraní API.
2. Klient (webová, desktopová, mobilní nebo jednostránková aplikace) volá chráněné webové rozhraní API a přidává přístupový token jako nosný token do ověřovací hlavičky požadavku HTTP. Webové rozhraní API ověřuje uživatele.
3. Když klient volá webové rozhraní API, webové rozhraní API požaduje jiný token jménem uživatele.  
4. Chráněné webové rozhraní API používá tento token k volání podřízenéwebové rozhraní API jménem uživatele.  Webové rozhraní API můžete také později požadovat tokeny pro jiné rozhraní API pro příjem dat (ale stále jménem stejného uživatele).

## <a name="client-credentials"></a>Přihlašovací údaje klienta

MSAL podporuje [tok pověření klienta OAuth 2](v2-oauth2-client-creds-grant-flow.md). Tento tok umožňuje přístup k webovým hostovaným prostředkům pomocí identity aplikace. Tento typ grantu se běžně používá pro interakce mezi servery, které musí být spuštěny na pozadí, bez okamžité interakce s uživatelem. Tyto typy aplikací jsou často označovány jako daemons nebo účty služeb. 

Tok pověření klienta umožňuje webové službě (důvěrnému klientovi) používat vlastní pověření namísto zosobnění uživatele k ověření při volání jiné webové služby. V tomto scénáři klient je obvykle webové služby střední vrstvy, daemon služby nebo webu. Pro vyšší úroveň záruky platforma identit microsoftu také umožňuje volající službě používat certifikát (namísto sdíleného tajného klíče) jako pověření.

> [!NOTE]
> Důvěrný tok klienta není k dispozici na mobilních platformách (UPW, Xamarin.iOS a Xamarin.Android), protože tyto podporují pouze veřejné klientské aplikace. Veřejné klientské aplikace nevědí, jak prokázat identitu aplikace zprostředkovateli identity. Zabezpečeného připojení lze dosáhnout na webových aplikací nebo webových api back-endů nasazením certifikátu.

MSAL.NET podporuje dva typy pověření klienta. Tyto přihlašovací údaje klienta je třeba zaregistrovat pomocí služby Azure AD. Pověření jsou předány konstruktory důvěrné klientské aplikace ve vašem kódu.

### <a name="application-secrets"></a>Tajné klíče aplikace

![Diagram důvěrného klienta s heslem](media/msal-authentication-flows/confidential-client-password.png)

V předchozím diagramu aplikace:

1. Získá token pomocí tajného klíče aplikace nebo hesla pověření.
2. Používá token k vyžádání prostředků.

### <a name="certificates"></a>Certifikáty

![Diagram důvěrného klienta s certifikátem](media/msal-authentication-flows/confidential-client-certificate.png)

V předchozím diagramu aplikace:

1. Získá token pomocí pověření certifikátu.
2. Používá token k vyžádání prostředků.

Tato pověření klienta musí být:
- Registrované s Azure AD.
- Předáno při konstrukci důvěrné klientské aplikace ve vašem kódu.

## <a name="device-code"></a>Kód zařízení

MSAL podporuje [tok kódu zařízení OAuth 2](v2-oauth2-device-code.md), který umožňuje uživatelům přihlásit se k zařízením s omezenými vstupy, jako je například inteligentní televize, zařízení IoT nebo tiskárna. Interaktivní ověřování pomocí Azure AD vyžaduje webový prohlížeč. Tok kódu zařízení umožňuje uživateli používat jiné zařízení (například jiný počítač nebo mobilní telefon) k interaktivnímu přihlášení, kde zařízení nebo operační systém neposkytuje webový prohlížeč.

Pomocí toku kódu zařízení aplikace získá tokeny prostřednictvím dvoustupňového procesu speciálně určeného pro tato zařízení nebo operační systémy. Příklady takových aplikací zahrnují aplikace spuštěné na zařízeních IoT nebo nástrojích příkazového řádku (CLI). 

![Diagram toku kódu zařízení](media/msal-authentication-flows/device-code.png)

V předchozím schématu:

1. Kdykoli je vyžadováno ověření uživatele, aplikace poskytne kód a požádá uživatele, aby použil jiné zařízení (například smartphone `https://microsoft.com/devicelogin`připojený k internetu) k zadání adresy URL (například ). Uživatel je pak vyzván k zadání kódu a pokračuje prostřednictvím normálního ověřování, včetně výzvy k souhlasu a vícefaktorové ověřování v případě potřeby.

2. Po úspěšném ověření aplikace příkazového řádku obdrží požadované tokeny prostřednictvím zadního kanálu a používá je k provádění volání webového rozhraní API, které potřebuje.

### <a name="constraints"></a>Omezení

- Tok kódu zařízení je k dispozici pouze ve veřejných klientských aplikacích.
- Orgán předaný při vytváření aplikace veřejného klienta musí být jedním z následujících:
  - Tenanted (formuláře, `https://login.microsoftonline.com/{tenant}/` `{tenant}` kde je buď GUID představující ID klienta nebo domény přidružené k tenantovi).
  - Pro všechny pracovní a`https://login.microsoftonline.com/organizations/`školní účty ( ).
- Osobní účty Microsoftu ještě nepodporují koncový bod Azure AD v2.0 `/common` (nemůžete používat nebo `/consumers` tenanty).

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows

MSAL podporuje integrované ověřování systému Windows (IWA) pro desktopové nebo mobilní aplikace, které běží na doméně spojené nebo Azure AD připojil počítač se systémem Windows. Pomocí IWA, tyto aplikace můžete získat token tiše (bez jakékoli interakce uživatelského rozhraní od uživatele). 

![Diagram integrovaného ověřování systému Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

V předchozím diagramu aplikace:

1. Získá token pomocí integrovaného ověřování systému Windows.
2. Používá token k vyžádání prostředků.

### <a name="constraints"></a>Omezení

IWA podporuje pouze federované uživatele, což znamená, že uživatelé vytvořené ve službě Active Directory a podporované službou Azure AD. Uživatelé vytvořená přímo ve službě Azure AD bez podpory služby Active Directory (spravovaní uživatelé) nemohou tento tok ověřování používat. Toto omezení nemá vliv na [tok uživatelského jména/hesla](#usernamepassword).

IWA je pro aplikace napsané pro rozhraní .NET Framework, .NET Core a platformy Univerzální platformy Windows.

IWA neobceme vícefaktorové ověřování. Pokud je nakonfigurováno vícefaktorové ověřování, může iwa selhat, pokud je vyžadována výzva vícefaktorového ověřování. Vícefaktorové ověřování vyžaduje interakci uživatele.

Neřídíte, kdy poskytovatel identity požaduje dvoufaktorové ověřování, které má být provedeno. Správce klienta ano. Dvoufaktorové ověřování je obvykle vyžadováno při přihlášení z jiné země, když nejste připojeni přes VPN k podnikové síti a někdy i když jste připojeni přes VPN. Azure AD používá AI průběžně zjistit, pokud je vyžadováno dvoufaktorové ověřování. Pokud iwa selže, měli byste se vrátit k [interaktivní uživatelské výzvě] (#interactive).

Orgán předaný při vytváření aplikace veřejného klienta musí být jedním z následujících:
- Tenanted (formuláře, `https://login.microsoftonline.com/{tenant}/` `tenant` kde je buď identifikátor guid představující ID klienta nebo domény přidružené k tenantovi).
- Pro všechny pracovní a`https://login.microsoftonline.com/organizations/`školní účty ( ). Osobní účty Microsoft nejsou podporované (nemůžete `/common` `/consumers` používat ani klienty).

Vzhledem k tomu, že IWA je tichý tok, musí být splněna jedna z následujících skutečností:
- Uživatel vaší aplikace musí mít dříve souhlas s použitím aplikace. 
- Správce klienta musí mít dříve souhlas pro všechny uživatele v tenantovi používat aplikaci.

To znamená, že platí jedna z následujících skutečností:
- Vy jako vývojář jste vybrali **Grant** na webu Azure portal pro sebe.
- Správce klienta vybral **souhlas správce udělení/odvolání pro {doménu klienta}** na kartě **oprávnění rozhraní API** registrace aplikace (viz Přidání oprávnění pro přístup k [webovým rozhraním API).](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)
- Poskytli jste uživatelům způsob, jak k aplikaci udělit souhlas (viz [Žádost o souhlas jednotlivých uživatelů](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Poskytli jste způsob, jak správce klienta souhlas pro aplikaci (viz [souhlas správce](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

Tok IWA je povolen pro desktopové aplikace .NET, .NET Core a aplikace univerzální platformy Windows. V centru .NET core je nutné zadat uživatelské jméno iwa, protože .NET Core nelze získat uživatelská jména z operačního systému.
  
Další informace o souhlasu naleznete [v tématu oprávnění a souhlas v 2.0](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Uživatelské jméno a heslo

MSAL podporuje [odejmuta hesla vlastníka prostředku OAuth 2 ,](v2-oauth-ropc.md)který umožňuje aplikaci přihlásit uživatele přímo zpracováním jejich hesla. V desktopové aplikaci můžete použít tok uživatelského jména a hesla k tichému získání tokenu. Při používání aplikace není vyžadováno žádné uj.

![Diagram toku uživatelského jména/hesla](media/msal-authentication-flows/username-password.png)

V předchozím diagramu aplikace:

1. Získá token odesláním uživatelskéjméno a heslo k poskytovateli identity.
2. Volá webové rozhraní API pomocí tokenu.

> [!WARNING]
> Tento tok se nedoporučuje. To vyžaduje vysoký stupeň důvěry a expozice uživatele.  Tento tok byste měli použít pouze v případě, že nelze použít jiné, bezpečnější toky. Další informace naleznete [v tématu Jaké je řešení rostoucího problému hesel?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Upřednostňovaným tokem pro tiché získání tokenu v počítačích s doménou windows je [integrované ověřování systému Windows](#integrated-windows-authentication). V opačném případě můžete také použít [tok kódu zařízení](#device-code).

I když je to užitečné v některých případech (Scénáře DevOps), pokud chcete použít uživatelské jméno nebo heslo v interaktivních scénářích, kde zadáte vlastní uživatelské rozhraní, zkuste se mu vyhnout. Pomocí uživatelského jména/hesla:
- Uživatelé, kteří potřebují provést vícefaktorové ověřování, se nebudou moci přihlásit (protože neexistuje žádná interakce).
- Uživatelé nebudou moci provést jednotné přihlášení.

### <a name="constraints"></a>Omezení

Kromě [omezení integrovaného ověřování systému Windows](#integrated-windows-authentication)platí také následující omezení:

- Tok uživatelského jména a hesla není kompatibilní s podmíněným přístupem a vícefaktorovým ověřováním. V důsledku toho pokud vaše aplikace běží v tenantovi Azure AD, kde správce tenanta vyžaduje vícefaktorové ověřování, nemůžete použít tento tok. To dělá mnoho organizací.
- Funguje pouze pro pracovní a školní účty (ne účty Microsoft).
- Tok je k dispozici na ploše .NET a .NET Core, ale ne na univerzální platformě Windows.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C specifika

Další informace o používání MSAL.NET a Azure AD B2C najdete [v tématu použití ROPC s Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
