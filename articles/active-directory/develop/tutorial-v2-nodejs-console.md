---
title: 'Kurz: volání Microsoft Graph v konzolové aplikaci Node.js | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu sestavíte konzolovou aplikaci pro volání Microsoft Graph do konzolové aplikace Node.js.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: v-doeris
ms.openlocfilehash: 3d4211acbf6b65ef8f04d00b3936d70bb930ed9e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562074"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Kurz: volání rozhraní API Microsoft Graph v konzolové aplikaci Node.js

V tomto kurzu vytvoříte konzolovou aplikaci, která volá Microsoft Graph API pomocí vlastní identity. Aplikace konzoly, kterou sestavíte, používá [Microsoft Authentication Library (MSAL) pro Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Postupujte podle kroků v tomto kurzu:

> [!div class="checklist"]
> - Registrace aplikace v Azure Portal
> - Vytvoření projektu konzolové aplikace Node.js
> - Přidání logiky ověřování do aplikace
> - Přidat podrobnosti registrace aplikace
> - Přidání metody pro volání webového rozhraní API
> - Otestování aplikace

## <a name="prerequisites"></a>Požadavky

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) nebo jiný Editor kódu

## <a name="register-the-application"></a>Registrace aplikace

Nejdřív proveďte kroky v části [Registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md) k registraci vaší aplikace.

Pro registraci vaší aplikace použijte následující nastavení:

- Název: `NodeConsoleApp` (navrhovaný)
- Podporované typy účtů: **účty v tomto organizačním adresáři**
- Oprávnění rozhraní API: **rozhraní API Microsoftu**  >  **Microsoft Graph**  >  **oprávnění aplikace** > `User.Read.All`
- Tajný kód klienta: `*********` (Poznamenejte si tuto hodnotu v pozdějším kroku – zobrazí se jenom jednou)

## <a name="create-the-project"></a>Vytvoření projektu

Vytvořte složku pro hostování aplikace, například *NodeConsoleApp*.

1. Nejprve přejděte do adresáře projektu v terminálu a spusťte následující příkazy NPM:

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. Dále vytvořte složku s názvem *bin*. Pak uvnitř této složky vytvořte soubor s názvem *index.js* a přidejte následující kód:

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Tento soubor odkazuje na dva další moduly node: *auth.js* , který obsahuje implementaci uzlu MSAL pro získání přístupových tokenů, a *fetch.js* , která obsahuje metodu pro vytvoření požadavku HTTP na Microsoft Graph rozhraní API pomocí přístupového tokenu. Po dokončení zbývající části kurzu by struktura souborů a složek vašeho projektu měla vypadat podobně jako v následujícím příkladu:

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Přidat logiku ověřování

Ve složce *bin* vytvořte jiný soubor s názvem *auth.js* a přidejte následující kód pro získání přístupového tokenu, který se má zobrazit při volání rozhraní API Microsoft Graph.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation. 
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md 
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit: 
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow 
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest 
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

Ve fragmentu kódu výše jsme nejprve vytvořili objekt konfigurace (*msalConfig*) a předáte ho k inicializaci MSAL [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md). Pak vytvoříme metodu pro získání tokenů prostřednictvím **přihlašovacích údajů klienta** a nakonec vystavení tohoto modulu, aby k němu měl *main.js*. Parametry konfigurace v tomto modulu se vykreslí ze souboru prostředí, který vytvoříme v dalším kroku.

## <a name="add-app-registration-details"></a>Přidat podrobnosti registrace aplikace

Vytvořte soubor prostředí pro uložení podrobností o registraci aplikace, které se použijí při získávání tokenů. Pokud to chcete udělat, vytvořte soubor s názvem *. env* v kořenové složce Ukázky (*NodeConsoleApp*) a přidejte následující kód:

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
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
- `Enter_the_Graph_Endpoint_Here` je instancí rozhraní API Microsoft Graph, se kterou by měla aplikace komunikovat.
  - Pro **globální** koncový bod rozhraní API Microsoft Graph nahraďte oba výskyty řetězce `https://graph.microsoft.com` .
  - V případě koncových bodů v **národních** cloudových nasazeních najdete informace v tématu věnovaném [národním cloudovým nasazením](/graph/deployments) v dokumentaci Microsoft Graph.

## <a name="add-a-method-to-call-a-web-api"></a>Přidání metody pro volání webového rozhraní API

Ve složce *bin* vytvořte jiný soubor s názvem *fetch.js* a přidejte následující kód pro volání rozhraní REST API Microsoft Graph:

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint 
 * @param {string} accessToken 
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

Tady se `callApi` Metoda používá k vytvoření `GET` požadavku HTTP proti chráněnému prostředku, který vyžaduje přístupový token. Požadavek potom vrátí obsah volajícímu. Tato metoda přidá získaný token v *autorizační hlavičce protokolu HTTP*. Chráněným prostředkem je tady [koncový bod](https://docs.microsoft.com/graph/api/user-list) Microsoft Graph API Users, který zobrazuje uživatele v tenantovi, ve kterém je tato aplikace zaregistrovaná.

## <a name="test-the-app"></a>Otestování aplikace

Dokončili jste vytváření aplikace a teď jste připraveni otestovat funkčnost aplikace.

Spusťte aplikaci Node.js konzolu spuštěním následujícího příkazu v kořenovém adresáři složky projektu:

```console
node . --op getUsers
```

To by mělo mít za následek určitou odpověď JSON z Microsoft Graph API a v konzole byste měli vidět pole objektů uživatele:

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Rozhraní příkazového řádku zobrazující reakci grafu":::

## <a name="how-the-application-works"></a>Jak aplikace funguje

Tato aplikace používá [udělení přihlašovacích údajů klienta OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Tento typ udělení se běžně používá pro interakce mezi servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. Tok udělení přihlašovacích údajů umožňuje webové službě (důvěrnému klientovi) použít vlastní přihlašovací údaje místo zosobnění uživatele k ověření při volání jiné webové služby. Typy aplikací, které jsou podporovány s tímto modelem ověřování, jsou obvykle **procesy démon** nebo **účty služeb**.

Obor pro požadavek na tok přihlašovacích údajů klienta je název prostředku následovaný `/.default` . Tento zápis oznamuje službě Azure Active Directory (Azure AD) používání oprávnění na úrovni aplikace deklarované staticky během registrace aplikace. Tato oprávnění rozhraní API musí taky udělit **Správce klienta**.

## <a name="next-steps"></a>Další kroky

Pokud byste chtěli podrobně hlouběji Node.js vývoj konzolových aplikací na platformě Microsoft Identity Platform, přečtěte si naši řadu scénářů s více částmi:

> [!div class="nextstepaction"]
> [Scénář: démon aplikace](scenario-daemon-overview.md)
