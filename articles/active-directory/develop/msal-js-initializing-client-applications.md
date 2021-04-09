---
title: Inicializovat klientské aplikace MSAL.js | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s inicializací klientských aplikací pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: a6d7b760ffd1931fa5dcdb3a67dd02f2798957a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100365833"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicializace klientských aplikací pomocí MSAL.js

Tento článek popisuje inicializaci knihovny Microsoft Authentication Library pro JavaScript (MSAL.js) s instancí aplikace uživatelského agenta.

Aplikace User-Agent je forma veřejné klientské aplikace, ve které se klientský kód spouští v uživatelském agentovi, jako je webový prohlížeč. Tito klienti neukládají tajné klíče, protože kontext prohlížeče je otevřený.

Další informace o typech klientských aplikací a možnostech konfigurace aplikací najdete v tématu [veřejné a důvěrné klientské aplikace v MSAL](msal-client-applications.md).

## <a name="prerequisites"></a>Požadavky

Před inicializací aplikace je nejprve nutné [ji zaregistrovat s Azure Portal](scenario-spa-app-registration.md)a vytvořit tak vztah důvěryhodnosti mezi vaší aplikací a platformou Microsoft identity.

Po registraci aplikace budete potřebovat některé nebo všechny následující hodnoty, které najdete v Azure Portal.

| Hodnota | Povinné | Popis |
|:----- | :------: | :---------- |
| ID aplikace (klienta) | Vyžadováno | Identifikátor GUID, který jedinečně identifikuje vaši aplikaci v rámci platformy Microsoft identity |
| Autorita | Volitelné | Adresa URL zprostředkovatele identity ( *instance*) a *cílová skupina pro přihlášení* k vaší aplikaci. Po zřetězení instance a přihlašování se přihlásí *autorita*. |
| ID adresáře (tenanta) | Volitelné | Tuto hodnotu zadejte, pokud vytváříte obchodní aplikaci výhradně pro vaši organizaci, která se často označuje jako *aplikace pro jednoho tenanta*. |
| Identifikátor URI pro přesměrování | Volitelné | Pokud vytváříte webovou aplikaci, `redirectUri` Určuje, kde poskytovatel identity (Microsoft Identity Platform) by měl vrátit tokeny zabezpečení, které vydala. |

## <a name="initialize-msaljs-2x-apps"></a>Inicializace aplikací MSAL.js 2. x

Inicializujte kontext ověřování MSAL vytvořením instance [PublicClientApplication][msal-js-publicclientapplication] s objektem [Konfigurace][msal-js-configuration] . Minimální požadovaná vlastnost konfigurace je `clientID` aplikace, která se zobrazuje jako **ID aplikace (klienta)** na stránce **přehled** registrace aplikace v Azure Portal.

Tady je příklad konfiguračního objektu a instance pro `PublicClientApplication` :

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

Vyvolá [handleRedirectPromise][msal-js-handleredirectpromise] , když vaše aplikace používá toky přesměrování. Při použití toků přesměrování by se `handleRedirectPromise` měly spouštět při každém načtení stránky.

Promise nabízí tři možné výsledky:

- `.then` je vyvolána a `tokenResponse` je pravdy: aplikace vrací z operace přesměrování, která byla úspěšná.
- `.then` je vyvolána a `tokenResponse` je NEPRAVDA ( `null` ): aplikace se nevrací z operace přesměrování.
- `.catch` je vyvoláno: aplikace vrací z operace přesměrování a došlo k chybě.

## <a name="initialize-msaljs-1x-apps"></a>Inicializovat aplikace MSAL.js 1. x

Inicializujte kontext ověřování MSAL 1. x vytvořením instance [UserAgentApplication][msal-js-useragentapplication] s objektem konfigurace. Minimální požadovaná vlastnost konfigurace je `clientID` aplikace, která se zobrazuje jako **ID aplikace (klienta)** na stránce **přehled** registrace aplikace v Azure Portal.

Pro metody ověřování s přesměrací toků ([loginRedirect][msal-js-loginredirect] a [acquireTokenRedirect][msal-js-acquiretokenredirect]) v MSAL.js 1.2. x nebo starší je nutné explicitně zaregistrovat zpětné volání pro úspěch nebo chybu prostřednictvím `handleRedirectCallback()` metody. Explicitní registrace zpětného volání je vyžadována v MSAL.js 1.2. x a starší, protože toky přesměrování nevrací příslibů jako metody s místním prostředím. Registrace zpětného volání je *volitelná* ve verzi MSAL.js 1.3. x a novější.

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>Jedna instance a konfigurace

MSAL.js 1. x a 2. x jsou navrženy tak, aby měly jednu instanci a konfiguraci `UserAgentApplication` nebo, v `PublicClientApplication` uvedeném pořadí, aby představovaly jediný kontext ověřování.

Více instancí `UserAgentApplication` nebo `PublicClientApplication` se nedoporučuje, protože způsobují konfliktní položky a chování mezipaměti v prohlížeči.

## <a name="next-steps"></a>Další kroky

Tato MSAL.js 2. x ukázka kódu na GitHubu ukazuje vytváření instancí [PublicClientApplication][msal-js-publicclientapplication] s objektem [Konfigurace][msal-js-configuration] :

[Azure-Samples/MS-identity-JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal.html#configuration
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
