---
title: Kurz pro jednostránkové aplikace v JavaScriptu – tok kódu ověřování | Azure
titleSuffix: Microsoft identity platform
description: Jak aplikace JavaScript SPA můžou pomocí toku kódu ověřování volat rozhraní API, které vyžaduje přístupové tokeny pomocí koncového bodu Azure Active Directory v 2.0
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 7a136c03db6e27763a22d92d2c335f23c616856e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91256802"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Kurz: přihlášení uživatelů a volání rozhraní API Microsoft Graph z jednostránkové aplikace v JavaScriptu (SPA) pomocí toku kódu ověřování

V tomto kurzu se dozvíte, jak vytvořit jednostránkovou aplikaci v JavaScriptu (SPA), která používá knihovnu Microsoft Authentication Library (MSAL) pro JavaScript v 2.0:

> [!div class="checklist"]
> * Provedení toku autorizačního kódu OAuth 2,0 s PKCE
> * Přihlaste se k osobním účtům Microsoft i pracovním a školním účtům.
> * Získání přístupového tokenu
> * Zavolejte Microsoft Graph nebo vlastní rozhraní API, které vyžaduje přístupové tokeny získané z koncového bodu Microsoft Identity Platform.

MSAL.js 2,0 vylepšuje MSAL.js 1,0 tím, že podporuje tok autorizačního kódu v prohlížeči místo implicitního toku udělení. MSAL.js **2,0 nepodporuje implicitní** tok.

[!INCLUDE [MSAL.js 2.0 and Azure AD B2C temporary incompatibility notice](../../../includes/msal-b2c-cors-compatibility-notice.md)]

## <a name="how-the-tutorial-app-works"></a>Jak funguje aplikace tutorial

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagram znázorňující tok autorizačního kódu v aplikaci s jednou stránkou":::

Aplikace, kterou vytvoříte v tomto kurzu, umožňuje pomocí JavaScriptu ZABEZPEČENÉho dotazování na rozhraní API Microsoft Graph získat tokeny zabezpečení z koncového bodu Microsoft Identity Platform. V tomto scénáři se po přihlášení uživatele vyžádá přístupový token a přidá se do požadavků HTTP v autorizační hlavičce. Získání a obnovení tokenu jsou zpracovávány knihovnou Microsoft Authentication Library pro JavaScript (MSAL.js).

V tomto kurzu se používá následující knihovna:

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) Knihovna Microsoft Authentication Library pro JavaScript v 2.0 – balíček

## <a name="get-the-completed-code-sample"></a>Získat ukázku dokončeného kódu

