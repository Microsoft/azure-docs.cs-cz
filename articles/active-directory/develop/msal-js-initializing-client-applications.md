---
title: Inicializovat klientské aplikace MSAL.js | Azure
titleSuffix: Microsoft identity platform
description: Informace o inicializaci klientských aplikací pomocí Knihovny Microsoft Authentication Library pro JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: fbd700c787a844fa7538ed198f76ed5c06af2c28
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010150"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicializovat klientské aplikace pomocí souboru MSAL.js
Tento článek popisuje inicializaci knihovny Microsoft Authentication Library pro JavaScript (MSAL.js) s instancí aplikace user-agent. Aplikace user-agent je forma veřejné klientské aplikace, ve které je klientský kód spuštěn v uživatelském agentovi, jako je například webový prohlížeč. Tito klienti neukládají tajné klíče, protože kontext prohlížeče je otevřeně přístupný. Další informace o typech klientských aplikací a možnostech konfigurace aplikace naleznete v [přehledu](msal-client-applications.md).

## <a name="prerequisites"></a>Požadavky
Před inicializací aplikace ji musíte nejdřív zaregistrovat na [portálu Azure,](scenario-spa-app-registration.md) aby se vaše aplikace mohla integrovat s platformou identit Microsoftu. Po registraci budete možná potřebovat následující informace (které najdete na webu Azure Portal):

- ID klienta (řetězec představující identifikátor GUID pro vaši aplikaci)
- Adresa URL poskytovatele identity (s názvem instance) a přihlašovací cílovou skupinu pro vaši aplikaci. Tyto dva parametry jsou souhrnně označovány jako autorita.
- ID klienta, pokud píšete obchodní aplikaci výhradně pro vaši organizaci (také s názvem aplikace s jedním tenantem).
- Pro webové aplikace budete muset také nastavit přesměrováníUri, kde se poskytovatel identity vrátí do vaší aplikace pomocí tokenů zabezpečení.

## <a name="initializing-applications"></a>Inicializace aplikací

MSAL.js můžete použít následujícím způsobem v jednoduché aplikaci JavaScript/Typescript. Inicializovat kontext ověřování MSAL `UserAgentApplication` vytvořením instance pomocí konfiguračního objektu. Minimální požadovaná konfigurace pro inicializaci souboru MSAL.js je clientID vaší aplikace, které byste měli získat z portálu pro registraci aplikací.

Pro metody ověřování s`loginRedirect` toky přesměrování ( a `acquireTokenRedirect`), v MSAL.js 1.2.x nebo starší, `handleRedirectCallback()` budete muset explicitně zaregistrovat zpětné volání pro úspěch nebo chybu prostřednictvím metody. To je potřeba, protože toky přesměrování nevracejí sliby jako metody s vyskakovací prostředí. To se stalo volitelné v MSAL.js verze 1.3.0.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
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

Soubor MSAL.js je navržen tak, aby `UserAgentApplication` měl jednu instanci a konfiguraci, která představuje jeden kontext ověřování. Více instancí se nedoporučuje, protože způsobují konfliktní položky mezipaměti a chování v prohlížeči.

## <a name="configuration-options"></a>Možnosti konfigurace

Soubor MSAL.js má níže uvedený konfigurační objekt, který poskytuje `UserAgentApplication`seskupení konfigurovatelných možností, které jsou k dispozici pro vytvoření instance aplikace .

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
    navigateFrameWait?: number;
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

Níže je uveden celkový počet konfigurovatelných možností, které jsou aktuálně podporovány v objektu config:

- **clientID**: Povinné. ClientID vaší aplikace, měli byste si to z portálu registrace aplikace.

