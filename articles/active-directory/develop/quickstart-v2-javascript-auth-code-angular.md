---
title: 'Rychlý Start: přihlášení uživatelů v jazyce JavaScript – jednostránkové aplikace (SPA) s kódem ověřování a volání Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak může aplikace s jednoduchou stránkou (SPA) JavaScriptu přihlašovat uživatele osobních účtů, pracovních účtů a školních účtů pomocí toku autorizačního kódu a Microsoft Graph volání.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/14/2021
ms.author: jamesmantu
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 1d5fc097be188c820f9be15594f4bb382754b849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224924"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-an-angular-spa-using-the-auth-code-flow"></a>Rychlý Start: přihlášení a získání přístupového tokenu v úhlovém ověřování pomocí toku kódu ověřování

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak se může aplikace s jednoduchou stránkou (SPA) JavaScriptu přihlašovat uživatelům a volat Microsoft Graph pomocí toku autorizačního kódu. Ukázka kódu ukazuje, jak získat přístupový token pro volání rozhraní Microsoft Graph API nebo jakéhokoli webového rozhraní API. 

Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.

V tomto rychlém startu se k toku autorizačního kódu používá MSAL Úhlov v2. Podobného rychlému startu, který používá MSAL úhlů 1. x s implicitním tokem, najdete v tématu [rychlý Start: přihlášení uživatelů v JavaScriptu jednostránkovéch aplikacích](./quickstart-v2-angular.md).

