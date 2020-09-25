---
title: Přihlášení uživatelů v úhlových aplikacích s jednou stránkou – Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak může úhlová aplikace volat rozhraní API, které vyžaduje přístupové tokeny pomocí platformy Microsoft identity.
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
ms.openlocfilehash: 23b25340906ddb4501492b048740f590c13654c4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258026"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Rychlý Start: přihlášení uživatelů a získání přístupového tokenu v úhlové aplikaci s jednou stránkou

V tomto rychlém startu pomocí ukázky kódu zjistíte, jak se v úhlové aplikaci (SPA) může přihlašovat uživatel, který má osobní účty Microsoft, pracovní účty nebo školní účty. K volání rozhraní API Microsoft Graph nebo jakéhokoli webového rozhraní API může získat přístupový token.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) pro úpravu souborů projektu nebo sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) pro spuštění projektu.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Registrace a stažení aplikace pro rychlý Start
> Chcete-li spustit aplikaci pro rychlý Start, použijte kteroukoli z následujících možností.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Možnost 1 (Express): registrace a Automatická konfigurace aplikace a stažení ukázky kódu
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
> 1. Pokud má váš účet přístup k více než jednomu klientovi, vyberte účet v pravém horním rohu a pak nastavte relaci portálu na tenanta Azure Active Directory (Azure AD), který chcete použít.
> 1. Otevřete nové podokno [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) v Azure Portal.
> 1. Zadejte název aplikace a pak vyberte **Registrovat**.
> 1. Přejít do podokna pro rychlý Start a zobrazit úhlový rychlý Start. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Možnost 2 (ruční): registrace a ruční konfigurace aplikace a ukázky kódu
>
> #### <a name="step-1-register-the-application"></a>Krok 1: registrace aplikace
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
> 1. Pokud má váš účet přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na klienta služby Azure AD, kterého chcete použít.
> 1. Postupujte podle pokynů k [registraci jednostránkové aplikace](./scenario-spa-app-registration.md) v Azure Portal.
> 1. Přidejte novou platformu do podokna **ověřování** registrace vaší aplikace a zaregistrujte identifikátor URI přesměrování: `http://localhost:4200/` .
> 1. V tomto rychlém startu se používá [implicitní tok udělení](v2-oauth2-implicit-grant-flow.md). Vyberte nastavení **implicitního udělení** pro **tokeny ID** a **přístupové tokeny**. Tokeny ID a přístupové tokeny jsou povinné, protože se tato aplikace přihlásí uživatelům a volá rozhraní API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu pro tento rychlý Start fungovala, je nutné přidat identifikátor URI přesměrování jako **http://localhost:4200/** a povolit **implicitní udělení**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-javascript/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-code-sample"></a>Krok 2: stažení ukázky kódu
>[!div renderon="docs"]
>Chcete-li spustit projekt s webovým serverem pomocí Node.js, [naklonujte ukázkové úložiště](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) nebo [si stáhněte základní soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Otevřete soubory pomocí editoru, jako je například Visual Studio Code.

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
>|Název hodnoty|Popis|
>|---------|---------|
>|Enter_the_Application_Id_Here|Na stránce **Přehled** registrace vaší aplikace se jedná o hodnotu ID vaší **aplikace (klienta)** . |
>|Enter_the_Cloud_Instance_Id_Here|Toto je instance cloudu Azure. V případě hlavního nebo globálního cloudu Azure zadejte **https://login.microsoftonline.com** . Pro národní cloudy (například Čína) si přečtěte téma [národní cloudy](./authentication-national-cloud.md).|
>|Enter_the_Tenant_Info_Here| Nastavte na jednu z následujících možností: Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu ID adresáře (tenant) nebo názvem tenanta (například **contoso.Microsoft.com**). Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**. Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu **běžnými**. Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu **příjemci**. |
>|Enter_the_Redirect_Uri_Here|Nahraďte parametrem **http://localhost:4200** .|
>|cacheLocation  | Volitelné Nastavte úložiště prohlížeče pro stav ověřování. Výchozí hodnota je **SessionStorage**.   |
>|storeAuthStateInCookie  | Volitelné Identifikujte knihovnu, ve které je uložený stav žádosti o ověření. Tento stav je nutný k ověření toků ověřování v souborech cookie prohlížeče. Tento soubor cookie je nastaven pro Internet Explorer a Edge tak, aby se vešel na tyto dva prohlížeče. Další podrobnosti najdete v tématu [známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

Další informace o dostupných konfigurovatelných možnostech najdete v tématu [inicializace klientských aplikací](msal-js-initializing-client-applications.md).

Zdrojový kód knihovny MSAL.js můžete najít v úložišti [AzureAD/Microsoft-Authentication-Library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) na GitHubu.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Krok 3: spuštění projektu

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Krok 4: spuštění projektu

Pokud používáte Node.js:

1. Spusťte Server spuštěním následujících příkazů z adresáře projektu:

   ```console
   npm install
   npm start
   ```

1. Přejděte na **http://localhost:4200/**.
1. Vyberte **Přihlásit**se.
1. Vyberte **profil** , který chcete volat Microsoft Graph.

Poté, co prohlížeč načte aplikaci, vyberte **Přihlásit**. Při prvním přihlášení se zobrazí výzva k zadání vašeho souhlasu, který aplikaci umožní získat přístup k vašemu profilu a přihlásit se. Po úspěšném přihlášení vyberte **profil**a na stránce se zobrazí informace o vašem profilu uživatele.

## <a name="how-the-sample-works"></a>Jak ukázka funguje

![Diagram, který ukazuje, jak ukázková aplikace v tomto rychlém startu funguje](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)


## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte, jak se přihlašovat uživateli a získat tokeny v úhlovém kurzu:

> [!div class="nextstepaction"]
> [Úhlový kurz](./tutorial-v2-angular.md)