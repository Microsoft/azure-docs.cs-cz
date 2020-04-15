---
title: Přihlášení uživatelů v jednostránkových aplikacích - Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak může angular aplikace volat rozhraní API, které vyžaduje přístupové tokeny pomocí platformy identit microsoftu.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: 4b6a2481c18314a44470a020033ffdc4ba1d7259
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380017"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Úvodní příručka: Přihlášení uživatelů a získání přístupového tokenu v jednostránkové aplikaci Angular

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se mohou před všeobecnou dostupností změnit.

V tomto rychlém startu se pomocí ukázky kódu dozvíte, jak může jednostránková aplikace (SPA) přihlašovat uživatele, kteří mají osobní účty Microsoft, pracovní účty nebo školní účty. Angular SPA můžete také získat přístupový token pro volání rozhraní Microsoft Graph API nebo jakékoli webové rozhraní API.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Soubor Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) pro úpravu souborů projektu nebo [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) pro spuštění projektu.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Chcete-li spustit aplikaci pro rychlý start, použijte některou z následujících možností.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Možnost 1 (express): Zaregistrujte a automaticky nakonfigurujte aplikaci a pak stáhněte ukázku kódu
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
> 1. Pokud má váš účet přístup k více než jednomu tenantovi, vyberte účet v pravém horním bodě a nastavte relaci portálu na tenanta Azure Active Directory (Azure AD), který chcete použít.
> 1. Otevřete nové podokno [registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) na webu Azure Portal.
> 1. Zadejte název aplikace a vyberte **registrovat**.
> 1. Přejděte do podokna rychlého startu a zobrazte rychlý start Angular. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Možnost 2 (ruční): Registrace a ruční konfigurace ukázky aplikace a kódu
>
> #### <a name="step-1-register-the-application"></a>Krok 1: Registrace žádosti
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
> 1. Pokud má váš účet přístup k více než jednomu tenantovi, vyberte svůj účet v pravém horním bodě a nastavte relaci portálu na klienta Azure AD, který chcete použít.
> 1. Podle pokynů [zaregistrujte jednostránkovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) na webu Azure Portal.
> 1. Přidejte novou platformu do podokna **Ověřování** registrace aplikace `http://localhost:4200/`a zaregistrujte identifikátor URI přesměrování: .
> 1. Tento rychlý start používá [implicitní grantový tok](v2-oauth2-implicit-grant-flow.md). Vyberte **implicitní** nastavení udělení **tokenů ID** a **tokenů aplikace Access**. ID tokeny a přístupové tokeny jsou povinné, protože tato aplikace se přihlásí u uživatelů a zavolá rozhraní API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure Portal
> Pro ukázku kódu pro tento rychlý start pracovat, je **http://localhost:4200/** třeba přidat přesměrování URI jako a povolit **implicitní grant**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Proveďte tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-javascript/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-code-sample"></a>Krok 2: Stažení ukázky kódu
>[!div renderon="docs"]
>Chcete-li spustit projekt s webovým serverem pomocí souboru Node.js, [klonujte ukázkové úložiště](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) nebo [stáhněte základní soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Otevřete soubory pomocí editoru, jako je visual studio kód.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Stáhnout ukázku kódu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Krok 3: Konfigurace aplikace JavaScript
>
> Ve složce *src/app* upravte *soubor app.module.ts* a nastavte hodnoty `clientId` a `authority` v části `MsalModule.forRoot`.
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
>|Enter_the_Application_Id_Here|Na stránce **Přehled** registrace aplikace se jedná o hodnotu **ID aplikace (klienta).** |
>|Enter_the_Cloud_Instance_Id_Here|Toto je instance cloudu Azure. Pro hlavní nebo globální cloud **https://login.microsoftonline.com**Azure zadejte . Národní cloudy (například Čína) viz [Národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Nastavte jednu z následujících možností: Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu ID adresáře (tenanta) nebo názvem klienta (například **contoso.microsoft.com**). Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**. Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu **běžnými**. Chcete-li omezit podporu pouze na *osobní účty Microsoft*, nahraďte tuto hodnotu **spotřebiteli**. |
>|Enter_the_Redirect_Uri_Here|Nahradit **http://localhost:4200**.|
>|cacheLocation  | (Nepovinné) Nastavte úložiště prohlížeče pro stav ověřování. Výchozí hodnota je **sessionStorage**.   |
>|storeAuthStateInCookie  | (Nepovinné) Identifikujte knihovnu, ve které je uložen stav požadavku na ověření. Tento stav je nutný k ověření toků ověřování v souborech cookie prohlížeče. Tento soubor cookie je nastaven pro aplikace Internet Explorer a Edge tak, aby vyhovoval těmto dvěma prohlížečům. Další podrobnosti naleznete v [tématu známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

Další informace o dostupných konfigurovatelných možnostech naleznete [v tématu Inicializaci klientských aplikací](msal-js-initializing-client-applications.md). 

Zdrojový kód knihovny MSAL.js najdete v úložišti [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) na GitHubu.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Krok 3: Spuštění projektu

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Krok 4: Spuštění projektu

Pokud používáte soubor Node.js:

1. Spusťte server spuštěním následujících příkazů z adresáře projektu:

   ```console
   npm install
   npm start
   ```

1. Přejděte **http://localhost:4200/** na .
1. Vyberte **Přihlásit se**.
1. Chcete-li volat do aplikace Microsoft Graph, vyberte **profil.**

Po načtení aplikace prohlížečem vyberte **Přihlásit se**. Při prvním přihlášení budete vyzváni k poskytnutí souhlasu s tím, aby aplikace mohla získat přístup k vašemu profilu a přihlásit se k němu. Po úspěšném přihlášení vyberte **profil**a na stránce se zobrazí informace o vašem profilu uživatele.

## <a name="how-the-sample-works"></a>Jak ukázka funguje

![Diagram, který ukazuje, jak ukázková aplikace v tomto rychlém startu funguje](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>Další kroky

Dále se dozvíte, jak se přihlásit k uživateli a získat tokeny v kurzu angular:

> [!div class="nextstepaction"]
> [Úhlové výukové program](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)

