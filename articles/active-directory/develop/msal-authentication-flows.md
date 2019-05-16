---
title: Toky ověřování (knihovna Microsoft Authentication Library) | Azure
description: Další informace o ověřování toky/uděluje použít pomocí Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb9a6f162a10408469669cf40b29efc6d2903944
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546053"
---
# <a name="authentication-flows"></a>Toky ověřování

Tento článek popisuje různé ověřování toků Microsoft Authentication Library (MSAL) k dispozici.  Tyto toky je možné v řadě různých scénářích aplikací.

| Tok | Popis | Použít v|  
| ---- | ----------- | ------- | 
| [Interaktivní](#interactive) | Získá token vyzývající uživatele k zadání přihlašovacích údajů prostřednictvím prohlížeče nebo místní okno, interaktivní proces. | [Aplikace klasické pracovní plochy](scenario-desktop-overview.md), [mobilní aplikace](scenario-mobile-overview.md) |
| [Implicitní grant](#implicit-grant) | Umožňuje aplikaci získat tokeny bez provedení výměnou přihlašovacích údajů back-end serveru. To umožňuje aplikaci pro uživatele, Udržovat relaci a získat tokeny do dalších webových rozhraní API vše v rámci klienta kódu jazyka JavaScript.| [Jednostránkové aplikace (SPA)](scenario-spa-overview.md) |
| [autorizační kód](#authorization-code) | Použít v aplikacích, které jsou nainstalované v zařízení získat přístup k chráněným prostředkům, jako je například webové rozhraní API. To umožňuje přidat přihlášení a přístup k rozhraní API do vašich mobilních a desktopových aplikací. | [Aplikace klasické pracovní plochy](scenario-desktop-overview.md), [mobilní aplikace](scenario-mobile-overview.md), [webové aplikace](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Aplikace volá služby/webové rozhraní API, která zase potřebuje k volání jiné služby nebo webové rozhraní API. Cílem je rozšíření delegovaný uživatel identit a oprávnění pomocí řetězce požadavku. | [Webová rozhraní API](scenario-web-api-call-api-overview.md) |
| [Přihlašovací údaje klienta](#client-credentials) | Umožňuje přístup k prostředkům hostované webové pomocí identity aplikace. Běžně používá pro interakce – servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. | [Aplikace démonů](scenario-daemon-overview.md) |
| [Kód zařízení](#device-code) | Umožňuje uživatelům umožní přihlásit k omezené vstupní zařízení, jako jsou inteligentní TV, zařízení IoT nebo tiskárny. | [Desktop nebo Mobile apps](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Ověření integrované Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Umožňuje aplikacím v doméně nebo v Azure AD připojených k počítačům získat token tiše (bez nutnosti zásahu uživatelského rozhraní od uživatele).| [Desktop nebo Mobile apps](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Uživatelské jméno/heslo](scenario-desktop-acquire-token.md#username--password) | Umožňuje aplikaci přihlásit uživatele pomocí přímo zpracování své heslo. Tento tok se nedoporučuje. | [Desktop nebo mobile apps](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interaktivní
Knihovna MSAL podporuje schopnost interaktivně vyzvat uživatele k zadání přihlašovacích údajů přihlásit a získat token pomocí těchto přihlašovacích údajů.

![Interaktivní tok](media/msal-authentication-flows/interactive.png)

Další informace o pomocí MSAL.NET interaktivně získat tokeny na konkrétní platformy, přečtěte si následující:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Univerzální platforma Windows](msal-net-uwp-considerations.md)

Další informace o interaktivní volání ve MSAL.js [výzvu chování v MSAL.js interaktivních požadavků](msal-js-prompt-behavior.md)

## <a name="implicit-grant"></a>Implicitní udělení

Podporuje MSAL [OAuth 2 implicitní tok poskytování](v2-oauth2-implicit-grant-flow.md), který umožňuje aplikaci získat tokeny z Microsoft identity platform bez provedení back-end serveru výměnou přihlašovacích údajů. To umožňuje aplikaci pro uživatele, Udržovat relaci a získat tokeny do dalších webových rozhraní API vše v rámci klienta kódu jazyka JavaScript.

![Implicitní grant toku](media/msal-authentication-flows/implicit-grant.svg)

Řada moderních webových aplikací jsou vytvořené jako na straně klienta jednostránkové aplikace napsané v jazyce JavaScript nebo nějaké SPA rozhraní, například Angular, Vue.js a React.js. Tyto aplikace spouštět ve webovém prohlížeči a mají různé ověřování vlastnosti než tradiční na straně serveru webové aplikace. Platforma identit Microsoft umožňuje jednostránkové aplikace přihlásit uživatele a získat tokeny pro přístup k back-endovým službám nebo webovým rozhraním API pomocí implicitního udělení toku. Implicitní tok umožňuje, aby aplikace k získání ID tokenů pro reprezentaci ověřeného uživatele a také přístupové tokeny, které jsou potřebné k volání rozhraní API služby chráněný.

Tento tok ověřování nezahrnuje scénáře aplikací pomocí rozhraní JavaScript napříč platformami jako elektronovým a React-Native, protože vyžadují další možnosti pro interakci s nativní platformy.

## <a name="authorization-code"></a>Autorizační kód
Podporuje MSAL [udělení autorizačního kódu OAuth 2](v2-oauth2-auth-code-flow.md), které lze použít v aplikacích, které jsou nainstalované v zařízení získat přístup k chráněným prostředkům, jako je například webové rozhraní API. To umožňuje přidat přihlášení a přístup k rozhraní API do vašich mobilních a desktopových aplikací. 

Po přihlášení uživatelů do webových aplikací (webové servery) webová aplikace obdrží autorizační kód.  K získání tokenu pro volání webových rozhraní API je uplatnit autorizační kód. V technologii ASP.NET nebo ASP.NET core webových aplikací, jenom cílem `AcquireTokenByAuthorizationCode` je přidat token do mezipaměti tokenů, takže je pak možné aplikaci (obvykle v zařízení), které právě získání tokenu pro rozhraní API pomocí `AcquireTokenSilent`.

![Tok autorizačního kódu](media/msal-authentication-flows/authorization-code.png)

1. Požádá o autorizační kód, který je uplatnit na přístupový token.
2. Přístupový token se používá k volání webového rozhraní API.

### <a name="considerations"></a>Požadavky
- Autorizační kód je použitelné pouze jednou pro uplatnění token. Nepokoušejte se k získání tokenu s více než jednou stejné autorizační kód (ji výslovně zakazuje standardní specifikace protokolu). Pokud jste redeem kód několikrát záměrně, nebo protože si jich nejste vědomi, že architektura také to udělá za vás, získáte k chybě: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Pokud píšete aplikaci ASP.NET/ASP.NET Core, k tomu může dojít, že pokud není informování rozhraní framework ASP.NET/Core, který jste už využili. autorizační kód. V takovém případě je potřeba volat `context.HandleCodeRedemption()` metodu `AuthorizationCodeReceived` obslužné rutiny události.

- Vyhněte se sdílení přístupový token pomocí technologie ASP.NET, které by mohly bránit přírůstkové souhlasu správně děje.  Další informace najdete v tématu [vydat #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

Podporuje MSAL [tok on-behalf-of ověřování OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Tento postup se používá při aplikace vyvolá služby/webové rozhraní API, která zase potřebuje k volání jiné služby nebo webové rozhraní API. Cílem je rozšíření delegovaný uživatel identit a oprávnění pomocí řetězce požadavku. Pro služby střední vrstvy, aby ověřených požadavků pro příjem dat služby je potřeba zabezpečit přístupového tokenu z platforma identit Microsoft jménem uživatele.

![Tok On-Behalf-Of](media/msal-authentication-flows/on-behalf-of.png)

1. Získá přístupový token pro webové rozhraní API
2. Klient (webových, mobilních a jednostránkové aplikace klasické pracovní plochy,) volá chráněné webové rozhraní API, přidání přístupový token jako token nosiče v ověřovací hlavičku požadavku HTTP. Webové rozhraní API ověří uživatele.
3. Když klient volá webové rozhraní API, webové rozhraní API požádá o jiný token on-behalf-of uživatele.  
4. Chráněné webové rozhraní API používá tento token volat podřízené webové rozhraní API on-behalf-of uživatele.  Požádat o tokeny webového rozhraní API také později pro ostatní rozhraní API pro příjem dat (ale pořád jménem stejného uživatele).

## <a name="client-credentials"></a>Přihlašovací údaje klienta

Podporuje MSAL [údajů klienta OAuth 2, flow](v2-oauth2-client-creds-grant-flow.md). Tento tok umožňuje přístup k prostředkům hostované webové pomocí identity aplikace. Tento typ udělení se běžně používá pro interakce – servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. Tyto typy aplikací jsou často označovány jako účty služby nebo procesy démon. 

Přihlašovací údaje klienta udělení povolení toku webové službě (důvěrnému klientovi) ověření při volání jiné webové služby pomocí vlastních přihlašovacích údajů, místo zosobňování uživatele. V tomto scénáři klient je obvykle střední vrstvy webové služby, služba démona nebo webová stránka. Pro vyšší úroveň záruky platforma identit Microsoft také umožňuje volání služby k používání certifikátu (ne sdílený tajný klíč) jako pověření.

> [!NOTE]
> Tok důvěrnému klientovi není k dispozici na mobilní platformy (UPW, Xamarin.iOS a Xamarin.Android), protože tyto veřejné klientské aplikace podporují jenom.  Veřejné klientské aplikace není zřejmé, jak k prokázání identity aplikace ke zprostředkovateli Identity. Zabezpečené připojení lze nastavit ve webové aplikaci nebo webové rozhraní API back EndY nasazením certifikátu.

MSAL.NET podporuje dva typy přihlašovacích údajů klienta. Tyto přihlašovací údaje pro klienta musí být zaregistrovaní v Azure AD. Přihlašovací údaje jsou předány v konstruktory důvěrnému klientovi aplikace ve vašem kódu.

### <a name="application-secrets"></a>Tajných klíčů aplikací 

![Důvěrnému klientovi s heslem](media/msal-authentication-flows/confidential-client-password.png)

1. Získá token pomocí přihlašovacích údajů tajný klíč nebo heslo aplikace.
2. Token, který se používá k podání žádostí o prostředku.

### <a name="certificates"></a>Certifikáty 

![Důvěrnému klientovi pomocí certifikátu](media/msal-authentication-flows/confidential-client-certificate.png)

1. Získá token pomocí přihlašovacích údajů pro certifikát.
2. Token, který se používá k podání žádostí o prostředku.

Tyto přihlašovací údaje klienta musí být:
- V Azure AD zaregistrováno.
- Předaná v procesu vytváření důvěrnému klientovi aplikace ve vašem kódu.


## <a name="device-code"></a>Kód zařízení
Podporuje MSAL [tok OAuth 2 zařízení kódu](v2-oauth2-device-code.md), což umožňuje uživatelům umožní přihlásit k omezené vstupní zařízení, jako jsou inteligentní TV, zařízení IoT nebo tiskárny. Interaktivní ověřování pomocí Azure AD vyžaduje webový prohlížeč. Tok kódu zařízení vám umožní používat jiné zařízení (například jiného počítače nebo mobilního telefonu) se přihlásit interaktivně kde zařízení nebo operační systém neposkytuje webový prohlížeč.

Pomocí toku kódu zařízení, aplikace získá tokeny krocích zvláště určené pro zařízení nebo operačního systému. Příkladem takové aplikace jsou aplikace běžící na zařízení iOT nebo nástroje příkazového řádku (CLI). 

![Tok kódu zařízení](media/msal-authentication-flows/device-code.png)

1. Pokaždé, když se vyžaduje ověření uživatele, poskytuje kód a žádá uživatele, aby pomocí jiného zařízení (jako jsou připojené k Internetu smartphone) přejděte na adresu URL aplikace (například https://microsoft.com/devicelogin), ve kterém uživateli zobrazí výzva k zadání kódu. Že budete hotovi, webové stránky přejde uživatele prostřednictvím normální ověřování prostředí, a v případě potřeby včetně výzev k udělení souhlasu a ověřování službou Multi-Factor Authentication.

2. Po úspěšném ověření aplikace příkazového řádku bude přijímat požadované tokeny prostřednictvím používající back channel a použije ho k provádění volání webové rozhraní API, které potřebuje.

### <a name="constraints"></a>Omezení

- Tok kódu při zařízení je dostupná pouze na veřejné klientské aplikace.
- Oprávnění předávané při vytváření aplikace veřejným klientem musí být:
  - klienty (ve formátu `https://login.microsoftonline.com/{tenant}/` kde `{tenant}` je buď identifikátor GUID představující ID tenanta nebo domény přidružené k tenantovi.
  - nebo jakékoli pracovním a školním účtům (`https://login.microsoftonline.com/organizations/`).
- Osobní účty Microsoft se zatím nepodporují pomocí koncového bodu Azure AD v2.0 (nelze použít `/common` nebo `/consumers` tenantů).

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows
Knihovna MSAL podporuje integrované ověřování Windows (IWA) pro stolní počítače nebo mobilní aplikace, které běží na Azure AD nebo připojené k doméně připojené k počítači s Windows. Pomocí IWA, tyto aplikace můžete získat token tiše (bez nutnosti zásahu uživatelského rozhraní od uživatele). 

![Integrované ověřování systému Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

1. Získá token pomocí integrovaného ověřování Windows.
2. Token, který se používá k podání žádostí o prostředku.

### <a name="constraints"></a>Omezení

Podporuje IWA **federativní** jenom uživatele.  Uživatelé vytvoření ve službě Active Directory a podporovaný službou Azure Active Directory. Uživatelé vytvořit přímo ve službě Azure AD, bez zálohování AD (**spravované** uživatelé) nelze pomocí tohoto toku ověřování. Toto omezení nemá vliv [tok uživatelského jména a hesla](#usernamepassword).

IWA je pro aplikace napsané pro platformy .NET Framework, .NET Core a univerzální platformu Windows.

IWA není obejít MFA (vícefaktorové ověřování). Pokud je nakonfigurované vícefaktorové ověřování, IWA může selhat, pokud ověřovacím testem MFA se vyžaduje, protože MFA vyžaduje zásah uživatele. Tento příklad je velmi obtížné. IWA je jako neinteraktivní, ale dvojúrovňového ověřování (2FA) vyžaduje s uživatelem. Můžete neovládají když poskytovatel identity požádá o 2FA, která se má provést, provede správce tenanta. Z našich pozorování se vyžaduje 2FA, při přihlášení z jiné země, když nejsou připojené prostřednictvím sítě VPN k podnikové síti a to někdy i v případě připojení prostřednictvím VPN. Neočekáváme, že deterministické sadu pravidel, Azure Active Directory používá průběžně další, pokud se vyžaduje 2FA AI. Měli byste nouzového řešení ověření pomocí výzvy uživateli (https://aka.ms/msal-net-interactive) IWA selže.

Oprávnění předávané při vytváření aplikace veřejným klientem musí být:
- klienty (ve formátu `https://login.microsoftonline.com/{tenant}/` kde `tenant` je buď identifikátor guid představující ID tenanta nebo domény přidružené k tenantovi.
- pro všechny pracovních a školních účtů (`https://login.microsoftonline.com/organizations/`). Osobní účty Microsoft se nepodporují (nelze použít `/common` nebo `/consumers` tenantů).

Protože IWA je pasivní tok:
- k používání aplikace musí mít dříve schválená uživatelem vaší aplikace. 
- nebo správce tenanta musí mít dříve souhlas pro všechny uživatele v tenantovi k používání aplikace.
- To znamená, že:
    - buď jako vývojář stisknutí **udělení** tlačítko na webu Azure portal pro sebe, 
    - nebo správce tenanta stiskla **udělení nebo odvolání souhlasu správce pro {doména tenanta}** tlačítko **oprávnění k rozhraní API** kartu registrace aplikace (naleznete v tématu [přidat oprávnění pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis))
    - nebo jste zadali způsob, jak uživatelům udělit souhlas s aplikace (naleznete v tématu [vyžádání souhlasu uživatele](v2-permissions-and-consent.md#requesting-individual-user-consent))
    - nebo jste zadali způsob, jak správce tenanta o souhlasu pro aplikaci (viz [souhlas správce](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))

Pro .NET desktop, .NET Core a aplikací pro univerzální platformu Windows je povolen tok IWA. V rozhraní .NET Core je k dispozici pouze přetížení přijímající uživatelské jméno, podle platformy .NET Core nemůžete se ptát uživatelské jméno pro operační systém.
  
Další informace o souhlas, naleznete v tématu [v2.0 oprávnění a vyjádření souhlasu](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Uživatelské jméno a heslo 
Podporuje MSAL [udělení přihlašovacího hesla vlastníka prostředku OAuth 2](v2-oauth-ropc.md), který umožňuje aplikaci přihlásit uživatele pomocí přímo zpracování své heslo. V desktopové aplikaci můžete použít tok uživatelského jména a hesla k získání tokenu bezobslužně. Vyžaduje se žádné uživatelské rozhraní, při používání aplikace.

![Tok uživatelského jména a hesla](media/msal-authentication-flows/username-password.png)

1. Získá token odesláním uživatelské jméno a heslo ke zprostředkovateli identity.
2. Volání webového rozhraní API pomocí tokenu.

> [!WARNING]
> Tento tok je **ale nedoporučený krok** protože nevyžaduje vysoký stupeň důvěryhodnosti a uživatel vystavení.  Tento tok byste měli používat jenom v případě, že toky, které bezpečnější, nelze použít. Další informace o tomto problému najdete v tématu [v tomto článku](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Upřednostňované tok pro získání tokenu tiše na počítačích připojených k doméně Windows je [integrované ověřování Windows](#integrated-windows-authentication). V opačném případě můžete použít také [toku kódu zařízení](#device-code)

I když to je užitečné v některých případech (scénáře DevOps), pokud chcete pomocí uživatelského jména a hesla v interaktivních scénářů, ve kterém můžete zadat vlastní uživatelské rozhraní, byste ve skutečnosti uvažovat o tom, jak ho přestat používat. Pomocí uživatelského jména a hesla, můžete se poskytuje up několik věcí:
- základní tenantů moderního identity: získá podléhá heslo, znovu přehrát. Když máme tento koncept tajného kódu sdílené složky, do které může.
To není kompatibilní s passwordless.
- Uživatelé, kteří vyžadují vícefaktorové ověřování nebudou můžou přihlašovat (protože není žádná interakce s)
- Uživatelé nebudou moct jednotného přihlašování

### <a name="constraints"></a>Omezení

Kromě [integrované ověřování Windows omezení](#integrated-windows-authentication), také platí následující omezení:

- Tok uživatelského jména a hesla není kompatibilní s podmíněného přístupu a ověřování službou Multi-Factor Authentication: Pokud vaše aplikace běží v tenantovi Azure AD, kde správce tenanta požaduje ověření službou Multi-Factor Authentication, v důsledku toho nelze pomocí tohoto toku. Mnoho organizací to udělat.
- Funguje pouze pro pracovní a školní účty (ne MSA).
- Tok je k dispozici na ploše .NET a .NET core, ale není na univerzální platformu Windows.

### <a name="azure-ad-b2c-specifics"></a>Specifika Azure AD B2C

Další informace o použití MSAL.NET a Azure AD B2C, přečtěte si [pomocí ROPC s Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
