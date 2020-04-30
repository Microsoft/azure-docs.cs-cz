---
title: Kurz pro jednostránkové aplikace v JavaScriptu 2,0 – Microsoft Identity Platform | Azure
description: Jak aplikace JavaScript SPA můžou pomocí toku kódu ověřování volat rozhraní API, které vyžaduje přístupové tokeny pomocí koncového bodu Azure Active Directory v 2.0
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ee156c8118a67061d0a000867ee64fe1f3ebd18c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182136"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>Přihlaste se uživatelům a zavolejte Microsoft Graph API z jednostránkové aplikace v JavaScriptu (SPA) – MSAL. js 2,0

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před všeobecnou dostupností změnit (GA).

V tomto kurzu se používá verze MSAL. js, která používá tok autorizačního kódu OAuth 2,0 s PKCE. Další informace o tomto protokolu a o rozdílech mezi postupem implicitního toku a toku autorizačního kódu naleznete v [dokumentaci](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow). Pokud hledáte kurz, který používá implicitní tok, přečtěte si [kurz MSAL. js v1](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa).

Tato verze MSAL. js se vylepšuje v aktuální knihovně MSAL-Core a používá tok autorizačního kódu v prohlížeči. Většina funkcí, které jsou k dispozici ve staré knihovně, je v této verzi k dispozici, ale drobné odlišnosti tok ověřování v obou. Tato **verze nepodporuje implicitní** tok.

Tato příručka ukazuje, jak může jednostránková aplikace v JavaScriptu (SPA):
- Přihlaste se k osobním účtům a pracovním a školním účtům
- Získání přístupového tokenu
- Volání rozhraní API Microsoft Graph nebo jiných rozhraní API, která vyžadují přístupové tokeny z *koncového bodu Microsoft Identity Platform*

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Další informace

Ukázková aplikace vytvořená touto příručkou umožňuje, aby se v prostředí JavaScript SPA dotazoval rozhraní API pro Microsoft Graph nebo webové rozhraní API, které přijímá tokeny z koncového bodu Microsoft Identity Platform. V tomto scénáři se po přihlášení uživatele vyžádá přístupový token a přidá se do požadavků HTTP prostřednictvím autorizační hlavičky. Získání a obnovení tokenu jsou zpracovávány pomocí knihovny Microsoft Authentication Library (MSAL).

### <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovnu:

|Knihovna|Popis|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Knihovna Microsoft Authentication Library pro JavaScript Browser – balíček|

## <a name="set-up-your-web-server-or-project"></a>Nastavení webového serveru nebo projektu

