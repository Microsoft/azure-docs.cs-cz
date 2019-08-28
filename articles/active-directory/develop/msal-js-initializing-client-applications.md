---
title: Inicializovat klientské aplikace (Microsoft Authentication Library pro JavaScript) | Azure
description: Seznamte se s inicializací klientských aplikací pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL. js).
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
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d36def4faa98f4b8e42c93cf3e222c2ec7ca89a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073844"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicializace klientských aplikací pomocí MSAL. js
Tento článek popisuje inicializaci knihovny Microsoft Authentication Library pro JavaScript (MSAL. js) s instancí aplikace uživatelského agenta. Aplikace User-Agent je forma veřejné klientské aplikace, ve které se klientský kód spouští v uživatelském agentovi, jako je webový prohlížeč. Tito klienti neukládají tajné kódy, protože kontext prohlížeče je otevřený. Další informace o typech klientských aplikací a možnostech konfigurace aplikací najdete v [přehledu](msal-client-applications.md).

## <a name="prerequisites"></a>Požadavky
Před inicializací aplikace je nejdřív potřeba [ji zaregistrovat s Azure Portal](scenario-spa-app-registration.md) , aby bylo možné aplikaci integrovat s platformou Microsoft identity. Po registraci možná budete potřebovat následující informace (které najdete v Azure Portal):

- ID klienta (řetězec představující GUID vaší aplikace)
- Adresa URL zprostředkovatele identity (pojmenovaná instance) a cílová skupina pro přihlášení k vaší aplikaci. Tyto dva parametry jsou souhrnně známé jako autorita.
- ID tenanta, pokud píšete obchodní aplikaci výhradně pro vaši organizaci (nazývá se jenom jediná aplikace tenanta).
- U webových aplikací budete muset také nastavit redirectUri, kde se poskytovatel identity vrátí do vaší aplikace s tokeny zabezpečení.

## <a name="initializing-applications"></a>Inicializace aplikací

MSAL. js můžete použít následujícím způsobem v jednoduché aplikaci JavaScript/TypeScript. Inicializujte kontext ověřování MSAL vytvořením `UserAgentApplication` instance s objektem konfigurace. Minimální požadovaná konfigurace pro inicializaci MSAL. js je clientID vaší aplikace, kterou byste měli získat z portálu pro registraci aplikací.

Pro metody ověřování pomocí toků přesměrování (`loginRedirect` a `acquireTokenRedirect`) budete muset explicitně zaregistrovat zpětné volání pro metodu Success nebo Error prostřednictvím `handleRedirectCallback()` metody. To je potřeba, protože toky přesměrování nevrací příslibů jako metody s místním prostředím.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL. js je navržen tak, aby měl jedinou instanci a konfiguraci `UserAgentApplication` pro reprezentaci jediného kontextu ověřování. Více instancí se nedoporučuje, protože způsobují konfliktní položky a chování mezipaměti v prohlížeči.

## <a name="configuration-options"></a>Možnosti konfigurace

MSAL. js má níže uvedený objekt konfigurace, který poskytuje seskupení konfigurovatelných možností, které jsou k dispozici pro `UserAgentApplication`vytvoření instance.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Níže je uvedená celková sada konfigurovatelných možností, které jsou aktuálně podporovány v objektu config:

- **clientID**: Povinný parametr. ClientID vaší aplikace byste měli získat z portálu pro registraci aplikací.

- **autorita**: Volitelný parametr. Adresa URL označující adresář, ze kterého může MSAL žádat o tokeny. Výchozí hodnota je: `https://login.microsoftonline.com/common`.
    * Ve službě Azure&lt;AD se jedná o cílovou skupinu&gt;/&gt;https://instance&lt;, ve které &lt;je&gt; instance doménou poskytovatele identity (například `https://login.microsoftonline.com`) a Cílováskupina&gt; je identifikátor představující cílovou skupinu přihlášení. &lt; Může se jednat o následující hodnoty:
        * `https://login.microsoftonline.com/<tenant>`-tenant je doména přidružená ke klientovi, jako je například contoso.onmicrosoft.com, nebo identifikátor GUID představující `TenantID` vlastnost adresáře, který se používá pouze pro přihlášení uživatelů určité organizace.
        * `https://login.microsoftonline.com/common`– Slouží k přihlašování uživatelů pomocí pracovních a školních účtů nebo osobního účtu Microsoft.
        * `https://login.microsoftonline.com/organizations/`– Slouží k přihlašování uživatelů pomocí pracovních a školních účtů.
        * `https://login.microsoftonline.com/consumers/`– Slouží k přihlášení uživatelů pouze pomocí osobního účet Microsoft (živé).
    * V Azure AD B2C se jedná o formulář `https://<instance>/tfp/<tenant>/<policyName>/`, kde instance je Azure AD B2C doména, tenant je název Azure AD B2Cho tenanta, Policy je název zásady B2C, která se má použít.


