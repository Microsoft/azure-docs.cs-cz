---
title: 'Kurz: vytvoření jednostránkové aplikace v JavaScriptu, která používá Microsoft Identity Platform pro ověřování | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu vytvoříte jednostránkovou aplikaci v JavaScriptu (SPA), která používá Microsoft Identity Platform k přihlašování uživatelů a získání přístupového tokenu pro volání rozhraní API Microsoft Graph jménem.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/06/2020
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 4eb3c2905f3c1ccfa63da1bb4a8c81decdbc2f2b
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507723"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Kurz: přihlášení uživatelů a volání rozhraní Microsoft Graph API z jednostránkové aplikace v JavaScriptu (SPA)

V tomto kurzu vytvoříte jednostránkovou aplikaci v JavaScriptu (SPA), která podepisuje uživatele a volá Microsoft Graph pomocí implicitního toku. Při vytváření zabezpečeného hesla se používá knihovna Microsoft Authentication Library (MSAL) pro jazyk JavaScript v 1.0.

V tomto kurzu:

> [!div class="checklist"]
> * Vytvořit projekt JavaScriptu pomocí `npm`
> * Registrace aplikace v Azure Portal
> * Přidat kód pro podporu přihlášení a odhlášení uživatele
> * Přidat kód pro volání rozhraní API Microsoft Graph
> * Otestování aplikace