> [!IMPORTANT]
> MSAL, úhlové v2 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvoření předplatného Azure zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) nebo jiný Editor kódu

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrace a stažení aplikace pro rychlý Start
> Chcete-li spustit aplikaci pro rychlý Start, použijte kteroukoli z následujících možností.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1 (Express): registrace a Automatická konfigurace aplikace a stažení ukázky kódu
>
> 1. Přejít k prostředí rychlý Start pro <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal registrace aplikací</a>
> 1. Zadejte název své aplikace.
> 1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
> 1. Vyberte **Zaregistrovat**.
> 1. Přejděte do podokna pro rychlé zprovoznění a podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2 (ruční): registrace a ruční konfigurace aplikace a ukázky kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> 1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Zadejte **název** vaší aplikace. Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
> 1. Vyberte **Zaregistrovat**. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
> 1. V části **Spravovat** vyberte **ověřování**.
> 1. V části **konfigurace platformy** vyberte **Přidat platformu**. V podokně, které se otevře, vyberte **jednostránkovou aplikaci**.
> 1. Nastavte hodnotu **identifikátorů URI pro přesměrování** na `http://localhost:4200/` . Toto je výchozí port NodeJS, který bude naslouchat na vašem místním počítači. Po úspěšném ověření uživatele vrátíme do tohoto identifikátoru URI odpověď ověřování. 
> 1. Chcete-li změny použít, vyberte možnost **Konfigurovat** .
> 1. V části **konfigurace platformy** rozbalte **jednostránkovou aplikaci**.
> 1. Ověřte, že u **typů grantů** , které ![ jsou už nakonfigurované ](media/quickstart-v2-javascript/green-check.png) , je váš identifikátor URI pro přesměrování způsobilý pro tok autorizačního kódu s PKCE.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu v tomto rychlém startu fungovala, přidejte **identifikátor URI přesměrování** `http://localhost:4200/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-javascript/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

 #### <a name="step-2-download-the-project"></a>Krok 2: Stažení projektu

> [!div renderon="docs"]
> Chcete-li spustit projekt s webovým serverem pomocí Node.js, [Stáhněte si základní soubory projektu](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Spustit projekt s webovým serverem pomocí Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurace aplikace JavaScriptu
>
> Ve složce *Src* otevřete složku *aplikace* a pak otevřete soubor *App. Module. TS* a aktualizujte `clientID` `authority` hodnoty, a `redirectUri` v `auth` objektu.
>
> ```javascript
> // MSAL instance to be passed to msal-angular
> export function MSALInstanceFactory(): IPublicClientApplication {
>   return new PublicClientApplication({
>     auth: {
>       clientId: 'Enter_the_Application_Id_Here',
>       authority: 'Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here',
>       redirectUri: 'Enter_the_Redirect_Uri_Here'
>     },
>     cache: {
>       cacheLocation: BrowserCacheLocation.LocalStorage,
>       storeAuthStateInCookie: isIE, // set to true for IE 11
>     },
>   });
> }
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Upravte hodnoty v `auth` části, jak je popsáno zde:
>
> - `Enter_the_Application_Id_Here` je **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali.
>
>    Pokud chcete zjistit hodnotu **ID aplikace (klienta)**, na stránce s přehledem registrace aplikace se podívejte na stránku Azure Portal s **přehledem** registrace aplikace.
> - `Enter_the_Cloud_Instance_Id_Here` je instancí cloudu Azure. V případě hlavního nebo globálního cloudu Azure zadejte `https://login.microsoftonline.com/` . Pro **národní** cloudy (například Čína) si přečtěte téma [národní cloudy](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` je nastaveno na jednu z následujících možností:
>   - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta**. Například, `contoso.microsoft.com`.
>
>    Pokud chcete zjistit hodnotu **ID adresáře (tenanta)**, na stránce s **přehledem** registrace aplikace se podívejte do Azure Portal.
>   - Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu hodnotou `organizations` .
>   - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu hodnotou `common` . **Pro tento rychlý Start** použijte `common` .
>   - Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu hodnotou `consumers` .
>
>    Pokud chcete najít hodnotu **podporovaných typů účtů**, v Azure Portal na stránce **Přehled** registrace aplikace.
> - `Enter_the_Redirect_Uri_Here` je `http://localhost:4200/`.
>
> `authority`Pokud používáte hlavní cloud Azure (Global), měla by být hodnota ve vaší *aplikaci. Module. TS* podobná následující:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace.

> [!div renderon="docs"]
>
> Přejděte dolů ve stejném souboru a aktualizujte `graphMeEndpoint` . 
> - Nahraďte řetězec `Enter_the_Graph_Endpoint_Herev1.0/me` řetězcem `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` je koncový bod, na který se bude volat volání rozhraní API. U hlavní (globální) Microsoft Graph služby API zadejte `https://graph.microsoft.com/` (včetně koncového lomítka). Další informace najdete v [dokumentaci](/graph/deployments).
>
>
> ```javascript
> export function MSALInterceptorConfigFactory(): MsalInterceptorConfiguration {
>   const protectedResourceMap = new Map<string, Array<string>>();
>   protectedResourceMap.set('Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']);
>
>   return {
>     interactionType: InteractionType.Redirect,
>     protectedResourceMap
>   };
> }
> ```
>
>
 #### <a name="step-4-run-the-project"></a>Krok 4: spuštění projektu

Spusťte projekt s webovým serverem pomocí Node.js:

1. Chcete-li spustit server, spusťte v adresáři projektu následující příkazy:
    ```console
    npm install
    npm start
    ```
1. Přejděte na adresu `http://localhost:4200/`.

1. Výběrem **přihlašovacího jména** spusťte proces přihlášení a potom zavolejte rozhraní Microsoft Graph API.

    Při prvním přihlášení se zobrazí výzva k zadání vašeho souhlasu, který aplikaci umožní přístup k vašemu profilu a bude vás přihlašovat. Po úspěšném přihlášení klikněte na tlačítko **profil** a zobrazte informace o uživateli na stránce.

## <a name="more-information"></a>Další informace

### <a name="how-the-sample-works"></a>Jak ukázka funguje

![Diagram znázorňující tok autorizačního kódu pro jednostránkovou aplikaci.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

Knihovna MSAL.js přihlašuje uživatele a požádá o tokeny, které se používají pro přístup k rozhraní API, které je chráněno platformou Microsoft identity. 

Pokud máte nainstalovanou Node.js, můžete si nejnovější verzi stáhnout pomocí Node.js správce balíčků (npm):

```console
npm install @azure/msal-browser @azure/msal-angular@2
```

## <a name="next-steps"></a>Další kroky

Podrobný průvodce vytvořením aplikace toku kódu ověřování pomocí JavaScriptu pro Vanilla najdete v následujícím kurzu:

> [!div class="nextstepaction"]
> [Kurz pro přihlášení a volání MS graphu](./tutorial-v2-javascript-auth-code.md)