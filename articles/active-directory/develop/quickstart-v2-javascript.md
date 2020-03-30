---
title: Přihlášení uživatelů v jednostránkových aplikacích JavaScriptu | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak může javascriptová aplikace volat rozhraní API, které vyžaduje přístupové tokeny pomocí platformy identit Microsoftu.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.openlocfilehash: 2649d885d31bcd0fef403e7e4eddbc828fd17b93
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050262"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Úvodní příručka: Přihlášení uživatelů a získání přístupového tokenu v javascriptovém spa

V tomto rychlém startu se pomocí ukázky kódu dozvíte, jak může jednostránková aplikace JavaScriptu (SPA) přihlašovat uživatele osobních účtů, pracovních účtů a školních účtů. JavaScript SPA může také získat přístupový token pro volání rozhraní Microsoft Graph API nebo libovolné webové rozhraní API. (Viz [jak ukázka funguje](#how-the-sample-works) pro ilustraci.)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (pro úpravu souborů projektu)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrace a stažení aplikace quickstart
> Chcete-li spustit aplikaci rychlého spuštění, použijte některou z následujících možností.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1 (Express): Zaregistrujte a automaticky nakonfigurujte aplikaci a pak si stáhněte ukázku kódu
>
> 1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet poskytuje přístup k více než jednomu tenantovi, vyberte účet v pravém horním rohu a nastavte relaci portálu na tenanta Azure Active Directory (Azure AD), který chcete použít.
> 1. Přejděte na nové [podokno Registrace aplikací azure.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)
> 1. Zadejte název své aplikace. 
> 1. V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft**.
> 1. Vyberte **Zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2 (ruční): Registrace a ruční konfigurace ukázky aplikace a kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> 1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
>
> 1. Pokud váš účet umožňuje přístup k více než jednomu tenantovi, vyberte svůj účet v pravém horním rohu a pak nastavte relaci portálu na klienta Azure AD, který chcete použít.
> 1. Přejděte na stránku Microsoft identity platformy pro vývojáře [Registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Vyberte **možnost Nová registrace**.
> 1. Když se zobrazí stránka **Zaregistrovat aplikaci**, zadejte název pro vaši aplikaci.
> 1. V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft**.
> 1. Vyberte **Zaregistrovat**. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
> 1. Tento rychlý start vyžaduje [implicitní tok grantu,](v2-oauth2-implicit-grant-flow.md) který má být povolen. V levém podokně registrované aplikace vyberte **možnost Ověřování**.
> 1. V části **Konfigurace platformy**vyberte **Přidat platformu**. Vlevo se otevře panel. Zde vyberte oblast **webových aplikací.**
> 1. Stále vlevo nastavte hodnotu **URI** přesměrování `http://localhost:3000/`na . Potom vyberte **přístupový token** a **token ID**.
> 1. Vyberte **Konfigurovat**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure Portal
> Chcete-li ukázku kódu v tomto rychlém startu `redirectUri` `http://localhost:3000/` pracovat, je třeba přidat as a povolit **implicitní grant**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Proveďte tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-javascript/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-project"></a>Krok 2: Stažení projektu

> [!div renderon="docs"]
> Chcete-li spustit projekt s webovým serverem pomocí souboru Node.js, [stáhněte základní soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal"]
> Spuštění projektu s webovým serverem pomocí souboru Node.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stáhnout ukázku kódu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurace aplikace JavaScript
>
> Ve složce *JavaScriptSPA* upravte *soubor authConfig.js*a nastavte `clientID`a `authority` `redirectUri` hodnoty v části `msalConfig`.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };  
>
>```

> [!div renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Kde:
> - Enter_the_Application_Id_Here>je **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali. * \<*
> - Enter_the_Cloud_Instance_Id_Here>je instancí cloudu Azure. * \<* Pro hlavní nebo globální cloud *https://login.microsoftonline.com*Azure jednoduše zadejte . Národní **national** cloudy (například Čína) viz [Národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - Enter_the_Tenant_info_here>je nastavena na jednu z následujících možností: * \<*
>    - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID klienta** nebo **názvem klienta** (například *contoso.microsoft.com*).
>    - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**.
>    - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu **běžnými**. Chcete-li omezit podporu pouze na *osobní účty Microsoft*, nahraďte tuto hodnotu **spotřebiteli**.
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: Aplikace je nakonfigurovaná a připravená ke spuštění
> Nakonfigurovali jsme váš projekt s hodnotami vlastností vaší aplikace. 

> [!div renderon="docs"]
> 
> Potom, stále ve stejné složce, upravte soubor *graphConfig.js* a nastavte `graphMeEndpoint` `graphMeEndpoint` `apiConfig` objekt a pro.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Kde:
> - Enter_the_Graph_Endpoint_Here>je koncový bod, proti kterému se budou volat rozhraní API. * \<* Hlavní nebo globální službu rozhraní Microsoft `https://graph.microsoft.com`Graph API jednoduše zadejte . Další informace najdete [v tématu Nasazení národního cloudu](https://docs.microsoft.com/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>Krok 4: Spuštění projektu

Spuštění projektu s webovým serverem pomocí [souboru Node.js](https://nodejs.org/en/download/):

1. Chcete-li spustit server, spusťte z adresáře projektu následující příkaz:
    ```batch
    npm install
    npm start
    ```
1. Otevřete webový prohlížeč `http://localhost:3000/`a přejděte na stránku .

1. Chcete-li přihlásit, **vyberte Přihlásit** se a pak zavolejte rozhraní Microsoft Graph API.

Po načtení aplikace prohlížečem vyberte **Přihlásit se**. Při prvním přihlášení budete vyzváni k poskytnutí souhlasu s tím, aby aplikace mohla získat přístup k vašemu profilu a přihlásit se k němu. Po úspěšném přihlášení by se měly na stránce zobrazit informace o vašem profilu uživatele.

## <a name="more-information"></a>Další informace

### <a name="how-the-sample-works"></a>Jak ukázka funguje

![Jak ukázková aplikace v tomto rychlém startu funguje](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

Knihovna MSAL se přihlásí k uživatelům a požaduje tokeny, které se používají pro přístup k rozhraní API, které je chráněno platformou identit microsoftu. Soubor *index.html* rychlého startu obsahuje odkaz na knihovnu:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Předchozí verzi můžete nahradit nejnovější verzí v části [Verze MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Případně, pokud máte nainstalovaný soubor Node.js, můžete stáhnout nejnovější verzi prostřednictvím správce balíčků Node.js (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Kód rychlého startu také ukazuje, jak inicializovat knihovnu MSAL:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };  

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Kde  |  |
> |---------|---------|
> |`clientId`     | ID aplikace, která je registrovaná na webu Azure Portal.|
> |`authority`    | (Nepovinné) Adresa URL autority, která podporuje typy účtů, jak je popsáno výše v části konfigurace. Výchozí autorita `https://login.microsoftonline.com/common`je . |
> |`redirectUri`     | Registrace aplikace je nakonfigurován reply/redirectUri. V tomto `http://localhost:3000/`případě. |
> |`cacheLocation`  | (Nepovinné) Nastaví úložiště prohlížeče pro stav auth. Výchozí je sessionStorage.   |
> |`storeAuthStateInCookie`  | (Nepovinné) Knihovna, která ukládá stav požadavku na ověření, který je vyžadován pro ověření toků ověřování v souborech cookie prohlížeče. Tento soubor cookie je nastaven pro prohlížeče IE a Edge, aby zmírnil některé [známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Další informace o dostupných konfigurovatelných možnostech naleznete [v tématu Inicializaci klientských aplikací](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Přihlášení uživatelů

Následující fragment kódu ukazuje, jak se přihlásit uživatele:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Kde  |  |
> |---------|---------|
> | `scopes`   | (Nepovinné) Obsahuje obory, které jsou požadovány pro souhlas uživatele v době přihlášení. Například `[ "user.read" ]` pro Microsoft `[ "<Application ID URL>/scope" ]` Graph nebo pro vlastní webová rozhraní API (to `api://<Application ID>/access_as_user`znamená). |

> [!TIP]
> Případně můžete použít metodu `loginRedirect` k přesměrování aktuální stránky na přihlašovací stránku namísto vyskakovacího okna.

### <a name="request-tokens"></a>Požadavek na tokeny

MSAL používá tři metody k `acquireTokenRedirect` `acquireTokenPopup`získání žetonů: , a`acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Metoda `acquireTokenSilent` zpracovává token akvizice a obnovení bez jakékoli interakce uživatele. Po `loginRedirect` spuštění `loginPopup` metody or je `acquireTokenSilent` metoda běžně používaná k získání tokenů, které se používají pro přístup k chráněným prostředkům pro následná volání. Volání požadavku nebo obnovení tokenů jsou prováděny tiše.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Kde  |  |
> |---------|---------|
> | `scopes`   | Obsahuje obory, které mají být vráceny v přístupovém tokenu pro rozhraní API. Například `[ "mail.read" ]` pro Microsoft `[ "<Application ID URL>/scope" ]` Graph nebo pro vlastní webová rozhraní API (to `api://<Application ID>/access_as_user`znamená).|

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Existují situace, kdy je třeba vynutit uživatelům interakci s koncovým bodem platformy identit microsoftu. Například:
* Uživatelé mohou potřebovat znovu zadat svá pověření, protože jejich heslo vypršelo.
* Vaše aplikace požaduje přístup k dalším oborům prostředků, se kterými musí uživatel souhlasit.
* Je vyžadováno dvoufaktorové ověřování.

Obvyklý doporučený vzor pro většinu `acquireTokenSilent` aplikací je nejprve volat, `acquireTokenPopup` pak `acquireTokenRedirect`zachytit výjimku a potom volání (nebo ) spustit interaktivní požadavek.

Volání `acquireTokenPopup` výsledků v vyskakovacím okně pro přihlášení. (Nebo `acquireTokenRedirect` má za následek přesměrování uživatelů na koncový bod platformy identit microsoftu.) V tomto okně uživatelé potřebují komunikovat potvrzením svých pověření, udělením souhlasu s požadovaným prostředkem nebo dokončením dvoufaktorového ověřování.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> Tento rychlý start `loginRedirect` `acquireTokenRedirect` používá metody a s aplikací Microsoft Internet Explorer z důvodu [známého problému](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) souvisejícího se zpracováním automaticky otevíraných oken aplikací Internet Explorer.

## <a name="next-steps"></a>Další kroky

Podrobnější podrobný návod k vytváření aplikace pro tento rychlý start naleznete v následujících tématech:

> [!div class="nextstepaction"]
> [Kurz pro přihlášení a volání MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Chcete-li procházet repo nástroje MSAL pro dokumentaci, nejčastější dotazy, problémy a další informace, přečtěte si následující:

> [!div class="nextstepaction"]
> [Úložiště MSAL.js GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Pomozte nám vylepšit platformu identit Microsoftu. Řekněte nám, co si myslíte, že dokončení krátké hod-průzkum u dvou otázek.

> [!div class="nextstepaction"]
> [Průzkum platformy identity Microsoftu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