Chcete místo toho stáhnout tento projekt ukázky? Chcete-li spustit projekt pomocí místního webového serveru, jako je například Node. js, naklonujte soubory projektu:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Chcete-li před spuštěním nakonfigurovat ukázku kódu, přejděte k [kroku konfigurace](#register-your-application).

## <a name="prerequisites"></a>Požadavky

* Pro spuštění tohoto kurzu potřebujete místní webový server, jako je například [Node. js](https://nodejs.org/en/download/) nebo [.NET Core](https://www.microsoft.com/net/core).

* Pokud používáte Node. js ke spuštění projektu, nainstalujte integrované vývojové prostředí (IDE), jako je [Visual Studio Code](https://code.visualstudio.com/download) , a upravte soubory projektu.

* Pokyny v tomto kurzu jsou založené na Node. js.

## <a name="create-your-project"></a>Vytvořit projekt

Ujistěte se, že máte [Node. js](https://nodejs.org/en/download/) nainstalovaný, a pak vytvořte složku pro hostování aplikace. Dále implementujte webový server s malým [expresním](https://expressjs.com/) zavedením, který bude obsluhovat váš `index.html` soubor.

1. Nejprve přejděte do složky vašeho projektu v terminálu a spusťte následující příkazy NPM.
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. Dále vytvořte soubor. js s názvem *Server. js*a přidejte následující kód:

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

Teď máte k dispozici jednoduchý Server, který bude sloužit pro SPA. Zamýšlená struktura složek na konci tohoto kurzu je následující:

![text, který je znázorněný v příslušné struktuře složek zabezpečeného hesla](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Vytvoření uživatelského rozhraní SPA

1. Vytvořte soubor *index. html* pro váš JavaScript Spa ve složce *aplikace* . Tento soubor implementuje uživatelské rozhraní vytvořené pomocí **rutiny Bootstrap 4** a importuje soubory skriptu pro konfiguraci, ověřování a volání rozhraní API.

   V souboru *index. html* přidejte následující kód:

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
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
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
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

   > [!TIP]
   > Verzi MSAL. js můžete v předchozím skriptu nahradit nejnovější vydanou verzí ve [verzích MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


2. Nyní vytvořte soubor. js s názvem *UI. js* , který bude mít přístup k elementům DOM a bude aktualizován, a přidejte následující kód:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
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

## <a name="register-your-application"></a>Registrace vaší aplikace

Postupujte podle pokynů a [Zaregistrujte novou jednu stránkovou aplikaci](https://docs.microsoft.com/zure/active-directory/develop/scenario-spa-app-registration).

#### <a name="set-a-redirect-url-for-nodejs"></a>Nastavení adresy URL pro přesměrování pro Node. js

Pro Node. js můžete nastavit port webového serveru v souboru *Server. js* . V tomto kurzu se používá port 3000, ale můžete použít jakýkoli jiný dostupný port.

Chcete-li nastavit adresu URL pro přesměrování v informacích o registraci aplikace, přepněte zpět do podokna **Registrace aplikace** a zaregistrujte nové **zabezpečené ověřování** na základě jedné z následujících možností:

- Nastaví *`http://localhost:3000/`* se jako **Adresa URL pro přesměrování**.
- Pokud používáte vlastní port TCP, použijte *`http://localhost:<port>/`* (kde * \<port>* je vlastní číslo portu TCP).

### <a name="configure-your-javascript-spa"></a>Konfigurace vašeho JavaScriptu SPA

Vytvořte nový soubor. js s názvem *authConfig. js* , který bude obsahovat parametry konfigurace pro ověřování, a přidejte následující kód:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
   scopes: ["openid", "profile", "User.Read"]
  };

   // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
   scopes: ["User.Read", "Mail.Read"]
  };

```

 Kde:
 - Enter_the_Application_Id_Here>je **ID aplikace (klienta)** pro zaregistrovanou aplikaci. * \<*
 - Enter_the_Cloud_Instance_Id_Here>je instance cloudu Azure. * \<* V případě hlavního nebo globálního cloudu Azure stačí zadat *https://login.microsoftonline.com*. Pro **národní** cloudy (například Čína) si přečtěte téma [národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - Enter_the_Tenant_info_here>je nastavená na jednu z následujících možností: * \<*
   - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například *contoso.Microsoft.com*).
   - Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**.
   - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu **běžnými**. Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu **příjemci**.
- *\ Enter_the_Redirect_Uri_Here>* je port, který jste zaregistrovali na portálu*`http://localhost:3000/`*().


Vytvořte nový soubor. js s názvem `graphConfig.js`, který bude obsahovat parametry konfigurace volající Microsoft Graph rozhraní API, a přidejte následující kód:
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- Enter_the_Graph_Endpoint_Here>je instance MS Graph API. * \<* V případě globálního koncového bodu služby MS Graph API jednoduše nahraďte `https://graph.microsoft.com`tento řetězec řetězcem. Pro národní cloudová nasazení si prosím přečtěte [Graph API dokumentaci](https://docs.microsoft.com/graph/deployments).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Přihlášení uživatele pomocí knihovny Microsoft Authentication Library (MSAL)

### <a name="popup"></a>Oken
Vytvořte nový soubor. js s názvem `authPopup.js`, který bude obsahovat vaše ověřování a logiku pro získání tokenu pro místní nabídku přihlášení, a přidejte následující kód:

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
                });
        });
}

function seeProfile() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(loginRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.classList.add('d-none');
                mailButton.classList.remove('d-none');
            }).catch(error => {
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>Přesměrování
Vytvořte nový soubor. js s názvem `authRedirect.js`, který bude obsahovat logiku ověřování a získání tokenu pro přesměrování přihlášení a přidejte následující kód:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>Další informace

Když uživatel poprvé vybere tlačítko pro **přihlášení** , `signIn` metoda se zavolá `loginPopup` na přihlášení uživatele. Tato metoda otevře automaticky otevírané okno s *koncovým bodem Microsoft Identity Platform* , kde se zobrazí výzva a ověří přihlašovací údaje uživatele. Po úspěšném přihlášení spustí *msal. js* [tok autorizačního kódu](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).

V tomto okamžiku se do koncového bodu tokenu Protected CORS pošle autorizační kód chráněný PKCE a vyměňují se pro tokeny. Obdrží se token ID, přístupový token a aktualizační token, který zpracovává *msal. js*, a informace obsažené v tokenu se uloží do mezipaměti.

Token ID obsahuje základní informace o uživateli, jako je jeho zobrazované jméno. Pokud máte v úmyslu používat data poskytnutá tímto tokenem, musí být ověřena vaším back-end serverem, aby bylo zaručeno, že token byl vydán pro platného uživatele pro vaši aplikaci. Obnovovací token má omezené trvání a vyprší za 24 hodin. Obnovovací token lze použít k tichému získání nových přístupových tokenů.

Zabezpečené ověřování hesla vygenerované touto příručkou volá `acquireTokenSilent` nebo `acquireTokenPopup` získá *přístupový token* , který se používá k dotazování rozhraní API pro Microsoft Graph pro informace o profilu uživatele. Pokud potřebujete ukázku, která ověří token ID, přečtěte si ukázkovou aplikaci [Active-Directory-JavaScript-singlepageapp-dotnet-WebApi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) na GitHubu. Ukázka používá webové rozhraní API ASP.NET pro ověření tokenu.

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Po počátečním přihlášení by se uživatelé neměli požádáni, aby znovu ověřili pokaždé, když potřebují požádat o token pro přístup k prostředku. Chcete-li zabránit takové žádosti o opakované `acquireTokenSilent`ověření, použijte. Existují však situace, kdy může být nutné vynutit, aby uživatelé mohli pracovat s koncovým bodem Microsoft Identity Platform. Příklad:

- Uživatelé musí znovu zadat své přihlašovací údaje, protože vypršela platnost hesla.
- Vaše aplikace požaduje přístup k prostředku a potřebujete souhlas uživatele.
- Je vyžadováno dvojúrovňové ověřování.

Volání `acquireTokenPopup` otevře automaticky otevírané okno (nebo `acquireTokenRedirect` přesměruje uživatele na koncový bod Microsoft Identity Platform). V tomto okně musí uživatelé pracovat pomocí potvrzení svých přihlašovacích údajů, udělení souhlasu k požadovanému prostředku nebo dokončením dvojúrovňového ověřování.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`acquireTokenSilent` Metoda zpracovává získání a obnovení tokenu bez zásahu uživatele. Po `loginPopup` prvním spuštění `loginRedirect`(nebo) je metoda, `acquireTokenSilent` která se běžně používá k získání tokenů používaných pro přístup k chráněným prostředkům pro následná volání. (Volání požadavků na požadavky nebo obnovení tokenů se provádí tiše.) `acquireTokenSilent` v některých případech může selhat. Může například dojít k vypršení platnosti hesla uživatele. Vaše aplikace může tuto výjimku zpracovat dvěma způsoby:

1. `acquireTokenPopup` Okamžitě zavolejte, aby se aktivovala výzva k přihlášení uživatele. Tento model se běžně používá v online aplikacích, kde není k dispozici žádný neautorizovaný obsah v aplikaci pro uživatele. Ukázka vygenerovaná tímto procesem instalace používá tento model.

1. Vizuálně nahlaste uživateli, že je vyžadováno interaktivní přihlášení, aby mohl uživatel vybrat správný čas pro přihlášení, nebo může aplikace opakovat `acquireTokenSilent` později. Tato technika se běžně používá v případě, že uživatel může použít jiné funkce aplikace, aniž by došlo k přerušení. V aplikaci může být například dostupný neautorizovaný obsah. V takovém případě se uživatel může rozhodnout, kdy se chce přihlásit k přístupu k chráněnému prostředku, nebo aktualizovat zastaralé informace.

> [!NOTE]
> V tomto rychlém `loginPopup` startu `acquireTokenPopup` se ve výchozím nastavení používají metody a. Pokud jako prohlížeč používáte Internet Explorer, doporučuje se použít `loginRedirect` metody a `acquireTokenRedirect` , protože se jedná o [známý problém](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) související s tím, jak Internet Explorer zpracovává automaticky otevíraná okna. Chcete-li zjistit, jak dosáhnout stejného výsledku pomocí metod přesměrování, přečtěte si [*authRedirect. js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Volání rozhraní API Microsoft Graph pomocí tokenu, který jste právě získali


 Vytvořte soubor. js s názvem *Graph. js* , který provede volání REST pro Microsoft Graph rozhraní API a přidejte následující kód:

   ```javascript

// Helper function to call MS Graph API endpoint
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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Další informace o tom, jak provést volání REST proti chráněnému rozhraní API

V ukázkové aplikaci vytvořené touto příručkou se `callMSGraph()` metoda používá k vytvoření požadavku HTTP `GET` proti chráněnému prostředku, který vyžaduje token. Požadavek potom vrátí obsah volajícímu. Tato metoda přidá získaný token v *autorizační hlavičce protokolu HTTP*. Pro ukázkovou aplikaci vytvořenou touto příručkou je prostředkem koncový bod Microsoft Graph API *já* , který zobrazuje informace o profilu uživatele.

## <a name="test-your-code"></a>Testování kódu

1. Pro Node. js spusťte webový server tak, že ve složce aplikace spustíte následující příkazy:

   ```bash
   npm install
   npm start
   ```
1. V prohlížeči zadejte **http://localhost:3000** nebo **http://localhost:{port}**, kde *port* je port, na kterém naslouchá webový server. Měl by se zobrazit obsah souboru *index. html* a **přihlašovací** tlačítko.

## <a name="test-your-application"></a>Testování aplikace

Poté, co prohlížeč načte soubor *index. html* , vyberte možnost **Přihlásit**se. Budete vyzváni k přihlášení pomocí koncového bodu Microsoft Identity Platform:

![Přihlašovací okno účtu v jazyce JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Poskytnutí souhlasu pro přístup k aplikaci

Při prvním přihlášení do aplikace budete vyzváni, abyste udělili přístup k vašemu profilu a přihlásili se k nim:

![Okno požadovaná oprávnění](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Zobrazit výsledky aplikace

Po přihlášení se v odpovědi rozhraní API Microsoft Graph zobrazí informace o vašem profilu uživatele:

![Výsledky z volání rozhraní API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje, aby *uživatel. přečetl* obor pro čtení profilu uživatele. Ve výchozím nastavení je tento obor automaticky přidán do každé aplikace, která je zaregistrována v Azure Portal. Jiná rozhraní API pro Microsoft Graph a také vlastní rozhraní API pro back-end Server můžou vyžadovat další obory. Například rozhraní Microsoft Graph API vyžaduje pro výpis e-mailů uživatele obor *mail. Read* .

> [!NOTE]
> Uživatel může být vyzván k dalšímu souhlasu při přidávání oborů.

Pokud rozhraní API back-endu nevyžaduje obor (nedoporučuje se), můžete použít *ClientID* jako obor v voláních pro získání tokenů.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

[Úložiště GitHub MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js) obsahuje další dokumentaci ke knihovně, nejčastější dotazy a poskytuje podporu pro vydání.
