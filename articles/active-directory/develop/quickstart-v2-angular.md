---
title: 'Rychlý Start: přihlášení uživatelů v úhlových aplikacích s jednou stránkou – Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak může úhlová aplikace volat rozhraní API, které vyžaduje přístupové tokeny vydané platformou Microsoft identity.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: d53ce97c4af302801098d9abaa633ced98c93f3a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814023"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Rychlý Start: přihlášení uživatelů a získání přístupového tokenu v úhlové aplikaci s jednou stránkou

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která demonstruje, jak se může aplikace s úhlovou stránkou (SPA) přihlašovat uživatelům a volat Microsoft Graph. Ukázka kódu ukazuje, jak získat přístupový token pro volání rozhraní Microsoft Graph API nebo jakéhokoli webového rozhraní API.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) pro úpravu souborů projektu nebo sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) pro spuštění projektu.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-the-quickstart-app"></a>Registrace a stažení aplikace pro rychlý Start
>
> Chcete-li spustit aplikaci pro rychlý Start, použijte kteroukoli z následujících možností.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Možnost 1 (Express): registrace a Automatická konfigurace aplikace a stažení ukázky kódu
>
> 1. Přejít k prostředí rychlý Start pro <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal registrace aplikací</a>
> 1. Zadejte název aplikace a pak vyberte **Registrovat**.
> 1. V podokně rychlé spuštění Najděte úhlový rychlý Start. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Možnost 2 (ruční): registrace a ruční konfigurace aplikace a ukázky kódu
>
> #### <a name="step-1-register-the-application"></a>Krok 1: registrace aplikace
>
> 1. Postupujte podle pokynů k [registraci jednostránkové aplikace](./scenario-spa-app-registration.md) v Azure Portal.
> 1. Přidejte novou platformu do podokna **ověřování** registrace vaší aplikace a zaregistrujte identifikátor URI přesměrování: `http://localhost:4200/` .
> 1. V tomto rychlém startu se používá [implicitní tok udělení](v2-oauth2-implicit-grant-flow.md). V části **implicitní udělení a hybridní toky** vyberte **tokeny ID** a **přístupové tokeny**. Tokeny ID a přístupové tokeny jsou povinné, protože tato aplikace podepisuje uživatele v a volá rozhraní API.

> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
>
> Aby ukázka kódu v tomto rychlém startu fungovala, je nutné přidat identifikátor URI přesměrování do `http://localhost:4200/` a povolit **implicitní udělení**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-javascript/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-code-sample"></a>Krok 2: stažení ukázky kódu
>[!div renderon="docs"]
>Chcete-li spustit projekt s webovým serverem pomocí Node.js, naklonujte [ukázkové úložiště](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) nebo [si stáhněte základní soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Otevřete soubory v editoru, jako je například Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Krok 3: Konfigurace aplikace JavaScriptu
>
> Ve složce *Src/App* upravte *App. Module. TS* a nastavte `clientId` `authority` hodnoty a `MsalModule.forRoot` .
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Nahraďte tyto hodnoty:
>
>|Název hodnoty|Description|
>|---------|---------|
>|Enter_the_Application_Id_Here|Na stránce **Přehled** registrace vaší aplikace se jedná o hodnotu ID vaší **aplikace (klienta)** . |
>|Enter_the_Cloud_Instance_Id_Here|Toto je cloudová instance Azure. V případě hlavního nebo globálního cloudu Azure zadejte `https://login.microsoftonline.com` . Pro národní cloudy (například Čína) si přečtěte téma [národní cloudy](./authentication-national-cloud.md).|
>|Enter_the_Tenant_Info_Here| Nastavte na jednu z následujících možností: Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu ID adresáře (tenant) nebo názvem tenanta (například `contoso.microsoft.com` ). Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu hodnotou `organizations` . Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu hodnotou `common` . Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu hodnotou `consumers` . |
>|Enter_the_Redirect_Uri_Here|Nahraďte parametrem `http://localhost:4200` .|
>|cacheLocation  | Volitelné Nastavte úložiště prohlížeče pro stav ověřování. Výchozí formát je `sessionStorage`.   |
>|storeAuthStateInCookie  | Volitelné Identifikujte knihovnu, ve které je uložený stav žádosti o ověření. Tento stav je nutný k ověření toků ověřování v souborech cookie prohlížeče. Tento soubor cookie je nastaven pro Internet Explorer a Microsoft Edge tak, aby vyhovoval těmto dvěma prohlížečům. Další podrobnosti najdete v tématu [známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
>
> Hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

> Další informace o dostupných konfigurovatelných možnostech najdete v tématu [inicializace klientských aplikací](msal-js-initializing-client-applications.md).

> Zdrojový kód knihovny MSAL.js můžete najít v úložišti [AzureAD/Microsoft-Authentication-Library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) na GitHubu.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace.

> [!div renderon="docs"]
>
> Přejděte dolů ve stejném souboru a aktualizujte `graphMeEndpoint` . 
> - Nahraďte řetězec `Enter_the_Graph_Endpoint_Herev1.0/me` řetězcem `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` je koncový bod, na který se bude volat volání rozhraní API. U hlavní (globální) Microsoft Graph služby API zadejte `https://graph.microsoft.com/` (včetně koncového lomítka). Další informace najdete v [dokumentaci](https://docs.microsoft.com/graph/deployments).
>
>
> ```javascript
>      protectedResourceMap: [
>        ['Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']]
>      ],
> ```
>
>

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Krok 4: spuštění projektu

Pokud používáte Node.js:

1. Spusťte Server spuštěním následujících příkazů z adresáře projektu:

   ```console
   npm install
   npm start
   ```

1. Přejděte na `http://localhost:4200/`.
1. Vyberte **Přihlásit** se. Při prvním přihlášení se zobrazí výzva, abyste aplikaci povolili přístup k vašemu profilu a automaticky vás přihlásili.
1. Vyberte **profil** , který chcete volat Microsoft Graph. Informace o profilu uživatele se zobrazí na stránce.

## <a name="how-the-sample-works"></a>Jak ukázka funguje

![Diagram, který ukazuje, jak ukázková aplikace v tomto rychlém startu funguje.](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)

## <a name="next-steps"></a>Další kroky

Naučte se, jak se přihlašovat uživateli a získat tokeny v úhlovém kurzu:

> [!div class="nextstepaction"]
> [Úhlový kurz](./tutorial-v2-angular.md)
