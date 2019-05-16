---
title: Inicializace klientské aplikace (knihovna Microsoft Authentication Library pro JavaScript) | Azure
description: Další informace o inicializaci klientských aplikací pomocí Microsoft Authentication Library pro JavaScript (MSAL.js).
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
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd26f36356affbc8c272bd093757a8482773baf2
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544027"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicializace klientské aplikace s využitím MSAL.js
Tento článek popisuje inicializaci knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) s instancí aplikace uživatelského agenta. Aplikace uživatele – agenta je forma veřejné klientské aplikace 00Z klientský kód spouští v uživatelský agent jako je webový prohlížeč. Tito klienti nebudou ukládat tajné klíče, protože kontext prohlížeče je otevřeně dostupné. Další informace o typy klientských aplikací a možnosti konfigurace aplikace, [přehled](msal-client-applications.md).

## <a name="prerequisites"></a>Požadavky
Před inicializací aplikace, musíte nejprve [ho zaregistrovat pomocí webu Azure portal](scenario-spa-app-registration.md) tak, aby vaše aplikace je možné integrovat s platformou identity Microsoft. Po registraci může potřebovat následující informace (který se nachází na webu Azure Portal):

- ID klienta (řetězec představující identifikátor GUID pro vaše aplikace)
- Adresa URL zprostředkovatele identity (s názvem instance) a skupinou přihlášení pro aplikaci. Tyto dva parametry jsou souhrnně označovány jako autorita.
- ID tenanta, pokud píšete – obchodní aplikace pouze pro vaši organizaci (také pojmenované jednoho tenanta aplikaci).
- Pro webové aplikace musíte také nastavit identifikátor URI pro přesměrování kde zprostředkovatel identity vrátí do vaší aplikace s použitím tokenů zabezpečení.

## <a name="initializing-applications"></a>Inicializace aplikací

Můžete použít MSAL.js následujícím způsobem v jednoduché aplikace v jazyce JavaScript/Typescript. Po vytvoření instance inicializovat kontext ověřování MSAL `UserAgentApplication` s objektem konfigurace. Minimální požadované konfigurace pro inicializaci MSAL.js je clientID vaší aplikace, který by měl získat z portálu pro registraci aplikace.

Pro metody ověřování se přesměrování toků (`loginRedirect` a `acquireTokenRedirect`), je potřeba explicitně zaregistrujte zpětné volání pro úspěch nebo Chyba prostřednictvím `handleRedirectCallback()` metody. To je potřeba, protože přesměrování toky nevrátí příslibů, stejně jako metody s místním prostředím.

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

MSAL.js slouží k mít jednu instanci a konfiguraci `UserAgentApplication` představující kontext jedné ověřování. Více instancí se nedoporučuje, protože mohou způsobit konfliktní položky mezipaměti a chování v prohlížeči.

## <a name="configuration-options"></a>Možnosti konfigurace

MSAL.js má konfiguraci objekt, který vidíte níže, která poskytuje seskupení konfigurovatelných možností, které jsou k dispozici pro vytvoření instance `UserAgentApplication`.

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

Níže je úplnou sadu konfigurovatelných možností, které jsou aktuálně podporovány v objektu konfigurace:

- **clientID**: Povinná hodnota. ClientID vaší aplikace, měli byste získat to na portálu pro registraci aplikace.

- **Autorita**: Volitelné. Adresa URL označující adresář, který MSAL můžete požádat o tokeny od. Výchozí hodnota je: `https://login.microsoftonline.com/common`.
    * Ve službě Azure AD, je formulář https://&lt;instance&gt;/&lt;cílovou skupinu&gt;, kde &lt;instance&gt; je shodný s doménou zprostředkovatele identity (třeba `https://login.microsoftonline.com`) a &lt;cílovou skupinu&gt; je identifikátor reprezentující cílovou skupinu přihlášení. To může být následující hodnoty:
        * `https://login.microsoftonline.com/<tenant>`-tenant je domény přidružené k tenantovi, například contoso.onmicrosoft.com, nebo identifikátor GUID představující `TenantID` vlastnosti adresáře používat jenom k přihlášení uživatelů konkrétní organizace.
        * `https://login.microsoftonline.com/common`-Použít k přihlášení uživatelů s pracovní a školní účty nebo osobní účet Microsoft.
        * `https://login.microsoftonline.com/organizations/`-Použít k přihlášení uživatelů s pracovním a školním účtům.
        * `https://login.microsoftonline.com/consumers/` -Používá k přihlašování uživatelů pomocí jenom osobní účet Microsoft (live).
    * V Azure AD B2C je ve formátu `https://<instance>/tfp/<tenant>/<policyName>/`, kde instance je shodný s doménou Azure AD B2C, tenant je název tenanta Azure AD B2C, policyName je název B2C zásady začaly platit.


