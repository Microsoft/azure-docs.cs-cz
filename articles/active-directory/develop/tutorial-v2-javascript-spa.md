---
title: JavaScript jednostránkový kurz aplikace - Platforma identit y Microsoft | Azure
description: Jak mohou aplikace JavaScript SPA volat rozhraní API, které vyžaduje přístupové tokeny podle koncového bodu Azure Active Directory v2.0
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ec47850ce4cccb6a891c7e5aef2644550bc3e39a
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990952"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Přihlášení uživatelů a volání rozhraní Microsoft Graph API z jednostránkové aplikace JavaScript (SPA)

Tato příručka ukazuje, jak může jednostránková aplikace JavaScriptu (SPA):
- Přihlaste se k osobním účtům a pracovním a školním účtům 
- Získání přístupového tokenu
- Volání rozhraní Microsoft Graph API nebo jiných rozhraní API, která vyžadují přístupové tokeny z *koncového bodu platformy identit microsoftu*

>[!NOTE]
> Pokud s platformou identit Microsoftu teče tezí, doporučujeme začít s [přihlášením uživatelů a získat přístupový token v rychlém startu javascriptového spa](quickstart-v2-javascript.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Ukázková aplikace vytvořená touto příručkou umožňuje javascriptovému spa dotazovat rozhraní Microsoft Graph API nebo webového rozhraní API, které přijímá tokeny z koncového bodu platformy identit y Microsoft. V tomto scénáři po přihlášení uživatele je požadován přístupový token a přidán do požadavků HTTP prostřednictvím hlavičky autorizace. Tento token bude použit k získání profilu uživatele a e-mailů prostřednictvím **rozhraní MS Graph API**. Pořízení a obnovení tokenu zpracovává **knihovna Microsoft Authentication Library (MSAL) pro JavaScript**.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovnu:

|Knihovna|Popis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Knihovna ověřování Microsoft pro JavaScript|

> [!NOTE]
> *Msal.js cílí* na koncový bod platformy identit microsoftu, který umožňuje osobním účtům a školním a pracovním účtům přihlásit se a získat tokeny. Koncový bod platformy identit y Microsoft má [určitá omezení](../azuread-dev/azure-ad-endpoint-comparison.md#limitations).
> Chcete-li porozumět rozdílům mezi koncovými body v1.0 a v2.0, přečtěte [si příručku pro porovnání koncových bodů](../azuread-dev/azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Nastavení webového serveru nebo projektu

> Chcete si místo toho stáhnout projekt této ukázky? [Stáhněte soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Chcete-li nakonfigurovat ukázku kódu před jeho spuštěním, přejděte k [kroku konfigurace](#register-your-application).

## <a name="prerequisites"></a>Požadavky

* Ke spuštění tohoto kurzu potřebujete místní webový server, například [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core)nebo IIS Express integrace s [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Pokyny v této příručce jsou založeny na webovém serveru integrovaném v souboru Node.js. Doporučujeme používat [Visual Studio Code](https://code.visualstudio.com/download) jako integrované vývojové prostředí (IDE).

## <a name="create-your-project"></a>Vytvoření projektu

Ujistěte se, že máte nainstalovaný [soubor Node.js,](https://nodejs.org/en/download/) a pak vytvořte složku pro hostování aplikace. Tam budeme implementovat jednoduchý [webový](https://expressjs.com/) server `index.html` Express sloužit váš soubor. 

1. Nejprve pomocí integrovaného terminálu Visual Studio Code vyhledejte složku projektu a potom nainstalujte Express pomocí npm.

1. Dále vytvořte soubor JS `server.js`s názvem a přidejte následující kód:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

Nyní máte jednoduchý server sloužit vaše SPA. Zamýšlená struktura složek na konci tohoto kurzu je následující:

![textové zobrazení zamýšlené struktury složek SPA](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Vytvoření spa ui

1. Vytvořte `index.html` soubor pro javascriptové spa. Tento soubor implementuje rozhraní vytvořené pomocí **rozhraní Bootstrap 4 Framework** a importuje soubory skriptů pro konfiguraci, ověřování a volání rozhraní API.

   Do `index.html` souboru přidejte následující kód:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
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

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > Verzi souboru MSAL.js v předchozím skriptu můžete nahradit nejnovější verzí vydané v části [Verze msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
   
2. Nyní vytvořte soubor .js s názvem `ui.js`, který bude přistupovat a aktualizovat prvky DOM a přidat následující kód:

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

Než budete pokračovat v ověřování, zaregistrujte svou aplikaci ve **službě Azure Active Directory**.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Pokud váš účet umožňuje přístup k více než jednomu tenantovi, vyberte účet v pravém horním bodě a nastavte relaci portálu na klienta Azure AD, který chcete použít.
1. Přejděte na stránku Microsoft identity platformy pro vývojáře [Registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Když se zobrazí stránka **Zaregistrovat aplikaci**, zadejte název pro vaši aplikaci.
1. V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft**.
1. V části **Identifikátor URI přesměrování** vyberte **webovou** platformu z rozevíracího seznamu a nastavte hodnotu na adresu URL aplikace, která je založena na webovém serveru.
1. Vyberte **Zaregistrovat**.
1. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
1. Tento rychlý start vyžaduje [implicitní tok grantu,](v2-oauth2-implicit-grant-flow.md) který má být povolen. V levém podokně registrované aplikace vyberte **možnost Ověřování**.
1. V **rozšířenénastavení**, v části **Implicitní udělení**, zaškrtněte **políčka ID tokeny** a **tokeny aplikace Access.** ID tokeny a přístupové tokeny jsou povinné, protože tato aplikace musí přihlásit uživatele a volat rozhraní API.
1. Vyberte **Uložit**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Nastavení adresy URL přesměrování pro soubor Node.js
>
> Soubor Node.js můžete nastavit port webového serveru v souboru *server.js.* Tento kurz používá port 3000, ale můžete použít jakýkoli jiný dostupný port.
>
> Chcete-li nastavit adresu URL přesměrování v informacích o registraci aplikace, přepněte zpět do **podokna Registrace aplikace** a proveďte jednu z následujících akcí:
>
> - Nastavit *`http://localhost:3000/`* jako **adresu URL přesměrování**.
> - Pokud používáte vlastní port TCP, *`http://localhost:<port>/`* použijte (kde * \<>portu* je vlastní číslo portu TCP).
>   1. Zkopírujte hodnotu **adresy URL.**
>   1. Přepněte zpět do **podokna Registrace aplikace** a vložte zkopírovanou hodnotu jako **adresu URL přesměrování**.
>

### <a name="configure-your-javascript-spa"></a>Konfigurace javascriptového spa

Vytvořte nový soubor JS s názvem `authConfig.js`, který bude obsahovat parametry konfigurace pro ověřování, a přidejte následující kód:

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
    scopes: ["Mail.Read"]
  };
```

 Kde:
 - Enter_the_Application_Id_Here>je **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali. * \<*
 - Enter_the_Cloud_Instance_Id_Here>je instancí cloudu Azure. * \<* Pro hlavní nebo globální cloud *https://login.microsoftonline.com*Azure jednoduše zadejte . Národní **national** cloudy (například Čína) viz [Národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - Enter_the_Tenant_info_here>je nastavena na jednu z následujících možností: * \<*
   - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID klienta** nebo **názvem klienta** (například *contoso.microsoft.com*).
   - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**.
   - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu **běžnými**. Chcete-li omezit podporu pouze na *osobní účty Microsoft*, nahraďte tuto hodnotu **spotřebiteli**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Přihlášení uživatele pomocí knihovny Ověřování Microsoft (MSAL)

Vytvořte nový soubor JS s názvem `authPopup.js`, který bude obsahovat logiku ověřování a získávání tokenů, a přidejte následující kód:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }
   
   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
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
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Poté, co uživatel poprvé vybere tlačítko **Přihlásit** se, `signIn` metoda volá `loginPopup` k přihlášení uživatele. Tato metoda otevře automaticky otevírané okno s *koncovým bodem platformy identity společnosti Microsoft,* aby se vyzývalo a ověřilo pověření uživatele. Po úspěšném přihlášení je uživatel přesměrován zpět na původní stránku *index.html.* Token je přijat, zpracován `msal.js`a informace obsažené v tokenu jsou uloženy do mezipaměti. Tento token se označuje jako *token ID* a obsahuje základní informace o uživateli, jako je například zobrazované jméno uživatele. Pokud máte v plánu použít všechna data poskytnutá tímto tokenem pro jakékoli účely, musíte se ujistit, že tento token je ověřen back-endový server zaručit, že token byl vydán platnému uživateli pro vaši aplikaci.

Spa generované touto příručkou `acquireTokenSilent` volání `acquireTokenPopup` nebo získat *přístupový token* slouží k dotazování rozhraní MICROSOFT Graph API pro informace o profilu uživatele. Pokud potřebujete ukázku, která ověřuje token ID, podívejte se na [tuto](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 ukázka") ukázkovou aplikaci v GitHubu. Ukázka používá ASP.NET webové rozhraní API pro ověření tokenu.

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Po počátečním přihlášení nechcete uživatele žádat o opětovné ověření pokaždé, když potřebují požádat o token pro přístup k prostředku. Takže *acquireTokenSilent* by měly být použity většinu času k získání tokenů. Existují však situace, kdy je třeba vynutit uživatelům interakci s koncovým bodem platformy identit y Microsoft. Příklady obsahují:

- Uživatelé musí znovu zadat svá pověření, protože platnost hesla vypršela.
- Vaše aplikace požaduje přístup k prostředku a potřebujete souhlas uživatele.
- Je vyžadováno dvoufaktorové ověřování.

Volání *acquireTokenPopup* otevře automaticky otevírané okno (nebo *získatTokenRedirect* přesměruje uživatele do koncového bodu platformy identity Microsoftu). V tomto okně uživatelé potřebují komunikovat potvrzením svých pověření, udělením souhlasu s požadovaným prostředkem nebo dokončením dvoufaktorového ověřování.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Metoda `acquireTokenSilent` zpracovává získávání tokenu a obnovení bez jakékoli interakce uživatele. Po `loginPopup` (nebo `loginRedirect`) je spuštěn a `acquireTokenSilent` poprvé, je metoda běžně používané k získání tokeny používané pro přístup k chráněným prostředkům pro následná volání. (Volání požádat nebo obnovit tokeny jsou prováděny tiše.) `acquireTokenSilent` v některých případech selhat. Například vypršela platnost hesla uživatele. Aplikace může tuto výjimku zpracovat dvěma způsoby:

1. Okamžitě zavolejte, `acquireTokenPopup` což spustí výzvu k přihlášení uživatele. Tento vzor se běžně používá v online aplikacích, kde není k dispozici žádný neověřený obsah v aplikaci. Ukázka generovaná tímto řízeným nastavením používá tento vzor.

1. Aplikace mohou také vytvořit vizuální indikaci pro uživatele, že je vyžadováno interaktivní přihlášení, takže uživatel může vybrat `acquireTokenSilent` správný čas pro přihlášení, nebo aplikace může opakovat později. To se běžně používá, když uživatel může používat jiné funkce aplikace bez přerušení. Například může být neověřený obsah k dispozici v aplikaci. V takovém případě se uživatel může rozhodnout, kdy se chce přihlásit k přístupu k chráněnému prostředku nebo aktualizovat zastaralé informace.

> [!NOTE]
> Tento rychlý start `loginPopup` `acquireTokenPopup` používá metody a ve výchozím nastavení. Pokud používáte aplikaci Internet Explorer jako prohlížeč, doporučujeme použít `loginRedirect` a `acquireTokenRedirect` metody z důvodu [známého problému](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) souvisejícího se způsobem, jakým aplikace Internet Explorer zpracovává automaticky otevíraná okna. Pokud byste chtěli vidět, jak dosáhnout `Redirect methods`stejného výsledku pomocí , [viz](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js). 
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Volání rozhraní Microsoft Graph API pomocí tokenu, který jste právě získali

1. Nejprve vytvořte soubor JS s názvem `graphConfig.js`, který bude ukládat koncové body REST. Přidejte následující kód:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Kde:
   - Enter_the_Graph_Endpoint_Here>je instancí ms graph api. * \<* Pro koncový bod rozhraní API globálního grafu jednoduše `https://graph.microsoft.com`nahraďte tento řetězec . Pro nasazení národního cloudu, naleznete [v dokumentaci rozhraní API grafu](https://docs.microsoft.com/graph/deployments).

1. Dále vytvořte soubor JS `graph.js`s názvem , který provede volání REST rozhraní Microsoft Graph API a přidá následující kód:

   ```javascript
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
       .catch(error => console.log(error))
   }
   ```

<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Další informace o volání REST proti chráněnému rozhraní API

V ukázkové aplikaci vytvořené `callMSGraph()` touto příručkou se `GET` metoda používá k vytvoření požadavku HTTP proti chráněnému prostředku, který vyžaduje token. Požadavek pak vrátí obsah volajícímu. Tato metoda přidá získaný token v *hlavičce autorizace PROTOKOLU HTTP*. Pro ukázkovou aplikaci vytvořenou touto příručkou je prostředek koncový bod *rozhraní* Microsoft Graph API, který zobrazuje informace o profilu uživatele.

<!--end-collapse-->

## <a name="test-your-code"></a>Testování kódu

1. Nakonfigurujte server tak, aby naslouchal portu TCP, který je založen na umístění souboru *index.html.* V souboru Node.js spusťte webový server pro poslech portu spuštěním následujících příkazů na příkazovém řádku ze složky aplikace:

   ```bash
   npm install
   npm start
   ```
1. V prohlížeči **http://localhost:3000** zadejte nebo **http://localhost:{port}**, *kde* port je port, který váš webový server poslouchá. Měli byste vidět obsah souboru *index.html* a tlačítko **Přihlásit** se.

## <a name="test-your-application"></a>Testování aplikace

Po načtení souboru *index.html* vyberte **přihlásit**. Budete vyzváni k přihlášení pomocí koncového bodu platformy identit microsoftu:

![Přihlašovací okno účtu JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Poskytnout souhlas s přístupem k aplikaci

Při prvním přihlášení k aplikaci budete vyzváni k udělení přístupu k profilu a přihlášení:

![Okno "Požadovaná oprávnění"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Zobrazit výsledky aplikace

Po přihlášení se informace o profilu uživatele vrátí v odpovědi rozhraní MICROSOFT Graph API, která se zobrazí:

![Výsledky volání rozhraní MICROSOFT Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje, aby obor *user.read* četl profil uživatele. Ve výchozím nastavení je tento obor automaticky přidán do každé aplikace, která je registrována na registračním portálu. Další rozhraní API pro Microsoft Graph, stejně jako vlastní rozhraní API pro back-end server, může vyžadovat další obory. Rozhraní API aplikace Microsoft Graph například vyžaduje obor *Mail.Read,* aby bylo možné vypsat e-maily uživatele.

> [!NOTE]
> Při zvýšení počtu oborů může být uživatel vyzván k zadání dalších souhlasů.

Pokud rozhraní API back-end nevyžaduje obor (nedoporučuje se), můžete použít *clientId* jako obor v volání získat tokeny.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