- **orgán**: Nepovinné. Adresa URL označující adresář, ze kterého může služba MSAL požadovat tokeny. Výchozí hodnota `https://login.microsoftonline.com/common`je: .
    * Ve službě Azure AD je&lt;ve&gt;/&lt;&gt;formuláři &lt;&gt; https:// instancí cílové skupiny `https://login.microsoftonline.com`, &lt;&gt; kde instance je doména zprostředkovatele identity (například) a cílová skupina je identifikátor představující cílovou skupinu pro přihlášení. To může být následující hodnoty:
        * `https://login.microsoftonline.com/<tenant>`- tenant je doména přidružená k tenantovi, například `TenantID` contoso.onmicrosoft.com nebo identifikátor GUID představující vlastnost adresáře, který slouží pouze k přihlášení uživatelů určité organizace.
        * `https://login.microsoftonline.com/common`- Používá se k přihlášení uživatelů pomocí pracovních a školních účtů nebo osobního účtu Microsoft.
        * `https://login.microsoftonline.com/organizations/`- Používá se k přihlášení uživatelů pomocí pracovních a školních účtů.
        * `https://login.microsoftonline.com/consumers/`- Používá se k přihlášení uživatelů pouze s osobním účtem Microsoft (live).
    * V Azure AD B2C je `https://<instance>/tfp/<tenant>/<policyName>/`ve formuláři , kde instance je doména Azure AD B2C, tj. {název tenanta}.b2clogin.com, tenant je název klienta Azure AD B2C, tj.


- **validateAuthority**: Nepovinné.  Ověřte vystavittele tokenů. Výchozí je `true`. Pro aplikace B2C, protože hodnota autority je známa a může se lišit podle zásad, `false`ověření orgánu nebude fungovat a musí být nastaveno na .

- **redirectUri**: Volitelné.  Identifikátor URI přesměrování vaší aplikace, kde mohou být odpovědi na ověřování odesílány a přijímány vaší aplikací. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu. Výchozí hodnota `window.location.href`je na .

- **postLogoutRedirectUri**: Volitelné.  Přesměruje uživatele `postLogoutRedirectUri` po odhlášení. Výchozí hodnota `redirectUri`je .

- **navigateToLoginRequestUrl**: Volitelné. Možnost vypnout výchozí navigaci na úvodní stránku po přihlášení. Platí výchozí hodnota. Používá se pouze pro přesměrovávat toky.

- **cacheLocation**: Volitelné.  Nastaví úložiště `localStorage` prohlížeče `sessionStorage`na jedno nebo . Výchozí formát je `sessionStorage`.

- **storeAuthStateInCookie**: Nepovinné.  Tento příznak byl zaveden v MSAL.js v0.2.2 jako oprava [problémů s ověřovací smyčkou](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) v aplikaci Microsoft Internet Explorer a Microsoft Edge. Povolte `storeAuthStateInCookie` příznak true využít této opravy. Pokud je tato možnost povolena, soubor MSAL.js uloží stav požadavku ověření požadovaný pro ověření toků ověření v souborech cookie prohlížeče. Ve výchozím nastavení je `false`tento příznak nastaven na hodnotu .

- **logger**: Volitelné.  A Logger objekt s instanci zpětného volání, které mohou být poskytnuty vývojář využívat a publikovat protokoly vlastním způsobem. Podrobnosti o předávání objektu protokolování naleznete [v tématu protokolování pomocí souboru msal.js](msal-logging.md).

- **loadFrameTimeout**: Volitelné.  Počet milisekund nečinnosti před odpověď obnovení tokenu z Azure AD by měly být považovány za vypršení časového doby. Výchozí hodnota je 6 sekund.

- **tokenRenewalOffsetSeconds**: Volitelné. Počet milisekund, které nastaví okno posunu potřebné k obnovení tokenu před vypršením platnosti. Výchozí hodnota je 300 milisekund.

- **navigateFrameWait**: Volitelné. Počet milisekund, které nastaví čekací dobu, než skryté iframe přejde na cíl. Výchozí hodnota je 500 milisekund.

Ty platí pouze pro předání z knihovny obalů MSAL Angular:
- **nechráněné zdroje**: Volitelné.  Pole identifikátorů URI, které jsou nechráněnými prostředky. MSAL nepřipojí token k odchozím požadavkům, které mají tyto identifikátory URI. Výchozí hodnota `null`je na .

- **protectedResourceMap**: Volitelné.  Toto je mapování prostředků na obory používané MSAL pro automatické připojení přístupových tokenů ve volání webového rozhraní API. Pro prostředek je získán jeden přístupový token. Můžete tedy namapovat konkrétní cestu prostředkuhttps://graph.microsoft.com/v1.0/metakto: {" ", ["user.read"]}nebo adresuhttps://graph.microsoft.com/URL aplikace prostředku jako: {" ", ["user.read", "mail.send"]}. To je vyžadováno pro volání CORS. Výchozí hodnota `null`je na .