- **validateAuthority**: Volitelné.  Ověření vystavitele tokeny. Výchozí hodnota je `true`. Pro aplikace B2C, protože hodnota autority se označuje a mohou být různé podle zásad, ověřovací autorita nebude fungovat a musí být nastavena na `false`.

- **redirectUri**: Volitelné.  Identifikátor URI přesměrování vaší aplikace, kde můžete odesílat a přijímat aplikací pro žádosti o ověření. Se musí přesně odpovídat jednu registraci na portálu pro identifikátory URI přesměrování s tím rozdílem, že ho musí mít kódování URL. Výchozí hodnota je `window.location.href`.

- **postLogoutRedirectUri**: Volitelné.  Přesměruje uživatele na `postLogoutRedirectUri` po odhlášení. Výchozí formát je `redirectUri`.

- **navigateToLoginRequestUrl**: Volitelné. Možnost vypnout navigační výchozí domovské stránky po přihlášení. Výchozí hodnota je true. Používá se pouze pro přesměrování toky.

- **cacheLocation**: Volitelné.  Nastaví úložiště prohlížeče buď `localStorage` nebo `sessionStorage`. Výchozí formát je `sessionStorage`.

- **storeAuthStateInCookie**: Volitelné.  Tento příznak byla zavedena v MSAL.js v0.2.2 jako opravu [problémy s ověřováním smyčky](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) na Microsoft Internet Explorerem a Microsoft Edgem. Povolit příznak `storeAuthStateInCookie` hodnotu PRAVDA, využijte výhod to opravit. Když je tato možnost povolena, MSAL.js uloží stav žádosti o ověření požadované pro ověření toků ověřování v prohlížeči soubory cookie. Ve výchozím nastavení je tento příznak nastaven na `false`.

- **logger**: Volitelné.  Objekt protokolovací nástroj s instance zpětného volání, můžete poskytuje vývojářům zpracovávejte a publikujte protokoly vlastní způsobem. Podrobnosti o předání objektu protokolovací nástroj, najdete v části [protokolování pomocí msal.js](msal-logging.md).

- **loadFrameTimeout**: Volitelné.  Počet milisekund neaktivity, než obnovení tokenu odpověď ze služby Azure AD by se měly zvažovat vypršel časový limit. Výchozí hodnota je 6 sekund.

- **tokenRenewalOffsetSeconds**: Volitelné. Počet milisekund která nastavuje hodnoty v okně posun potřebné k obnovení tokenu před vypršením platnosti. Výchozí hodnota je 300 milisekund.

Tyto platí pouze pro být předáván z MSAL Angular obálky knihovny:
- **unprotectedResources**: Volitelné.  Pole identifikátory URI, které jsou nechráněných prostředcích. MSAL nebude token připojení pro odchozí požadavky, které mají tyto identifikátoru URI. Výchozí hodnota je `null`.

- **protectedResourceMap**: Volitelné.  To je mapování prostředků k oborům používaný MSAL automatické připojení přístupových tokenů ve volání webového rozhraní API. Pro prostředek se získávají jednoho přístupového tokenu. Aby bylo možné mapovat cestu konkrétní prostředek následujícím způsobem: {"https://graph.microsoft.com/v1.0/me", ["user.read"]}, nebo adresu URL aplikace jako prostředku: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Toto je nezbytné pro volání CORS. Výchozí hodnota je `null`.