>[!TIP]
> V tomto kurzu se používá MSAL.js V1. x, která je omezená na použití implicitního toku udělení pro jednostránkové aplikace. Místo toho doporučujeme, aby všechny nové aplikace používaly [MSAL.js 2. x a tok autorizačního kódu s podporou PKCE a CORS](tutorial-v2-javascript-auth-code.md) .

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/download/) pro spuštění místního webového serveru.
* [Visual Studio Code](https://code.visualstudio.com/download) nebo jiný editor pro úpravu souborů projektu.
* Moderní webový prohlížeč. Aplikace **Internet Explorer** není aplikací, kterou jste v tomto kurzu sestavili, **podporována** , protože používá konvence [ES6](http://www.ecma-international.org/ecma-262/6.0/) v aplikaci.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

Ukázková aplikace vytvořená touto příručkou umožňuje, aby se v prostředí JavaScript SPA dotazoval rozhraní API pro Microsoft Graph nebo webové rozhraní API, které přijímá tokeny z koncového bodu Microsoft Identity Platform. V tomto scénáři se po přihlášení uživatele vyžádá přístupový token a přidá se do požadavků HTTP prostřednictvím autorizační hlavičky. Pomocí tohoto tokenu se získá profil uživatele a e-maily přes **MS Graph API**.

Získání a obnovení tokenu jsou zpracovávány [knihovnou Microsoft Authentication Library (MSAL) pro jazyk JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js).

## <a name="set-up-your-web-server-or-project"></a>Nastavení webového serveru nebo projektu

> Chcete místo toho stáhnout tento projekt ukázky? [Stáhněte si soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Chcete-li před spuštěním nakonfigurovat ukázku kódu, přejděte k [kroku konfigurace](#register-your-application).

## <a name="create-your-project"></a>Vytvoření projektu

Ujistěte se, že máte nainstalovanou [Node.js](https://nodejs.org/en/download/) a pak vytvořte složku pro hostování aplikace. Tam budeme [implementovat jednoduchý webový](https://expressjs.com/) Server, který bude obsluhovat váš `index.html` soubor.

1. Pomocí terminálu (například Visual Studio Code Integrated Terminal) najděte složku vašeho projektu a pak zadejte:

   ```console
   npm init
   ```

2. Dále nainstalujte požadované závislosti:

   ```console
   npm install express --save
   npm install morgan --save
   ```

1. Nyní vytvořte soubor. js s názvem `server.js` a přidejte následující kód:

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

Teď máte k dispozici jednoduchý Server, který bude sloužit pro SPA. Zamýšlená struktura složek na konci tohoto kurzu je následující:

![text, který je znázorněný v příslušné struktuře složek zabezpečeného hesla](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Vytvoření uživatelského rozhraní SPA

1. Vytvořte `index.html` soubor pro váš JavaScript Spa. Tento soubor implementuje uživatelské rozhraní vytvořené pomocí **rutiny Bootstrap 4** a importuje soubory skriptu pro konfiguraci, ověřování a volání rozhraní API.

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
   > Verzi MSAL.js v předchozím skriptu můžete nahradit nejnovější vydanou verzí v části [MSAL.js verze](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

2. Nyní vytvořte soubor. js s názvem `ui.js` , který bude mít přístup k elementům DOM a aktualizován, a přidejte následující kód:

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

## <a name="register-your-application"></a>Registrace aplikace

Než budete pokračovat s ověřováním, zaregistrujte svou aplikaci na **Azure Active Directory**.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
1. Zadejte **název** vaší aplikace. Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
1. V části **identifikátor URI pro přesměrování** vyberte v rozevíracím seznamu **webovou** platformu a pak nastavte tuto hodnotu na adresu URL aplikace, která je založená na vašem webovém serveru.
1. Vyberte **Zaregistrovat**.
1. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
1. V části **Spravovat** vyberte **ověřování**.
1. V části **implicitní udělení** vyberte možnost **tokeny ID** a **přístupové tokeny**. Tokeny ID a přístupové tokeny jsou povinné, protože tato aplikace musí přihlašovat uživatele a volat rozhraní API.
1. Vyberte **Uložit**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Nastavit adresu URL pro přesměrování pro Node.js
>
> V případě Node.js můžete port webového serveru nastavit v souboru *server.js* . V tomto kurzu se používá port 3000, ale můžete použít jakýkoli jiný dostupný port.
>
> Chcete-li nastavit adresu URL pro přesměrování v informacích o registraci aplikace, přepněte zpět do podokna **Registrace aplikace** a proveďte jednu z následujících akcí:
>
> - Nastaví *`http://localhost:3000/`* se jako **Adresa URL pro přesměrování**.
> - Pokud používáte vlastní port TCP, použijte *`http://localhost:<port>/`* (kde *\<port>* je vlastní číslo portu TCP).
>   1. Zkopírujte hodnotu **URL** .
>   1. Přepněte zpět do podokna **Registrace aplikace** a vložte zkopírovanou hodnotu jako **adresu URL pro přesměrování**.
>

### <a name="configure-your-javascript-spa"></a>Konfigurace vašeho JavaScriptu SPA

Vytvořte nový soubor. js s názvem `authConfig.js` , který bude obsahovat parametry konfigurace pro ověřování, a přidejte následující kód:

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
 - *\<Enter_the_Application_Id_Here>* je **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali.
 - *\<Enter_the_Cloud_Instance_Id_Here>* je instancí cloudu Azure. V případě hlavního nebo globálního cloudu Azure stačí zadat *https://login.microsoftonline.com* . Pro **národní** cloudy (například Čína) si přečtěte téma [národní cloudy](./authentication-national-cloud.md).
 - *\<Enter_the_Tenant_info_here>* je nastavená na jednu z následujících možností:
   - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například *contoso.Microsoft.com*).
   - Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**.
   - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu **běžnými**. Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu **příjemci**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Přihlášení uživatele pomocí knihovny Microsoft Authentication Library (MSAL)

Vytvořte nový soubor. js s názvem `authPopup.js` , který bude obsahovat vaše ověřování a logiku pro pořízení tokenu, a přidejte následující kód:

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

### <a name="more-information"></a>Další informace

Když uživatel poprvé vybere tlačítko pro **přihlášení** , `signIn` metoda se zavolá na `loginPopup` přihlášení uživatele. Tato metoda otevře automaticky otevírané okno s *koncovým bodem Microsoft Identity Platform* , kde se zobrazí výzva a ověří přihlašovací údaje uživatele. Po úspěšném přihlášení se uživatel přesměruje zpátky na původní *index.htmstránku l* . Obdrží se token, který se zpracovává, `msal.js` a informace obsažené v tokenu se ukládají do mezipaměti. Tento token je známý jako *token ID* a obsahuje základní informace o uživateli, jako je například zobrazované jméno uživatele. Pokud plánujete použít data poskytnutá tímto tokenem pro jakékoli účely, musíte se ujistit, že váš back-end Server ověří tento token, aby bylo zaručeno, že token byl vydán pro platného uživatele pro vaši aplikaci.

Zabezpečené ověřování hesla vygenerované touto příručkou volá `acquireTokenSilent` nebo `acquireTokenPopup` získá *přístupový token* , který se používá k dotazování rozhraní API pro Microsoft Graph pro informace o profilu uživatele. Pokud potřebujete ukázku, která ověří token ID, podívejte se na [tuto](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub Active-Directory-JavaScript-singlepageapp-dotnet-WebApi-v2 Sample") ukázkovou aplikaci v GitHubu. Ukázka používá webové rozhraní API ASP.NET pro ověření tokenu.

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Po počátečním přihlášení nechcete požádat uživatele o opětovné ověření pokaždé, když potřebují požádat o token pro přístup k prostředku. *AcquireTokenSilent* by proto mělo být použito většinou v čase k získání tokenů. Existují však situace, kdy potřebujete vynutit, aby uživatelé mohli pracovat s koncovým bodem Microsoft Identity Platform. Mezi příklady patří:

- Uživatelé musí znovu zadat své přihlašovací údaje, protože vypršela platnost hesla.
- Vaše aplikace požaduje přístup k prostředku a potřebujete souhlas uživatele.
- Je vyžadováno dvojúrovňové ověřování.

Volání *acquireTokenPopup* otevře automaticky otevírané okno (nebo *acquireTokenRedirect* uživatele přesměruje na koncový bod Microsoft Identity Platform). V tomto okně musí uživatelé pracovat pomocí potvrzení svých přihlašovacích údajů, udělení souhlasu k požadovanému prostředku nebo dokončením dvojúrovňového ověřování.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`acquireTokenSilent`Metoda zpracovává získání a obnovení tokenu bez zásahu uživatele. Po `loginPopup` `loginRedirect` prvním spuštění (nebo) `acquireTokenSilent` je metoda, která se běžně používá k získání tokenů používaných pro přístup k chráněným prostředkům pro následná volání. (Volání požadavků na požadavky nebo obnovení tokenů se provádí tiše.) `acquireTokenSilent` v některých případech může selhat. Může například dojít k vypršení platnosti hesla uživatele. Vaše aplikace může tuto výjimku zpracovat dvěma způsoby:

1. Zavolejte `acquireTokenPopup` okamžitě, čímž se spustí výzva k přihlášení uživatele. Tento model se běžně používá v online aplikacích, kde není k dispozici žádný neautorizovaný obsah v aplikaci pro uživatele. Ukázka vygenerovaná tímto procesem instalace používá tento model.

1. Aplikace mohou také uživateli vytvořit vizuální indikaci, že je vyžadováno interaktivní přihlášení, takže uživatel může vybrat správný čas pro přihlášení nebo může aplikace opakovat `acquireTokenSilent` později. To se běžně používá, když uživatel může použít jiné funkce aplikace bez přerušení. V aplikaci může být například dostupný neautorizovaný obsah. V takovém případě se uživatel může rozhodnout, kdy se chce přihlásit k přístupu k chráněnému prostředku, nebo aktualizovat zastaralé informace.

> [!NOTE]
> V tomto rychlém startu se `loginPopup` `acquireTokenPopup` ve výchozím nastavení používají metody a. Pokud jako prohlížeč používáte Internet Explorer, doporučuje se použít metody a, protože se jedná o `loginRedirect` `acquireTokenRedirect` [známý problém](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) související s tím, jak Internet Explorer zpracovává automaticky otevíraná okna. Pokud chcete zjistit, jak dosáhnout stejného výsledku pomocí `Redirect methods` , [Přečtěte si téma](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Volání rozhraní API Microsoft Graph pomocí tokenu, který jste právě získali

1. Nejprve vytvořte soubor. js s názvem `graphConfig.js` , který bude ukládat koncové body REST. Přidejte následující kód:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Kde:
   - *\<Enter_the_Graph_Endpoint_Here>* je instance MS Graph API. V případě globálního koncového bodu služby MS Graph API jednoduše nahraďte tento řetězec řetězcem `https://graph.microsoft.com` . Pro národní cloudová nasazení si prosím přečtěte [Graph API dokumentaci](/graph/deployments).

1. Dále vytvořte soubor. js s názvem `graph.js` , který provede volání REST pro Microsoft Graph rozhraní API a přidejte následující kód:

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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Další informace o tom, jak provést volání REST proti chráněnému rozhraní API

V ukázkové aplikaci vytvořené touto příručkou se `callMSGraph()` Metoda používá k vytvoření `GET` požadavku HTTP proti chráněnému prostředku, který vyžaduje token. Požadavek potom vrátí obsah volajícímu. Tato metoda přidá získaný token v *autorizační hlavičce protokolu HTTP*. Pro ukázkovou aplikaci vytvořenou touto příručkou je prostředkem koncový bod Microsoft Graph API *já* , který zobrazuje informace o profilu uživatele.

## <a name="test-your-code"></a>Testování kódu

1. Nakonfigurujte server tak, aby naslouchal portu TCP, který je založený na umístění vašeho souboru *index.html* . V případě Node.js spusťte webový server pro naslouchání na portu spuštěním následujících příkazů na příkazovém řádku ve složce aplikace:

   ```bash
   npm install
   npm start
   ```
1. V prohlížeči zadejte **http://localhost:3000** nebo **http://localhost:{port}** , kde *port* je port, na který webový server naslouchá. Měl by se zobrazit obsah souboru *index.html* a **přihlašovací** tlačítko.

Poté, co prohlížeč načte soubor *index.html* , vyberte možnost **Přihlásit** se. Budete vyzváni k přihlášení pomocí koncového bodu Microsoft Identity Platform:

![Přihlašovací okno účtu v jazyce JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Poskytnutí souhlasu pro přístup k aplikaci

Při prvním přihlášení do aplikace budete vyzváni, abyste udělili přístup k vašemu profilu a přihlásili se k nim:

![Okno požadovaná oprávnění](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Zobrazit výsledky aplikace

Po přihlášení se v odpovědi rozhraní API Microsoft Graph zobrazí informace o vašem profilu uživatele:

![Výsledky z volání rozhraní API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje, aby *uživatel. přečetl* obor pro čtení profilu uživatele. Ve výchozím nastavení se tento obor automaticky přidá do každé aplikace, která je zaregistrovaná na portálu pro registraci. Jiná rozhraní API pro Microsoft Graph a také vlastní rozhraní API pro back-end Server můžou vyžadovat další obory. Například rozhraní Microsoft Graph API vyžaduje pro výpis e-mailů uživatele obor *mail. Read* .

> [!NOTE]
> Uživatel může být vyzván k dalšímu souhlasu při zvýšení počtu oborů.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

V naší sadě scénářů s více částmi se dostanete hlouběji do vývoje jednostránkové aplikace (SPA) na platformě Microsoft identity.

> [!div class="nextstepaction"]
> [Scénář: jednostránkové aplikace](scenario-spa-overview.md)