Chcete místo toho stáhnout ukázkový projekt tohoto kurzu? Chcete-li spustit projekt pomocí místního webového serveru, například Node.js, naklonujte úložiště [MS-identity-JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) :

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Pokud chcete před spuštěním nakonfigurovat ukázku kódu, přejděte k [kroku konfigurace](#register-your-application).

Pokud chcete pokračovat v kurzu a sestavit aplikaci sami, přejděte k další části [požadavky](#prerequisites).

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/download/) pro spuštění místního serveru
* [Visual Studio Code](https://code.visualstudio.com/download) nebo jiný Editor kódu

## <a name="create-your-project"></a>Vytvoření projektu

Po nainstalování [Node.js](https://nodejs.org/en/download/) vytvořte složku, která bude hostovat vaši aplikaci, například *msal-Spa – kurz*.

V dalším kroku implementujte webový server s malým [expresním](https://expressjs.com/) webem, který bude sloužit vašemu *index.htmmu* souboru.

1. Nejprve přejděte do adresáře projektu v terminálu a spusťte následující `npm` příkazy:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Dále vytvořte soubor s názvem *server.js* a přidejte následující kód:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Teď máte malý dynamický Server, který bude sloužit pro SPA. Po dokončení zbývající části kurzu by struktura souborů a složek vašeho projektu měla vypadat podobně jako v následujícím příkladu:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>Vytvoření uživatelského rozhraní SPA

1. Vytvořte složku *aplikace* v adresáři projektu a v ní vytvořte soubor *index.html* pro váš JavaScript Spa. Tento soubor implementuje uživatelské rozhraní vytvořené pomocí **rutiny Bootstrap 4** a importuje soubory skriptu pro konfiguraci, ověřování a volání rozhraní API.

    Do souboru *index.html* přidejte následující kód:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
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

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. Dále ve složce *aplikace* vytvořte soubor s názvem *ui.js* a přidejte následující kód. Tento soubor bude mít přístup k elementům DOM a jejich aktualizaci.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>Registrace aplikace

Postupujte podle kroků v [jednostránkové aplikaci: registrace aplikace](scenario-spa-app-registration.md) pro vytvoření registrace aplikace pro Spa.

Do pole [identifikátor URI pro přesměrování: MSAL.js 2,0 s krok tok kódu pro ověřování](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) zadejte `http://localhost:3000` výchozí umístění, ve kterém se spouští aplikace tohoto kurzu.

Pokud chcete použít jiný port, zadejte `http://localhost:<port>` , kde `<port>` je vaše preferované číslo portu TCP. Pokud zadáte jiné číslo portu než `3000` , aktualizujte *server.js* také s preferovaným číslem portu.

### <a name="configure-your-javascript-spa"></a>Konfigurace vašeho JavaScriptu SPA

Ve složce *aplikace* vytvořte soubor s názvem *authConfig.js* , který obsahuje parametry konfigurace pro ověřování, a přidejte následující kód:

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

Upravte hodnoty v `msalConfig` části, jak je popsáno zde:

- `Enter_the_Application_Id_Here`: **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali.
- `Enter_the_Cloud_Instance_Id_Here`: Cloudová instance Azure, ve které je vaše aplikace zaregistrovaná.
  - V případě hlavního (nebo *globálního*) cloudu Azure zadejte `https://login.microsoftonline.com` .
  - Pro **národní** cloudy (například Čína) můžete najít odpovídající hodnoty v [národních cloudech](authentication-national-cloud.md).
- `Enter_the_Tenant_info_here` měla by být jedna z následujících:
  - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta**. Například, `contoso.microsoft.com`.
  - Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu hodnotou `organizations` .
  - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu hodnotou `common` .
  - Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu hodnotou `consumers` .
- `Enter_the_Redirect_Uri_Here` je `http://localhost:3000`.

`authority`Pokud používáte globální cloud Azure, měla by být hodnota v *authConfig.js* podobná následující:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Pořád ve složce *aplikace* vytvořte soubor s názvem *graphConfig.js*. Přidejte následující kód, který poskytne vaší aplikaci parametry konfigurace pro volání rozhraní Microsoft Graph API:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

Upravte hodnoty v `graphConfig` části, jak je popsáno zde:

- `Enter_the_Graph_Endpoint_Here` je instancí rozhraní API Microsoft Graph, se kterou by měla aplikace komunikovat.
  - Pro **globální** koncový bod rozhraní API Microsoft Graph nahraďte oba výskyty řetězce `https://graph.microsoft.com` .
  - V případě koncových bodů v **národních** cloudových nasazeních najdete informace v tématu věnovaném [národním cloudovým nasazením](https://docs.microsoft.com/graph/deployments) v dokumentaci Microsoft Graph.

`graphMeEndpoint`Hodnoty a `graphMailEndpoint` v *graphConfig.js* by měly být podobné následujícímu, pokud používáte globální koncový bod:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-microsoft-authentication-library-msal-to-sign-in-user"></a>Přihlášení uživatele pomocí knihovny Microsoft Authentication Library (MSAL)

### <a name="pop-up"></a>Automaticky otevíraná okna

Ve složce *aplikace* vytvořte soubor s názvem *authPopup.js* a přidejte následující kód pro získání a ověření tokenu pro automaticky otevírané okno pro přihlášení:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>Přesměrování

Ve složce *aplikace* vytvořte soubor s názvem *authRedirect.js* a přidejte následující kód pro získání přihlašovacích údajů a token pro přesměrování přihlášení:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>Jak kód funguje

Když uživatel poprvé vybere tlačítko pro **přihlášení** , `signIn` metoda se zavolá na `loginPopup` přihlášení uživatele. `loginPopup`Metoda otevře automaticky otevírané okno s *koncovým bodem Microsoft Identity Platform* , kde se zobrazí výzva a ověří přihlašovací údaje uživatele. Po úspěšném přihlášení zahájí *msal.js* [tok autorizačního kódu](v2-oauth2-auth-code-flow.md).

V tomto okamžiku se do koncového bodu tokenu Protected CORS pošle autorizační kód chráněný PKCE a vyměňují se pro tokeny. Vaše *msal.js*aplikace obdrží token ID, přístupový token a aktualizační token a informace obsažené v tokenech jsou uložené v mezipaměti.

Token ID obsahuje základní informace o uživateli, jako je jeho zobrazované jméno. Pokud máte v úmyslu použít jakákoli data poskytnutá tokenem ID, váš back-end server *musí* ověřit, zda byl token vydán platnému uživateli vaší aplikace. Obnovovací token má omezené trvání a vyprší za 24 hodin. Obnovovací token lze použít k tichému získání nových přístupových tokenů.

SPA, které jste vytvořili v tomto kurzu, volá nebo `acquireTokenSilent` `acquireTokenPopup` získá *přístupový token* , který se používá k dotazování rozhraní API Microsoft Graph pro informace o profilu uživatele. Pokud potřebujete ukázku, která ověří token ID, přečtěte si ukázkovou aplikaci [Active-Directory-JavaScript-singlepageapp-dotnet-WebApi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) na GitHubu. Ukázka používá webové rozhraní API ASP.NET pro ověření tokenu.

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Po prvním přihlášení by vaše aplikace neměla požádat uživatele, aby se znovu ověřili pokaždé, když potřebují přístup k chráněnému prostředku (to znamená vyžádání tokenu). Chcete-li zabránit takové žádosti o opakované ověření, zavolejte `acquireTokenSilent` . Existují však situace, kdy může být nutné vynutit, aby uživatelé mohli pracovat s koncovým bodem Microsoft Identity Platform. Příklad:

- Uživatelé musí znovu zadat své přihlašovací údaje, protože vypršela platnost hesla.
- Vaše aplikace požaduje přístup k prostředku a potřebujete souhlas uživatele.
- Je vyžadováno dvojúrovňové ověřování.

Volání `acquireTokenPopup` otevře automaticky otevírané okno (nebo `acquireTokenRedirect` přesměruje uživatele na koncový bod Microsoft Identity Platform). V tomto okně musí uživatelé pracovat pomocí potvrzení svých přihlašovacích údajů, udělení souhlasu k požadovanému prostředku nebo dokončením dvojúrovňového ověřování.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`acquireTokenSilent`Metoda zpracovává získání a obnovení tokenu bez zásahu uživatele. Po `loginPopup` `loginRedirect` prvním spuštění (nebo) `acquireTokenSilent` je metoda, která se běžně používá k získání tokenů používaných pro přístup k chráněným prostředkům pro následná volání. (Volání požadavků na požadavky nebo obnovení tokenů se provádí tiše.) `acquireTokenSilent` v některých případech může selhat. Může například dojít k vypršení platnosti hesla uživatele. Vaše aplikace může tuto výjimku zpracovat dvěma způsoby:

1. Okamžitě zavolejte, `acquireTokenPopup` aby se aktivovala výzva k přihlášení uživatele. Tento model se běžně používá v online aplikacích, kde není k dispozici žádný neautorizovaný obsah v aplikaci pro uživatele. Ukázka vygenerovaná tímto procesem instalace používá tento model.
1. Vizuálně nahlaste uživateli, že je vyžadováno interaktivní přihlášení, aby mohl uživatel vybrat správný čas pro přihlášení, nebo může aplikace opakovat `acquireTokenSilent` později. Tato technika se běžně používá v případě, že uživatel může použít jiné funkce aplikace, aniž by došlo k přerušení. V aplikaci může být například dostupný neautorizovaný obsah. V takovém případě se uživatel může rozhodnout, kdy se chce přihlásit k přístupu k chráněnému prostředku, nebo aktualizovat zastaralé informace.

> [!NOTE]
> V tomto kurzu se `loginPopup` `acquireTokenPopup` ve výchozím nastavení používají metody a. Pokud používáte Internet Explorer, doporučujeme použít `loginRedirect` `acquireTokenRedirect` metody a z důvodu [známého problému](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) s Internet Explorerem a automaticky otevíraných oken. Příklad toho, jak dosáhnout stejného výsledku pomocí metod přesměrování, najdete v tématu [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) na GitHubu.

## <a name="call-the-microsoft-graph-api"></a>Volání rozhraní API pro Microsoft Graph

Ve složce *aplikace* vytvořte soubor s názvem *graph.js* a přidejte následující kód pro volání rozhraní REST API Microsoft Graph:

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

V ukázkové aplikaci vytvořené v tomto kurzu se `callMSGraph()` Metoda používá k vytvoření `GET` požadavku HTTP proti chráněnému prostředku, který vyžaduje token. Požadavek potom vrátí obsah volajícímu. Tato metoda přidá získaný token v *autorizační hlavičce protokolu HTTP*. V ukázkové aplikaci vytvořené v tomto kurzu je chráněným prostředkem koncový bod Microsoft Graph API *já* , který zobrazuje informace o profilu přihlášeného uživatele.

## <a name="test-your-application"></a>Testování aplikace

Dokončili jste vytváření aplikace a teď jste připraveni spustit Node.js webový server a otestovat funkčnost aplikace.

1. Spusťte Node.js webový server spuštěním následujícího příkazu v kořenovém adresáři složky projektu:

   ```console
   npm start
   ```
1. V prohlížeči přejděte na adresu `http://localhost:3000` nebo `http://localhost:<port>` , kde `<port>` je port, na kterém váš webový server naslouchá. Měl by se zobrazit obsah souboru *index.html* a **přihlašovací** tlačítko.

### <a name="sign-in-to-the-application"></a>Přihlášení k aplikaci

Poté, co prohlížeč načte soubor *index.html* , vyberte možnost **Přihlásit**se. Budete vyzváni k přihlášení pomocí koncového bodu Microsoft Identity Platform:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Webový prohlížeč, který zobrazuje přihlašovací dialog":::

### <a name="provide-consent-for-application-access"></a>Poskytnutí souhlasu pro přístup k aplikaci

Při prvním přihlášení k aplikaci se zobrazí výzva, abyste udělili přístup k vašemu profilu a přihlásili se k nim:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Dialog obsahu zobrazený ve webovém prohlížeči":::

Pokud souhlasíte s požadovanými oprávněními, webové aplikace zobrazí vaše uživatelské jméno a označuje úspěšné přihlášení:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Výsledky úspěšného přihlášení ve webovém prohlížeči":::

### <a name="call-the-graph-api"></a>Volání Graph API

Po přihlášení vyberte **Zobrazit profil** pro zobrazení informací o profilu uživatele vrácených v odpovědi z volání rozhraní Microsoft Graph API:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="Informace o profilu z Microsoft Graph zobrazené v prohlížeči":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje, aby *uživatel. přečetl* obor pro čtení profilu uživatele. Ve výchozím nastavení je tento obor automaticky přidán do každé aplikace, která je zaregistrována v Azure Portal. Jiná rozhraní API pro Microsoft Graph a také vlastní rozhraní API pro back-end Server můžou vyžadovat další obory. Například rozhraní Microsoft Graph API vyžaduje pro výpis e-mailu uživatele obor *mail. Read* .

Když přidáváte obory, můžou se uživatelům zobrazit výzva k poskytnutí dalšího souhlasu pro přidané obory.

Pokud rozhraní API pro back-end nevyžaduje obor, což se nedoporučuje, můžete použít `clientId` jako obor v voláních pro získání tokenů.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili jednostránkovou aplikaci v JavaScriptu (SPA), která používá knihovnu Microsoft Authentication Library (MSAL) pro JavaScript v 2.0:

> [!div class="checklist"]
> * Provedení toku autorizačního kódu OAuth 2,0 s PKCE
> * Přihlaste se k osobním účtům Microsoft i pracovním a školním účtům.
> * Získání přístupového tokenu
> * Zavolejte Microsoft Graph nebo vlastní rozhraní API, které vyžaduje přístupové tokeny získané z koncového bodu Microsoft Identity Platform.

Další informace o toku autorizačního kódu, včetně rozdílů mezi toky implicitního a ověřovacího kódu, najdete v části [Microsoft Identity Platform a tok autorizačního kódu OAuth 2,0](v2-oauth2-auth-code-flow.md).

Pokud byste chtěli podrobně hlubší vývoj aplikací v JavaScriptu na platformě Microsoft Identity Platform, scénář s více částmi: řada článků s [jednou stránkou](scenario-spa-overview.md) vám může pomáhat začít.
