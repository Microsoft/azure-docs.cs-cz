---
title: Toky ověřování (knihovna Microsoft Authentication Library) | Azure
description: Další informace o ověřování toků a uděluje použít pomocí Microsoft Authentication Library (MSAL).
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
ms.openlocfilehash: b7ba6ae188c098e85573503a1518ba65480d713a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807212"
---
# <a name="authentication-flows"></a>Toky ověřování

Tento článek popisuje různé ověřování toků Microsoft Authentication Library (MSAL) k dispozici.  Tyto toky je možné v řadě různých scénářích aplikací.

| Tok | Popis | Použít v|  
| ---- | ----------- | ------- | 
| [Interaktivní](#interactive) | Získá token interaktivní proces vyzývající uživatele k zadání přihlašovacích údajů prostřednictvím prohlížeče nebo automaticky otevírané okno. | [Aplikace klasické pracovní plochy](scenario-desktop-overview.md), [mobilní aplikace](scenario-mobile-overview.md) |
| [Implicitní grant](#implicit-grant) | Umožňuje aplikaci získat tokeny bez provedení výměnou přihlašovacích údajů back endového serveru. To umožňuje aplikaci pro uživatele, Udržovat relaci a získat tokeny do dalších webových rozhraní API, vše v rámci klienta kódu jazyka JavaScript.| [Jednostránkové aplikace (SPA)](scenario-spa-overview.md) |
| [autorizační kód](#authorization-code) | Použít v aplikacích, které jsou nainstalované v zařízení získat přístup k chráněným prostředkům, jako je například webové rozhraní API. To umožňuje přidat přihlášení a přístup k rozhraní API do vašich mobilních a desktopových aplikací. | [Aplikace klasické pracovní plochy](scenario-desktop-overview.md), [mobilní aplikace](scenario-mobile-overview.md), [webové aplikace](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Aplikace volá webové rozhraní API, která zase potřebuje k volání jiné služby nebo webové rozhraní API a služby. Cílem je rozšíření delegovaný uživatel identit a oprávnění pomocí řetězce požadavku. | [Webová rozhraní API](scenario-web-api-call-api-overview.md) |
| [Přihlašovací údaje klienta](#client-credentials) | Umožňuje přístup k prostředkům hostované webové pomocí identity aplikace. Běžně používá pro interakce – servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. | [Aplikace démonů](scenario-daemon-overview.md) |
| [Kód zařízení](#device-code) | Umožňuje uživatelům umožní přihlásit k omezené vstupní zařízení, jako jsou inteligentní TV, zařízení IoT nebo tiskárny. | [Desktop nebo mobile apps](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Ověření integrované Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Umožňuje počítačům aplikace v doméně nebo v Azure Active Directory (Azure AD) připojených k získání tokenu tiše (bez nutnosti zásahu uživatelského rozhraní od uživatele).| [Desktop nebo mobile apps](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Uživatelské jméno/heslo](scenario-desktop-acquire-token.md#username--password) | Umožňuje aplikaci přihlásit uživatele pomocí přímo zpracování své heslo. Tento tok se nedoporučuje. | [Desktop nebo mobile apps](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interaktivní
Knihovna MSAL podporuje schopnost interaktivně vyzvat uživatele k zadání přihlašovacích údajů přihlásit a získat token s použitím těchto přihlašovacích údajů.

![Diagram interaktivního toku](media/msal-authentication-flows/interactive.png)

Další informace o použití MSAL.NET interaktivně získat tokeny na konkrétní platformy naleznete v tématu:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Univerzální platforma Windows](msal-net-uwp-considerations.md)

Další informace o interaktivní volání ve MSAL.js najdete v tématu [výzvu chování v interaktivních požadavků MSAL.js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicitní grant

Podporuje MSAL [OAuth 2 implicitní tok poskytování](v2-oauth2-implicit-grant-flow.md), který umožňuje aplikaci získat tokeny z Microsoft identity platform bez provedení back endového serveru výměnou přihlašovacích údajů. To umožňuje aplikaci pro uživatele, Udržovat relaci a získat tokeny do dalších webových rozhraní API, vše v rámci klienta kódu jazyka JavaScript.

![Diagram toku implicitní udělení](media/msal-authentication-flows/implicit-grant.svg)

Řada moderních webových aplikací jsou vytvořené jako aplikace na straně klienta, jedna stránka, vytvořené pomocí jazyka JavaScript nebo rozhraní jednostránková aplikace, například Angular, Vue.js a React.js. Tyto aplikace spouštět ve webovém prohlížeči a mají různé ověřování vlastnosti než tradiční na straně serveru webové aplikace. Platforma identit Microsoft umožňuje jednostránkové aplikace přihlásit uživatele a získat tokeny pro přístup k back endovým službám nebo webovým rozhraním API, s použitím implicitního udělení toku. Implicitní tok umožňuje, aby aplikace k získání ID tokenů pro reprezentaci ověřeného uživatele a také přístupové tokeny, které jsou potřebné k volání rozhraní API služby chráněný.

Tento tok ověřování neobsahuje scénáře aplikací, které používají rozhraní JavaScript napříč platformami jako elektronovým a React-Native, protože vyžadují další možnosti pro interakci s nativní platformy.

## <a name="authorization-code"></a>autorizační kód
Podporuje MSAL [udělení autorizačního kódu OAuth 2](v2-oauth2-auth-code-flow.md). Tomuto grantu lze použít v aplikacích, které jsou nainstalované v zařízení získat přístup k chráněným prostředkům, jako je například webové rozhraní API. To umožňuje přidat přihlášení a přístup k rozhraní API do vašich mobilních a desktopových aplikací. 

Po přihlášení uživatelů do webových aplikací (websites) webová aplikace obdrží autorizační kód.  K získání tokenu pro volání webových rozhraní API je uplatnit autorizační kód. V ASP.NET a ASP.NET Core webových aplikací, jenom cílem `AcquireTokenByAuthorizationCode` je přidat token do mezipaměti tokenů. Token, který je pak možné aplikací (obvykle v seznamu zařízení, která právě získání tokenu pro rozhraní API s využitím `AcquireTokenSilent`).

![Diagram toku kódu autorizace](media/msal-authentication-flows/authorization-code.png)

V předchozím diagramu aplikace:

1. Požádá o autorizační kód, který je uplatnit na přístupový token.
2. Přístupový token se používá k volání webového rozhraní API.

### <a name="considerations"></a>Požadavky
- Můžete pouze jednou autorizační kód pro uplatnění token. Nepokoušejte se k získání tokenu s více než jednou stejné autorizační kód (ji výslovně zakazuje standardní specifikace protokolu). Pokud jste redeem kód několikrát záměrně, nebo protože si jich nejste vědomi, že architektura také to udělá za vás, získáte následující chybu: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Pokud píšete aplikaci ASP.NET nebo ASP.NET Core, k tomu může dojít, že pokud není informování rozhraní framework, který jste už využili autorizační kód. V takovém případě musíte zavolat `context.HandleCodeRedemption()` metodu `AuthorizationCodeReceived` obslužné rutiny události.

- Vyhněte se sdílení přístupový token pomocí technologie ASP.NET, které by mohly bránit přírůstkové souhlasu správně děje. Další informace najdete v tématu [vydat #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

Podporuje MSAL [tok on-behalf-of ověřování OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Tento postup se používá, když aplikace volá webové rozhraní API, která zase potřebuje k volání jiné služby nebo webové rozhraní API a služby. Cílem je rozšíření delegovaný uživatel identit a oprávnění pomocí řetězce požadavku. Pro služby střední vrstvy, aby ověřených požadavků pro příjem dat služby je potřeba zabezpečit přístupového tokenu z platforma identit Microsoft jménem uživatele.

![Diagram tok on-behalf-of](media/msal-authentication-flows/on-behalf-of.png)

Na předchozím obrázku:

1. Aplikace získá přístupový token pro webové rozhraní API.
2. Klient (web, desktopové, mobilní nebo jednostránkové aplikace) volá chráněné webové rozhraní API, přidání přístupový token jako token nosiče v ověřovací hlavičku požadavku HTTP. Webové rozhraní API ověří uživatele.
3. Když klient volá webové rozhraní API, webové rozhraní API požádá o jiný token on-behalf-of uživatele.  
4. Chráněné webové rozhraní API používá tento token volat podřízené webové rozhraní API on-behalf-of uživatele.  Webové rozhraní API může taky novější požadovat tokeny pro ostatní rozhraní API pro příjem dat (ale pořád jménem stejného uživatele).

## <a name="client-credentials"></a>Přihlašovací údaje klienta

Podporuje MSAL [údajů klienta OAuth 2, flow](v2-oauth2-client-creds-grant-flow.md). Tento tok umožňuje přístup k prostředkům hostované webové pomocí identity aplikace. Tento typ udělení se běžně používá pro interakce – servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. Tyto typy aplikací jsou často označovány jako účty služby nebo procesy démon. 

Přihlašovací údaje klienta udělení povolení toku webové službě (důvěrnému klientovi) ověření při volání jiné webové služby pomocí vlastních přihlašovacích údajů, místo zosobňování uživatele. V tomto scénáři klient je obvykle střední vrstvy webové služby, služba démona nebo webu. Pro vyšší úroveň záruky platforma identit Microsoft také umožňuje volání služby k používání certifikátu (ne sdílený tajný klíč) jako pověření.

> [!NOTE]
> Tok důvěrnému klientovi není k dispozici na mobilní platformy (UPW, Xamarin.iOS a Xamarin.Android), protože tyto veřejné klientské aplikace podporují jenom. Veřejné klientské aplikace není zřejmé, jak k prokázání identity aplikace ke zprostředkovateli Identity. Zabezpečené připojení lze nastavit ve webové aplikaci nebo webové rozhraní API zpět končí nasazením certifikátu.

MSAL.NET podporuje dva typy přihlašovacích údajů klienta. Tyto přihlašovací údaje pro klienta musí být zaregistrovaní v Azure AD. Přihlašovací údaje jsou předány v konstruktory důvěrnému klientovi aplikace ve vašem kódu.

### <a name="application-secrets"></a>Tajných klíčů aplikací 

![Diagram důvěrnému klientovi s heslem](media/msal-authentication-flows/confidential-client-password.png)

V předchozím diagramu aplikace:

1. Získá token s použitím přihlašovacích údajů tajný klíč nebo heslo aplikace.
2. Token, který se používá k podání žádostí o prostředku.

### <a name="certificates"></a>Certifikáty 

![Diagram důvěrnému klientovi pomocí certifikátu](media/msal-authentication-flows/confidential-client-certificate.png)

V předchozím diagramu aplikace:

1. Získá token s použitím přihlašovacích údajů certifikátu.
2. Token, který se používá k podání žádostí o prostředku.

Tyto přihlašovací údaje klienta musí být:
- V Azure AD zaregistrováno.
- Předaná v procesu vytváření důvěrnému klientovi aplikace ve vašem kódu.


## <a name="device-code"></a>Kód zařízení
Podporuje MSAL [tok OAuth 2 zařízení kódu](v2-oauth2-device-code.md), což umožňuje uživatelům umožní přihlásit k omezené vstupní zařízení, jako je inteligentní TV, zařízení IoT nebo tiskárny. Interaktivní ověřování pomocí Azure AD vyžaduje webový prohlížeč. Tok kódu zařízení vám umožní používat jiné zařízení (například jiného počítače nebo mobilního telefonu) se přihlásit interaktivně, ve kterém zařízení nebo operační systém neposkytuje webový prohlížeč.

S použitím toku kódu zařízení, aplikace získá tokeny krocích zvláště určené pro tato zařízení nebo operační systémy. Příkladem takové aplikace jsou spuštěné na zařízeních IoT nebo nástroje příkazového řádku (CLI). 

![Diagram toku kódu zařízení](media/msal-authentication-flows/device-code.png)

Na předchozím obrázku:

1. Pokaždé, když se vyžaduje ověření uživatele, tato aplikace poskytuje kód a požádá uživatele, aby pomocí jiného zařízení (jako jsou připojené k Internetu smartphone) přejděte na adresu URL (například https://microsoft.com/devicelogin). Uživatel je pak výzva k zadání kódu a pokračuje přes běžné ověřování prostředí, včetně výzev k udělení souhlasu a ověřování službou Multi-Factor Authentication v případě potřeby.

2. Po úspěšném ověření aplikace příkazového řádku přijímá požadované tokeny prostřednictvím používající back channel a používá k provedení volání webové rozhraní API, které potřebuje.

### <a name="constraints"></a>Omezení

- Tok kódu při zařízení je dostupná pouze na veřejné klientské aplikace.
- Autorita předávané při vytváření aplikace veřejným klientem musí být jedna z následujících akcí:
  - Klienty (ve formátu `https://login.microsoftonline.com/{tenant}/` kde `{tenant}` je buď identifikátor GUID představující ID tenanta nebo domény přidružené k tenantovi).
  - pro všechny pracovních a školních účtů (`https://login.microsoftonline.com/organizations/`).
- Osobní účty Microsoft se zatím nepodporují pomocí koncového bodu Azure AD v2.0 (nelze použít `/common` nebo `/consumers` tenantů).

## <a name="integrated-windows-authentication"></a>Ověření integrované Windows
Knihovna MSAL podporuje integrované ověřování Windows (IWA) pro stolní počítače nebo mobilní aplikace, které běží na Azure AD nebo připojené k doméně připojené k počítači s Windows. Pomocí IWA, tyto aplikace můžete získat token tiše (bez nutnosti zásahu uživatelského rozhraní od uživatele). 

![Diagram ověření integrované Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

V předchozím diagramu aplikace:

1. Získá token s použitím integrované ověřování Windows.
2. Token, který se používá k podání žádostí o prostředku.

### <a name="constraints"></a>Omezení

IWA podporuje pouze federovaných uživatelů to znamená uživatelé vytvořili ve službě Active Directory a Azure AD se opírá o. Vytvořit přímo ve službě Azure AD, bez Active Directory pro uživatele (spravovaných uživatelů) nelze pomocí tohoto toku ověřování. Toto omezení nemá vliv [tok uživatelského jména a hesla](#usernamepassword).

IWA je pro aplikace napsané pro platformy .NET Framework, .NET Core a univerzální platformu Windows.

IWA nebude obejít ověřování Multi-Factor Authentication. Pokud je nakonfigurováno ověřování pomocí služby Multi-Factor Authentication, IWA může selhat, pokud výzva ověřování službou Multi-Factor Authentication je povinný. Ověřování službou Multi-Factor Authentication vyžaduje zásah uživatele.

Nemáte kontrolu, když poskytovatel identity požádá o dvojúrovňové ověřování, která se má provést. Správce tenanta nepodporuje. Dvoufaktorové ověřování je obvykle vyžaduje při přihlášení z jiné země, pokud nejste připojeni přes síť VPN k podnikové síti a někdy i když jste připojení přes síť VPN. Azure AD používá AI průběžně další, pokud vyžádáním dvoufaktorového ověřování. Pokud selže IWA vám by měl vrátit zpět k [interaktivní uživatelské výzvy] (#interactive).

Autorita předávané při vytváření aplikace veřejným klientem musí být jedna z následujících akcí:
- Klienty (ve formátu `https://login.microsoftonline.com/{tenant}/` kde `tenant` je buď identifikátor guid představující ID tenanta nebo domény přidružené k tenantovi).
- pro všechny pracovních a školních účtů (`https://login.microsoftonline.com/organizations/`). Osobní účty Microsoft se nepodporují (nelze použít `/common` nebo `/consumers` tenantů).

Protože IWA je pasivní tok, musí být splněné jednu z následujících:
- k používání aplikace musí mít dříve schválená uživatelem vaší aplikace. 
- Správce tenanta musí mít dříve odsouhlasený. pro všechny uživatele v tenantovi k používání aplikace.

To znamená, že je splněna jedna z následujících akcí:
- Rozhodli jste se jako vývojář **udělení** na portálu Azure portal si to sami.
- Správce tenanta vybral **udělení nebo odvolání souhlasu správce pro {doména tenanta}** v **oprávnění k rozhraní API** kartu registrace aplikace (naleznete v tématu [přidat oprávnění pro přístup k webovým rozhraním API ](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Jste zadali způsob, jak uživatelům udělit souhlas s aplikace (naleznete v tématu [vyžádání souhlasu uživatele](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Zadali jste způsob, jak správce tenanta o souhlasu pro aplikaci (viz [souhlas správce](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

Pro .NET desktop, .NET Core a aplikací pro univerzální platformu Windows je povolen tok IWA. V rozhraní .NET Core je k dispozici pouze přetížení přijímající uživatelské jméno. Uživatelské jméno pro operační systém nemůžete se ptát platformy .NET Core.
  
Další informace o souhlas, naleznete v tématu [v2.0 oprávnění a vyjádření souhlasu](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Uživatelské jméno a heslo 
Podporuje MSAL [udělení přihlašovacího hesla vlastníka prostředku OAuth 2](v2-oauth-ropc.md), který umožňuje aplikaci přihlásit uživatele pomocí přímo zpracování své heslo. V desktopové aplikaci můžete použít tok uživatelského jména a hesla k získání tokenu bezobslužně. Vyžaduje se žádné uživatelské rozhraní, při používání aplikace.

![Diagram toku uživatelského jména a hesla](media/msal-authentication-flows/username-password.png)

V předchozím diagramu aplikace:

1. Získá token odesláním uživatelské jméno a heslo ke zprostředkovateli identity.
2. Volání webového rozhraní API pomocí tokenu.

> [!WARNING]
> Tento tok se nedoporučuje. Vyžaduje vysokou míru ohrožení zabezpečení a uživatele.  Tento tok byste měli používat jenom v případě, že toky, které bezpečnější, nelze použít. Další informace najdete v tématu [co je řešení, aby rostoucí problém hesel?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Upřednostňované tok pro získání tokenu tiše na počítačích připojených k doméně Windows je [integrované ověřování Windows](#integrated-windows-authentication). V opačném případě můžete použít také [toku kódu zařízení](#device-code).

I když to je užitečné v některých případech (scénáře DevOps), pokud chcete pomocí uživatelského jména a hesla v interaktivních scénářů, ve kterém můžete zadat vlastní uživatelské rozhraní, snažte se vyhnout ho. Pomocí uživatelského jména a hesla:
- Uživatelé, kteří potřebují provést ověřování službou Multi-Factor Authentication nebude můžou přihlašovat (protože není žádná interakce).
- Uživatelé nebudou moct jednotného přihlašování.

### <a name="constraints"></a>Omezení

Kromě [integrované ověřování Windows omezení](#integrated-windows-authentication), také platí následující omezení:

- Tok uživatelského jména a hesla není kompatibilní s podmíněného přístupu a ověřování službou Multi-Factor Authentication. Pokud vaše aplikace běží v tenantovi Azure AD, kde správce tenanta požaduje ověření službou Multi-Factor Authentication, v důsledku toho nelze pomocí tohoto toku. Mnoho organizací to udělat.
- Funguje pouze pro pracovní a školní účty (nikoli účty Microsoft).
- Tok je k dispozici na ploše .NET a .NET Core, ale není na univerzální platformu Windows.

### <a name="azure-ad-b2c-specifics"></a>Specifika Azure AD B2C

Další informace o použití MSAL.NET a Azure AD B2C, najdete v části [pomocí ROPC s Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
