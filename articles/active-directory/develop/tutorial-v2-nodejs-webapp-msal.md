---
title: 'Kurz: přihlášení uživatelů ve webové aplikaci Node.js & Express | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu přidáte podporu pro přihlašování uživatelů ve webové aplikaci.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 3f1f26acbba0f5830421e760d6a68a11f618fa85
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648986"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Kurz: přihlášení uživatelů v Node.js Web App & Express

V tomto kurzu vytvoříte webovou aplikaci, která se přihlásí uživatelům. Webová aplikace, kterou sestavíte, používá [Microsoft Authentication Library (MSAL) pro uzel](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Postupujte podle kroků v tomto kurzu:

> [!div class="checklist"]
> - Registrace aplikace v Azure Portal
> - Vytvoření projektu aplikace Express Web App
> - Instalace balíčků knihovny ověřování
> - Přidat podrobnosti registrace aplikace
> - Přidat kód pro přihlášení uživatele
> - Otestování aplikace

## <a name="prerequisites"></a>Požadavky

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) nebo jiný Editor kódu

## <a name="register-the-application"></a>Registrace aplikace

Nejdřív proveďte kroky v části [Registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md) k registraci vaší aplikace.

Pro registraci vaší aplikace použijte následující nastavení:

- Název: `ExpressWebApp` (navrhovaný)
- Podporované typy účtů: **účty v jakémkoli adresáři organizace (libovolný tenant adresáře Azure AD) a osobní účty Microsoft (např. Skype, Xbox)**
- Typ platformy: **Web**
- Identifikátor URI pro přesměrování: `http://localhost:3000/redirect`
- Tajný kód klienta: `*********` (Poznamenejte si tuto hodnotu v pozdějším kroku – zobrazí se jenom jednou)

## <a name="create-the-project"></a>Vytvoření projektu

Vytvořte složku pro hostování aplikace, například *ExpressWebApp*.

1. Nejprve přejděte do adresáře projektu v terminálu a spusťte následující `npm` příkazy:

```console
    npm init -y
    npm install --save express
```

2. Dále vytvořte soubor s názvem *index.js* a přidejte následující kód:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

Teď máte jednoduchý webový server, který běží na portu 3000. Struktura souboru a složky vašeho projektu by měla vypadat podobně jako v následujícím příkladu:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Instalace knihovny ověřování

Vyhledejte kořen adresáře projektu v terminálu a nainstalujte balíček MSAL Node prostřednictvím NPM.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Přidat podrobnosti registrace aplikace

Do souboru *index.js* , který jste vytvořili dříve, přidejte následující kód:

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
```

Do těchto podrobností zadejte hodnoty, které získáte z portálu pro registraci aplikací Azure:

- `Enter_the_Tenant_Id_here` měla by být jedna z následujících:
  - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta**. Například, `contoso.microsoft.com`.
  - Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu hodnotou `organizations` .
  - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu hodnotou `common` .
  - Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu hodnotou `consumers` .
- `Enter_the_Application_Id_Here`: **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali.
- `Enter_the_Cloud_Instance_Id_Here`: Cloudová instance Azure, ve které je vaše aplikace zaregistrovaná.
  - V případě hlavního (nebo *globálního*) cloudu Azure zadejte `https://login.microsoftonline.com` .
  - Pro **národní** cloudy (například Čína) můžete najít odpovídající hodnoty v [národních cloudech](authentication-national-cloud.md).
- `Enter_the_Client_secret`: Tuto hodnotu nahraďte tajným klíčem klienta, který jste vytvořili dříve. Pokud chcete vygenerovat nový klíč, použijte **certifikáty & tajných** kódů v nastavení registrace aplikace v Azure Portal.

> [!WARNING]
> Jakýkoli nešifrovaný tajný klíč ve zdrojovém kódu představuje zvýšené bezpečnostní riziko. V tomto článku se pro jednoduchost používá tajný klíč klienta prostého textu. Používejte [přihlašovací údaje certifikátu](active-directory-certificate-credentials.md) místo klientských tajných kódů v tajných klientských aplikacích, zejména u aplikací, které máte v úmyslu nasadit do produkčního prostředí.

## <a name="add-code-for-user-login"></a>Přidat kód pro přihlášení uživatele

Do souboru *index.js* , který jste vytvořili dříve, přidejte následující kód:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Testovat přihlášení

Dokončili jste vytváření aplikace a teď jste připraveni otestovat funkčnost aplikace.

1. Spusťte aplikaci Node.js konzolu spuštěním následujícího příkazu v kořenovém adresáři složky projektu:

```console
   node index.js
```

2. Otevřete okno prohlížeče a přejděte na adresu `http://localhost:3000`. Měla by se zobrazit přihlašovací obrazovka:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Zobrazení přihlašovací obrazovky pro Azure AD":::

3. Po zadání přihlašovacích údajů by se vám měla zobrazit obrazovka pro vyjádření souhlasu s žádostí o schválení oprávnění pro aplikaci.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Zobrazuje se obrazovka pro vyjádření souhlasu služby Azure AD":::

## <a name="how-the-application-works"></a>Jak aplikace funguje

V tomto kurzu jste inicializovali objekt MSAL node [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) předáním objektu konfigurace (*msalConfig*), který obsahuje parametry získané z registrace vaší aplikace Azure AD v Azure Portal. Webová aplikace, kterou jste vytvořili, používá [tok udělení autorizačního kódu OAuth 2,0](./v2-oauth2-auth-code-flow.md) k přihlašování uživatelů a získání ID a přístupových tokenů.

## <a name="next-steps"></a>Další kroky

Pokud se chcete podrobně hlouběji na Node.js & expresní vývoj webových aplikací na platformě Microsoft identity, přečtěte si naši řadu scénářů s více částmi:

> [!div class="nextstepaction"]
> [Scénář: webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md)