---
title: Kurz jednoduché jednostránkové aplikace v JavaScriptu – Microsoft Identity Platform | Azure
description: Jak aplikace JavaScript SPA můžou volat rozhraní API, které vyžaduje přístupové tokeny pomocí koncového bodu Azure Active Directory v 2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 51a456a05e1d41057e80b92e44f997e1b52cb132
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129112"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Přihlaste se uživatelům a zavolejte Microsoft Graph API z jednostránkové aplikace v JavaScriptu (SPA).

Tato příručka ukazuje, jak může jednostránková aplikace v JavaScriptu (SPA):
- Přihlaste se k osobním účtům a pracovním a školním účtům 
- Získání přístupového tokenu
- Volání rozhraní API Microsoft Graph nebo jiných rozhraní API, která vyžadují přístupové tokeny z *koncového bodu Microsoft Identity Platform*

>[!NOTE]
> Pokud začínáte s platformou Microsoft identity, doporučujeme začít s [přihlašováním uživatelů a získat přístupový token v rychlém startu pro JavaScript Spa](quickstart-v2-javascript.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Ukázková aplikace vytvořená touto příručkou umožňuje, aby se v prostředí JavaScript SPA dotazoval rozhraní API pro Microsoft Graph nebo webové rozhraní API, které přijímá tokeny z koncového bodu Microsoft Identity Platform. V tomto scénáři se po přihlášení uživatele vyžádá přístupový token a přidá se do požadavků HTTP prostřednictvím autorizační hlavičky. Získání a obnovení tokenu jsou zpracovávány pomocí knihovny Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovnu:

|Knihovna|Popis|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library pro JavaScript Preview|

> [!NOTE]
> *Msal. js* cílí na koncový bod Microsoft Identity Platform, který umožňuje osobním účtům a školním a pracovním účtům přihlašovat a získávat tokeny. Koncový bod platformy Microsoft identity má [určitá omezení](../azuread-dev/azure-ad-endpoint-comparison.md#limitations).
> Rozdíly mezi koncovými body v 1.0 a v 2.0 najdete v [Průvodci porovnáním koncových](../azuread-dev/azure-ad-endpoint-comparison.md)bodů.

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Nastavení webového serveru nebo projektu

> Chcete místo toho stáhnout tento projekt ukázky? Proveďte jednu z následujících akcí:
> 
> - Chcete-li spustit projekt pomocí místního webového serveru, jako je například Node. js, [Stáhněte soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - Volitelné Chcete-li spustit projekt pomocí serveru Microsoft Internetová informační služba (IIS), [Stáhněte projekt sady Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Chcete-li před spuštěním nakonfigurovat ukázku kódu, přejděte k [kroku konfigurace](#register-your-application).

## <a name="prerequisites"></a>Předpoklady

* Pro spuštění tohoto kurzu potřebujete místní webový server, jako je například [Node. js](https://nodejs.org/en/download/), [.net Core](https://www.microsoft.com/net/core)nebo IIS Express integrace se sadou [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Pokud používáte Node. js ke spuštění projektu, nainstalujte integrované vývojové prostředí (IDE), například [Visual Studio Code](https://code.visualstudio.com/download), pro úpravu souborů projektu.

* Pokyny v této příručce jsou založené na Node. js i v aplikaci Visual Studio 2017, ale můžete použít jakékoli jiné vývojové prostředí nebo webový server.

## <a name="create-your-project"></a>Vytvořit projekt

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Možnost 1: Node. js nebo jiné webové servery
> Ujistěte se, že máte [Node. js](https://nodejs.org/en/download/) nainstalovaný, a pak vytvořte složku pro hostování aplikace.
>
> ### <a name="option-2-visual-studio"></a>Možnost 2: Visual Studio
> Pokud používáte aplikaci Visual Studio a vytváříte nový projekt, postupujte podle následujících kroků:
> 1. V aplikaci Visual Studio vyberte **soubor** > **Nový** > **projekt**.
> 1. V části **Visual C#\Web** vyberte **Webová aplikace ASP.NET (.NET Framework)** .
> 1. Zadejte název vaší aplikace a pak vyberte **OK**.
> 1. V části **Nová webová aplikace ASP.NET**vyberte **prázdné**.

## <a name="create-the-spa-ui"></a>Vytvoření uživatelského rozhraní SPA
1. Vytvořte soubor *index. html* pro váš JavaScript Spa. Pokud používáte aplikaci Visual Studio, vyberte projekt (kořenová složka projektu). Klikněte pravým tlačítkem myši a vyberte možnost **přidat** > **novou položku** > **stránky HTML**a pojmenujte soubor *index. html*.

1. V souboru *index. html* přidejte následující kód:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Verzi MSAL. js můžete v předchozím skriptu nahradit nejnovější vydanou verzí ve [verzích MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Přihlášení uživatele pomocí knihovny Microsoft Authentication Library (MSAL)

Do `index.html` souboru přidejte následující kód v rámci značek `<script></script>`:

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
       },
       cache: {
           cacheLocation: "localStorage",
           storeAuthStateInCookie: true
       }
   };

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Jakmile uživatel poprvé vybere tlačítko pro **přihlášení** , metoda `signIn` zavolá `loginPopup`, aby se přihlásili uživateli. Tato metoda otevře automaticky otevírané okno s *koncovým bodem Microsoft Identity Platform* , kde se zobrazí výzva a ověří přihlašovací údaje uživatele. Po úspěšném přihlášení se uživatel přesměruje zpátky na původní stránku *index. html* . Byl přijat, zpracován pomocí `msal.js`a informace obsažené v tokenu jsou uloženy v mezipaměti. Tento token je známý jako *token ID* a obsahuje základní informace o uživateli, jako je například zobrazované jméno uživatele. Pokud plánujete použít data poskytnutá tímto tokenem pro jakékoli účely, musíte se ujistit, že váš back-end Server ověří tento token, aby bylo zaručeno, že token byl vydán pro platného uživatele pro vaši aplikaci.

Zabezpečené ověřování hesla vygenerované touto příručkou volá `acquireTokenSilent` a/nebo `acquireTokenPopup` k získání *přístupového tokenu* , který se používá k dotazování rozhraní API Microsoft Graph pro informace o profilu uživatele. Pokud potřebujete ukázku, která ověří token ID, podívejte se na [tuto](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub Active-Directory-JavaScript-singlepageapp-dotnet-WebApi-v2 Sample") ukázkovou aplikaci v GitHubu. Ukázka používá webové rozhraní API ASP.NET pro ověření tokenu.

#### <a name="getting-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Po počátečním přihlášení nechcete požádat uživatele o opětovné ověření pokaždé, když potřebují požádat o token pro přístup k prostředku. *AcquireTokenSilent* by proto mělo být použito většinou v čase k získání tokenů. Existují však situace, kdy potřebujete vynutit, aby uživatelé mohli pracovat s koncovým bodem Microsoft Identity Platform. Příklady obsahují:

- Uživatelé musí znovu zadat své přihlašovací údaje, protože vypršela platnost hesla.
- Vaše aplikace požaduje přístup k prostředku a potřebujete souhlas uživatele.
- Je vyžadováno dvojúrovňové ověřování.

Volání *acquireTokenPopup* otevře automaticky otevírané okno (nebo *acquireTokenRedirect* uživatele přesměruje na koncový bod Microsoft Identity Platform). V tomto okně musí uživatelé pracovat pomocí potvrzení svých přihlašovacích údajů, udělení souhlasu k požadovanému prostředku nebo dokončením dvojúrovňového ověřování.

#### <a name="getting-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Metoda `acquireTokenSilent` zpracovává získání a obnovení tokenu bez zásahu uživatele. Po prvním spuštění `loginPopup` (nebo `loginRedirect`) se `acquireTokenSilent` jedná o metodu, která se běžně používá k získání tokenů používaných pro přístup k chráněným prostředkům pro následná volání. (Volání požadavků na požadavky nebo obnovení tokenů se provádí tiše.) v některých případech může `acquireTokenSilent` selhat. Může například dojít k vypršení platnosti hesla uživatele. Vaše aplikace může tuto výjimku zpracovat dvěma způsoby:

1. Zavolejte `acquireTokenPopup` okamžitě, čímž se spustí výzva k přihlášení uživatele. Tento model se běžně používá v online aplikacích, kde není k dispozici žádný neautorizovaný obsah v aplikaci pro uživatele. Ukázka vygenerovaná tímto procesem instalace používá tento model.

2. Aplikace také mohou uživateli vizuálně vyznačit, že je vyžadováno interaktivní přihlášení, takže uživatel může vybrat správný čas pro přihlášení nebo může aplikace opakovat `acquireTokenSilent` později. To se běžně používá, když uživatel může použít jiné funkce aplikace bez přerušení. V aplikaci může být například dostupný neautorizovaný obsah. V takovém případě se uživatel může rozhodnout, kdy se chce přihlásit k přístupu k chráněnému prostředku, nebo aktualizovat zastaralé informace.

> [!NOTE]
> V tomto rychlém startu se používají metody `loginRedirect` a `acquireTokenRedirect`, když se používá prohlížeč Internet Explorer. Tento postup se řídí z důvodu [známého problému](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) , který se týká způsobu, jakým aplikace Internet Explorer zpracovává automaticky otevíraná okna.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Volání rozhraní API Microsoft Graph pomocí tokenu, který jste právě získali

Do `index.html` souboru přidejte následující kód v rámci značek `<script></script>`:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Další informace o tom, jak provést volání REST proti chráněnému rozhraní API

V ukázkové aplikaci vytvořené touto příručkou se používá metoda `callMSGraph()` k vytvoření žádosti HTTP `GET` proti chráněnému prostředku, který vyžaduje token. Požadavek potom vrátí obsah volajícímu. Tato metoda přidá získaný token v *autorizační hlavičce protokolu HTTP*. Pro ukázkovou aplikaci vytvořenou touto příručkou je prostředkem koncový bod Microsoft Graph API *já* , který zobrazuje informace o profilu uživatele.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Přidejte metodu pro odhlášení uživatele.

Do `index.html` souboru přidejte následující kód v rámci značek `<script></script>`:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Registrace vaší aplikace

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte účet v pravém horním rohu a pak nastavte relaci portálu na klienta služby Azure AD, kterého chcete použít.
1. Přejít na stránku Microsoft Identity Platform for Developers [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Po zobrazení stránky **Registrovat aplikaci** zadejte název vaší aplikace.
1. V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
1. V části **identifikátor URI pro přesměrování** vyberte v rozevíracím seznamu **webovou** platformu a pak nastavte tuto hodnotu na adresu URL aplikace, která je založená na vašem webovém serveru.

   Informace o tom, jak nastavit a získat adresu URL pro přesměrování pro Node. js a Visual Studio, naleznete v části "nastavení adresy URL pro přesměrování pro Node. js" a [Nastavení adresy URL pro přesměrování pro Visual Studio](#set-a-redirect-url-for-visual-studio).

1. Vyberte **Zaregistrovat**.
1. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
1. Tento rychlý Start vyžaduje, aby byl povolený [tok implicitního udělení](v2-oauth2-implicit-grant-flow.md) . V levém podokně registrované aplikace vyberte **ověřování**.
1. V části **Upřesnit nastavení**v části **implicitní udělení**vyberte zaškrtávací políčka **tokeny ID** a **přístupové tokeny** . Tokeny ID a přístupové tokeny jsou povinné, protože tato aplikace musí přihlašovat uživatele a volat rozhraní API.
1. Vyberte **Save** (Uložit).

> #### <a name="set-a-redirect-url-for-nodejs"></a>Nastavení adresy URL pro přesměrování pro Node. js
> Pro Node. js můžete nastavit port webového serveru v souboru *Server. js* . V tomto kurzu se používá port 30662, ale můžete použít jakýkoli jiný dostupný port.
>
> Chcete-li nastavit adresu URL pro přesměrování v informacích o registraci aplikace, přepněte zpět do podokna **Registrace aplikace** a proveďte jednu z následujících akcí:
>
> - Jako **adresu URL pro přesměrování**nastavte *`http://localhost:30662/`* .
> - Pokud používáte vlastní port TCP, použijte *`http://localhost:<port>/`* (kde *\<portu >* je vlastní číslo portu TCP).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Nastavení adresy URL pro přesměrování pro Visual Studio
> Chcete-li získat adresu URL pro přesměrování pro aplikaci Visual Studio, postupujte podle následujících kroků:
> 1. V Průzkumníku řešení vyberte projekt.
>
>    Otevře se okno **vlastnosti** . Pokud tomu tak není, stiskněte F4.
>
>    ![okno Vlastnosti projektu JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Zkopírujte hodnotu **URL** .
> 1. Přepněte zpět do podokna **Registrace aplikace** a vložte zkopírovanou hodnotu jako **adresu URL pro přesměrování**.

#### <a name="configure-your-javascript-spa"></a>Konfigurace vašeho JavaScriptu SPA

1. V souboru *index. html* , který jste vytvořili během nastavení projektu, přidejte informace o registraci aplikace. V horní části souboru do značek `<script></script>` přidejte následující kód:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Kde:
    - *\<Enter_the_Application_Id_here >* je **ID aplikace (klienta)** pro zaregistrovanou aplikaci.
    - *\<Enter_the_Tenant_info_here >* je nastavena na jednu z následujících možností:
       - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například *contoso.Microsoft.com*).
       - Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**.
       - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu **běžnými**. Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu **příjemci**.

## <a name="test-your-code"></a>Testování kódu

Otestujte kód pomocí některého z následujících prostředí.

### <a name="test-with-nodejs"></a>Testování pomocí Node. js

Pokud nepoužíváte aplikaci Visual Studio, ujistěte se, že je váš webový server spuštěný.

1. Nakonfigurujte server tak, aby naslouchal portu TCP, který je založený na umístění souboru *index. html* . Pro Node. js spusťte webový server, aby naslouchal na portu spuštěním následujících příkazů na příkazovém řádku ve složce aplikace:

    ```bash
    npm install
    node server.js
    ```
1. V prohlížeči zadejte **http://\<rozsah >\</span > localhost: 30662** nebo **http://\<span >\</span > localhost: {port}** , kde *port* je port, na který webový server naslouchá. Měl by se zobrazit obsah souboru *index. html* a **přihlašovací** tlačítko.

### <a name="test-with-visual-studio"></a>Testování pomocí sady Visual Studio

Pokud používáte aplikaci Visual Studio, vyberte řešení projektu a potom stiskněte klávesu F5 ke spuštění projektu. V prohlížeči se otevře umístění http://<span></span>localhost: {port} a **přihlašovací** tlačítko by mělo být viditelné.

## <a name="test-your-application"></a>Testování aplikace

Poté, co prohlížeč načte soubor *index. html* , vyberte možnost **Přihlásit**se. Budete vyzváni k přihlášení pomocí koncového bodu Microsoft Identity Platform:

![Přihlašovací okno účtu v jazyce JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Poskytnutí souhlasu pro přístup k aplikaci

Při prvním přihlášení do aplikace budete vyzváni, abyste udělili přístup k vašemu profilu a přihlásili se k nim:

![Okno požadovaná oprávnění](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Zobrazit výsledky aplikace

Po přihlášení se v odpovědi rozhraní API Microsoft Graph zobrazí informace o vašem profilu uživatele:

![Výsledky z volání rozhraní API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje, aby *uživatel. přečetl* obor pro čtení profilu uživatele. Ve výchozím nastavení se tento obor automaticky přidá do každé aplikace, která je zaregistrovaná na portálu pro registraci. Jiná rozhraní API pro Microsoft Graph a také vlastní rozhraní API pro back-end Server můžou vyžadovat další obory. Například rozhraní Microsoft Graph API vyžaduje kalendáře. Pokud chcete zobrazit seznam kalendářů uživatelů, je nutné obor *číst.*

Chcete-li získat přístup k kalendářům uživatele v kontextu aplikace, přidejte *kalendáře. Přečtěte si* delegované oprávnění k informacím o registraci aplikace. Pak přidejte *calendars.* scope pro `acquireTokenSilent` volání.

>[!NOTE]
>Uživatel může být vyzván k dalšímu souhlasu při zvýšení počtu oborů.

Pokud rozhraní API back-endu nevyžaduje obor (nedoporučuje se), můžete použít *ClientID* jako obor v voláních pro získání tokenů.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