- **validateAuthority**: Volitelný parametr.  Ověření vystavitele tokenů. Výchozí hodnota je `true`. Pro B2C aplikace, protože je hodnota autority známá a může se lišit podle zásad, ověření platnosti autority nebude fungovat a musí být nastavená na `false`.

- **redirectUri**: Volitelný parametr.  Identifikátor URI pro přesměrování vaší aplikace, ve kterém může vaše aplikace odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu. Výchozí hodnota je `window.location.href`.

- **postLogoutRedirectUri**: Volitelný parametr.  Přesměruje uživatele na `postLogoutRedirectUri` po odhlášení. Výchozí formát je `redirectUri`.

- **navigateToLoginRequestUrl**: Volitelný parametr. Možnost vypnout výchozí navigaci na úvodní stránku po přihlášení. Výchozí hodnota je true. Používá se pouze pro toky přesměrování.

- **cacheLocation**: Volitelný parametr.  Nastaví úložiště prohlížeče na buď `localStorage` nebo `sessionStorage`. Výchozí hodnota je `sessionStorage`.

- **storeAuthStateInCookie**: Volitelný parametr.  Tento příznak byl představen v MSAL. js v 0.2.2 jako oprava pro problémy ve [smyčce ověřování](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) v aplikaci Microsoft Internet Explorer a Microsoft Edge. Pokud chcete tuto `storeAuthStateInCookie` opravu využít, povolte příznak na hodnotu true. Pokud je tato možnost povolena, bude MSAL. js ukládat stav žádosti o ověření, který je vyžadován pro ověření toků ověřování v souborech cookie prohlížeče. Ve výchozím nastavení je tento příznak nastaven `false`na hodnotu.

- **logger**: Volitelný parametr.  Objekt protokolovacího nástroje s instancí zpětného volání, kterou může vývojář poskytnout pro využívání a publikování protokolů vlastním způsobem. Podrobnosti o předávání objektu protokolovacího nástroje naleznete v tématu [protokolování pomocí msal. js](msal-logging.md).

- **loadFrameTimeout**: Volitelný parametr.  Časový limit počtu milisekund nečinnosti, než má být odpověď na obnovení tokenu z Azure AD považována za časový limit. Výchozí hodnota je 6 sekund.

- **tokenRenewalOffsetSeconds**: Volitelný parametr. Počet milisekund, které nastaví posunutí okna potřebné k obnovení tokenu před vypršením platnosti. Výchozí hodnota je 300 milisekund.

Ty se dají použít jenom v případě, že se má předávat z MSALové knihovny obálek:
- **unprotectedResources**: Volitelný parametr.  Pole identifikátorů URI, které jsou nechráněné prostředky. MSAL nebude připojovat token k odchozím žádostem, které mají tento identifikátor URI. Výchozí hodnota je `null`.

- **protectedResourceMap**: Volitelný parametr.  Jedná se o mapování prostředků na obory používané MSALem pro automatické připojení tokenů přístupu v voláních webového rozhraní API. Pro prostředek se získá jeden přístupový token. Můžete tedy mapovat konkrétní cestu prostředku následujícím způsobem: {"https://graph.microsoft.com/v1.0/me", ["User. Read"]} nebo adresa URL aplikace prostředku jako: {"https://graph.microsoft.com/", ["User. Read", "mail. Send"]}. To se vyžaduje pro volání CORS. Výchozí hodnota je `null`.
