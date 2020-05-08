---
title: Přihlaste se uživatelům v aplikacích JavaScript Single-Page s ověřovacím kódem | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak může aplikace JavaScriptu volat rozhraní API, které vyžaduje přístupové tokeny pomocí platformy Microsoft Identity Platform.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: 9663c11508b0478a67f528cb301d705a3125e4f6
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871526"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Rychlý Start: přihlášení uživatelů a získání přístupového tokenu v ZABEZPEČENÉm kódu v JavaScriptu pomocí toku kódu ověřování 

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před všeobecnou dostupností změnit (GA).


V tomto rychlém startu se používá MSAL. js 2,0 s tokem autorizačního kódu. Chcete-li použít MSAL. js 1,0 s implicitním tokem, zobrazte si [Tento rychlý Start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

V tomto rychlém startu pomocí ukázky kódu zjistíte, jak se jednostránkové aplikace v JavaScriptu (SPA) můžou přihlašovat uživatelům osobních účtů, pracovních účtů a školních účtů. K volání rozhraní API pro Microsoft Graph nebo libovolné webové rozhraní API může získat přístupový token, a to prostřednictvím JavaScriptu. Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvoření předplatného Azure zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (pro úpravy souborů projektu)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrace a stažení aplikace pro rychlý Start
> Chcete-li spustit aplikaci pro rychlý Start, použijte kteroukoli z následujících možností.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1 (Express): registrace a Automatická konfigurace aplikace a stažení ukázky kódu
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
> 1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte účet v pravém horním rohu a nastavte relaci portálu na tenanta Azure Active Directory (Azure AD), který chcete použít.
> 1. Vyberte [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Zadejte název své aplikace.
> 1. V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
> 1. Vyberte **Zaregistrovat**.
> 1. Přejděte do podokna pro rychlé zprovoznění a podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2 (ruční): registrace a ruční konfigurace aplikace a ukázky kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
>
> 1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu a pak nastavte relaci portálu na tenanta Azure AD, kterého chcete použít.
> 1. Vyberte [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Vyberte **Nová registrace**.
> 1. Když se zobrazí stránka **Zaregistrovat aplikaci**, zadejte název pro vaši aplikaci.
> 1. V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
> 1. Vyberte **Zaregistrovat**. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
> 1. V levém podokně registrované aplikace vyberte **ověřování**.
> 1. V části **konfigurace platformy**vyberte **Přidat platformu**. Panel se otevře vlevo. Tam vyberte oblast s **jednou stránkou aplikací** .
> 1. Pořád na levé straně nastavte hodnotu **identifikátoru URI přesměrování** na `http://localhost:3000/`. 
> 1. Vyberte **Konfigurovat**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Chcete-li, aby ukázka kódu v tomto rychlém startu fungovala, `redirectUri` je `http://localhost:3000/`nutné přidat jako.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-javascript/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-project"></a>Krok 2: Stažení projektu

> [!div renderon="docs"]
> Chcete-li spustit projekt s webovým serverem pomocí Node. js, [Stáhněte si základní soubory projektu](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Spustit projekt s webovým serverem pomocí Node. js

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurace aplikace JavaScriptu
>
> Ve složce *aplikace* upravte *authConfig. js*a nastavte `clientID`hodnoty `authority` a `redirectUri` v části. `msalConfig`
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

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > :::no-loc text="Enter_the_Supported_Account_Info_Here":::

> [!div renderon="docs"]
>
> Kde:
> - Enter_the_Application_Id_Here>je **ID aplikace (klienta)** pro zaregistrovanou aplikaci. * \<*
> - Enter_the_Cloud_Instance_Id_Here>je instance cloudu Azure. * \<* V případě hlavního nebo globálního cloudu Azure stačí zadat *https://login.microsoftonline.com/*. Pro **národní** cloudy (například Čína) si přečtěte téma [národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - Enter_the_Tenant_info_here>je nastavená na jednu z následujících možností: * \<*
>    - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například *contoso.Microsoft.com*).
>    - Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**.
>    - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu **běžnými**. Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu **příjemci**.
> - * \<Enter_the_Redirect_Uri_Here>*`http://localhost:3000`
> > [!TIP]
> > Pokud chcete najít hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **podporované typy účtů**, na stránce s **přehledem** registrace aplikace se podívejte do Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace.

> [!div renderon="docs"]
>
> Potom stále ve stejné složce upravte soubor *graphConfig. js* pro nastavení `graphMeEndpoint` a `graphMailEndpoint` pro `apiConfig` objekt.
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
> Enter_the_Graph_Endpoint_Here>je koncový bod, proti kterému se volání rozhraní API budou provádět. * \<* Pro Main nebo Global Microsoft Graph API Service zadejte `https://graph.microsoft.com`. Další informace najdete v tématu věnovaném [národním cloudovým nasazením](https://docs.microsoft.com/graph/deployments).
>
> #### <a name="step-4-run-the-project"></a>Krok 4: spuštění projektu

Spusťte projekt s webovým serverem pomocí [Node. js](https://nodejs.org/en/download/):

1. Chcete-li spustit server, spusťte v adresáři projektu následující příkazy:
    ```bash
    npm install
    npm start
    ```
1. Přejděte na `http://localhost:3000/`.

1. Vyberte **Přihlásit** se a spusťte proces přihlášení a potom zavolejte Microsoft Graph API.

    Při prvním přihlášení se zobrazí výzva k zadání vašeho souhlasu, který aplikaci umožní přístup k vašemu profilu a bude vás přihlašovat. Po úspěšném přihlášení by se na stránce měly zobrazit informace o vašem profilu uživatele.

## <a name="more-information"></a>Další informace

### <a name="how-the-sample-works"></a>Jak ukázka funguje

![Jak ukázka ZABEZPEČENÉho kódu JavaScript funguje: 1. SPA inicializuje přihlášení. 2. SPA získá token ID z platformy Microsoft identity. 3. Zabezpečené ověřování hesla volá token získání. 4. Platforma Microsoft Identity vrací přístupový token do zabezpečeného hesla. 5. Pomocí přístupového tokenu k rozhraní Microsoft Graph API vytvoří protokol SPA požadavek a HTTP GET. 6. Graph API vrátí odpověď protokolu HTTP do zabezpečeného hesla.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal. js

Knihovna MSAL. js podepisuje uživatele a žádá o tokeny, které se používají pro přístup k rozhraní API, které je chráněno platformou Microsoft identity. Soubor *index. html* v ukázce obsahuje odkaz na knihovnu:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```
> [!TIP]
> Předchozí verzi můžete nahradit nejnovější vydanou verzí ve [verzích MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Případně, pokud máte nainstalovanou aplikaci Node. js, můžete si stáhnout nejnovější verzi pomocí Správce balíčků Node. js (npm):

```batch
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Další kroky

[Úložiště GitHub MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js) obsahuje další dokumentaci ke knihovně, nejčastější dotazy a poskytuje podporu pro vydání.

Podrobnější návod k sestavování aplikace pro tento rychlý Start najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Kurz pro přihlášení a volání MS graphu](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
