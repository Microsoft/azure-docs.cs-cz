---
title: 'Kurz: přihlášení uživatelů a volání rozhraní API pro Microsoft Graph v pracovní desktopové aplikaci | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu vytvoříte aplikaci pracovní plochy, která se může přihlašovat uživatelům a použít tok kódu ověřování k získání přístupového tokenu z platformy Microsoft identity a volání rozhraní Microsoft Graph API.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: v-doeris
ms.openlocfilehash: 82e7d01442d30356a3d8ac68262a1ac49990666c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651183"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>Kurz: přihlášení uživatelů a volání rozhraní API pro Microsoft Graph v pracovní desktopové aplikaci

V tomto kurzu sestavíte aplikaci pracovní plochy, která se přihlašuje uživatelům a volá Microsoft Graph pomocí toku autorizačního kódu s PKCE. Desktopová aplikace, kterou sestavíte, používá [Microsoft Authentication Library (MSAL) pro Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Postupujte podle kroků v tomto kurzu:

> [!div class="checklist"]
> - Registrace aplikace v Azure Portal
> - Vytvoření projektu aplikace pracovní plochy
> - Přidání logiky ověřování do aplikace
> - Přidání metody pro volání webového rozhraní API
> - Přidat podrobnosti registrace aplikace
> - Otestování aplikace

## <a name="prerequisites"></a>Požadavky

- [Node.js](https://nodejs.org/en/download/)
- [Chyt](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) nebo jiný Editor kódu

## <a name="register-the-application"></a>Registrace aplikace

Nejdřív proveďte kroky v části [Registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md) k registraci vaší aplikace.

Pro registraci vaší aplikace použijte následující nastavení:

- Název: `ElectronDesktopApp` (navrhovaný)
- Podporované typy účtů: **účty v jakémkoli adresáři organizace (libovolný tenant adresáře Azure AD) a osobní účty Microsoft (např. Skype, Xbox)**
- Typ platformy: **mobilní a desktopové aplikace**
- Identifikátor URI pro přesměrování: `msal://redirect`

## <a name="create-the-project"></a>Vytvoření projektu

Vytvořte složku pro hostování aplikace, například *ElectronDesktopApp*.

1. Nejprve přejděte do adresáře projektu v terminálu a spusťte následující `npm` příkazy:

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. Pak vytvořte složku s názvem *App*. V této složce vytvořte soubor s názvem *index.html* , který bude sloužit jako uživatelské rozhraní. Sem přidejte následující kód:

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
                    </div>
                </div>
            </div>
            <br>
            <br>
            <div class="col-md-4">
                <div class="list-group" id="list-tab" role="tablist">
                </div>
            </div>
            <div class="col-md-5">
                <div class="tab-content" id="nav-tabContent">
                </div>
            </div>
        </div>
        <br>
        <br>

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. Dále vytvořte soubor s názvem *main.js* a přidejte následující kód:

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

Ve výše uvedeném fragmentu kódu inicializujeme objekt elektronického hlavního okna a vytvoříme nějaké obslužné rutiny událostí pro interakce s elektronovým oknem. Naimportujeme také parametry konfigurace, instanci třídy *authProvider* pro zpracování přihlášení, odhlášení a získání tokenu a volání rozhraní API pro Microsoft Graph.

4. Ve stejné složce (*aplikaci*) vytvořte další soubor s názvem *renderer.js* a přidejte následující kód:

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. Nakonec vytvořte soubor s názvem *constants.js* , ve kterém budou uloženy řetězcové konstanty pro popis **událostí** aplikace:

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

Teď máte k dispozici jednoduché grafické rozhraní (GUI) a interakce pro vaši elektronickou aplikaci. Po dokončení zbývající části kurzu by struktura souborů a složek vašeho projektu měla vypadat podobně jako v následujícím příkladu:

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>Přidání logiky ověřování do aplikace

Ve složce *aplikace* vytvořte soubor s názvem *AuthProvider.js*. Bude obsahovat třídu poskytovatele ověřování, která bude zpracovávat přihlašovací údaje, odhlášení, získání tokenu, výběr účtu a související úlohy ověřování pomocí uzlu MSAL. Sem přidejte následující kód:

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of 
 * a custom file protocol instead of a regular web (https://) redirect URI in order to 
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation. 
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md 
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md 
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;
        
        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);
        
        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         * 
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         * 
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         * 
         * For details on PKCE code generation logic, consult the 
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = { 
            ...this.authCodeUrlParams, 
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method 
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);
        
        const authResponse = await this.clientApplication.acquireTokenByCode({ 
            ...this.authCodeRequest, 
            scopes: tokenRequest.scopes, 
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier 
        });
        
        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {
        
        authWindow.loadURL(navigateUrl);

        return new Promise((resolve, reject) => {
            authWindow.webContents.on('will-redirect', (event, responseUrl) => {
                try {
                    const parsedUrl = new URL(responseUrl);
                    const authCode = parsedUrl.searchParams.get('code');
                    resolve(authCode);
                } catch (err) {
                    reject(err);
                }
            });
        });
    }

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response 
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

Ve fragmentu kódu výše jsme nejprve inicializoval MSAL uzel `PublicClientApplication` předáním konfiguračního objektu ( `msalConfig` ). Pak jsme vystaveni `login` `logout` a `getToken` metody, které má volat hlavní modul (*main.js*). V systému `login` a `getToken` získáme ID a přístupové tokeny, a to tak, že nejprve vyžádáte autorizační kód a pak ho vyměňujete pomocí tokenu pomocí `acquireTokenByCode` veřejného rozhraní API MSAL Node.

## <a name="add-a-method-to-call-a-web-api"></a>Přidání metody pro volání webového rozhraní API

Vytvořte jiný soubor s názvem *fetch.js*. Tento soubor bude obsahovat klienta Axios HTTP pro volání rozhraní REST API Microsoft Graph.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint 
 * @param accessToken 
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>Přidat podrobnosti registrace aplikace

Nakonec vytvořte soubor prostředí pro uložení podrobností o registraci aplikace, které se použijí při získávání tokenů. Pokud to chcete udělat, vytvořte soubor s názvem *. env* v kořenové složce Ukázky (*ElectronDesktopApp*) a přidejte následující kód:

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Do těchto podrobností zadejte hodnoty, které získáte z portálu pro registraci aplikací Azure:

- `Enter_the_Tenant_Id_here` měla by být jedna z následujících:
  - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta**. Například, `contoso.microsoft.com`.
  - Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu hodnotou `organizations` .
  - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu hodnotou `common` .
  - Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu hodnotou `consumers` .
- `Enter_the_Application_Id_Here`: **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali.
- `Enter_the_Cloud_Instance_Id_Here`: Cloudová instance Azure, ve které je vaše aplikace zaregistrovaná.
  - V případě hlavního (nebo *globálního*) cloudu Azure zadejte `https://login.microsoftonline.com/` .
  - Pro **národní** cloudy (například Čína) můžete najít odpovídající hodnoty v [národních cloudech](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` je instancí rozhraní API Microsoft Graph, se kterou by měla aplikace komunikovat.
  - Pro **globální** koncový bod rozhraní API Microsoft Graph nahraďte oba výskyty řetězce `https://graph.microsoft.com/` .
  - V případě koncových bodů v **národních** cloudových nasazeních najdete informace v tématu věnovaném [národním cloudovým nasazením](/graph/deployments) v dokumentaci Microsoft Graph.

## <a name="test-the-app"></a>Otestování aplikace

Dokončili jste vytváření aplikace a teď jste připraveni spustit aplikaci pracovní plocha a otestovat její funkčnost.

1. Spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři složky projektu:

```console
electron App/main.js
```

2. V hlavním okně aplikace by se měl zobrazit obsah souboru *index.html* a **přihlašovací** tlačítko.

## <a name="test-sign-in-and-sign-out"></a>Testovat přihlášení a odhlásit se

Po načtení souboru *index.html* vyberte **Přihlásit** se. Budete vyzváni k přihlášení pomocí platformy Microsoft Identity Platform:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="výzva k přihlášení":::

Pokud souhlasíte s požadovanými oprávněními, webové aplikace zobrazí vaše uživatelské jméno a označuje úspěšné přihlášení:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="úspěšné přihlášení":::

## <a name="test-web-api-call"></a>Testování volání webového rozhraní API

Po přihlášení vyberte **Zobrazit profil** pro zobrazení informací o profilu uživatele vrácených v odpovědi z volání rozhraní Microsoft Graph API:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="informace o profilu z Microsoft Graph":::

Výběrem **číst e-maily** zobrazíte zprávy v uživatelském účtu. Zobrazí se obrazovka pro vyjádření souhlasu:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="obrazovka souhlasu pro oprávnění číst. mail":::

Po souhlasu se zobrazí zprávy vrácené v odpovědi z volání rozhraní Microsoft Graph API:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="informace o poště z Microsoft Graph":::

## <a name="how-the-application-works"></a>Jak aplikace funguje

Když uživatel poprvé vybere tlačítko pro **přihlášení** , `getTokenInteractive` zavolá se metoda get pro *AuthProvider.js* . Tato metoda přesměruje uživatele na přihlášení pomocí *koncového bodu Microsoft Identity Platform* a ověří přihlašovací údaje uživatele a pak získá **autorizační kód**. Tento kód se pak vymění pro přístupový token pomocí `acquireTokenByCode` veřejného rozhraní API uzlu MSAL.

V tomto okamžiku se do koncového bodu tokenu Protected CORS pošle autorizační kód chráněný PKCE a vyměňují se pro tokeny. Vaše aplikace přijímá token ID, přístupový token a aktualizační token a informace obsažené v tokenech jsou uložené v mezipaměti.

Token ID obsahuje základní informace o uživateli, jako je jeho zobrazované jméno. Přístupový token má omezené trvání a vyprší za 24 hodin. Pokud plánujete používat tyto tokeny pro přístup k chráněnému prostředku, váš back-end server *musí* ověřit, zda byl token vydán platnému uživateli vaší aplikace.

Aplikace klasické pracovní plochy, kterou jste vytvořili v tomto kurzu, vytvoří volání rozhraní API Microsoft Graph pomocí přístupového tokenu jako nosiče v hlavičce požadavku ([RFC 6750](https://tools.ietf.org/html/rfc6750)).

Rozhraní Microsoft Graph API vyžaduje, aby *uživatel. přečetl* obor pro čtení profilu uživatele. Ve výchozím nastavení je tento obor automaticky přidán do každé aplikace, která je zaregistrována v Azure Portal. Jiná rozhraní API pro Microsoft Graph a také vlastní rozhraní API pro back-end Server můžou vyžadovat další obory. Například rozhraní Microsoft Graph API vyžaduje pro výpis e-mailu uživatele obor *mail. Read* .

Když přidáváte obory, můžou se uživatelům zobrazit výzva k poskytnutí dalšího souhlasu pro přidané obory.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Pokud byste chtěli podrobně hlouběji na Node.js a vývoji aplikací pracovní plochy na platformě Microsoft Identity Platform, přečtěte si naše série scénářů s více částmi:

> [!div class="nextstepaction"]
> [Scénář: desktopová aplikace, která volá webová rozhraní API](scenario-desktop-overview.md)
