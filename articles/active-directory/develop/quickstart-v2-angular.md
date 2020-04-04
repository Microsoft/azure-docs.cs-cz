---
title: Přihlášení uživatelů v jednostránkových aplikacích - Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak může aplikace Angular volat rozhraní API, které vyžaduje přístupové tokeny pomocí platformy identit Microsoftu.
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
ms.openlocfilehash: 61a098b92db13b8422d9cfebb19610c5de7685cd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631753"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-spa"></a>Úvodní příručka: Přihlášení uživatelů a získání přístupového tokenu v úhlovém spa

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

V tomto rychlém startu se pomocí ukázky kódu dozvíte, jak může jednostránková aplikace (SPA) přihlašovat uživatele, kteří mají osobní účty Microsoft, a pracovní účty a školní účty. Angular SPA můžete také získat přístupový token pro volání rozhraní Microsoft Graph API nebo jakékoli webové rozhraní API.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) pro úpravu souborů projektu nebo [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) pro spuštění projektu.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Chcete-li spustit aplikaci pro rychlý start, použijte některou z následujících možností:
>
> ### <a name="option-1-express-register-and-auto-configure-the-app-then-download-the-code-sample"></a>Možnost 1 (Express): Zaregistrujte a automaticky nakonfigurujte aplikaci. Pak si stáhněte ukázku kódu
>
> 1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud má váš účet přístup k více než jednomu tenantovi, vyberte účet v pravém horním rohu a nastavte relaci portálu na tenanta Azure Active Directory (Azure AD), který chcete použít.
> 1. Otevřete nové [podokno Registrace aplikací.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)
> 1. Zadejte název aplikace a vyberte **registrovat**.
> 1. Přejděte do podokna rychlého startu a zobrazte rychlý start Úhlu. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Možnost 2 (ruční): Registrace a ruční konfigurace ukázky aplikace a kódu
>
> #### <a name="step-1-register-the-application"></a>Krok 1: Registrace žádosti
>
> 1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
>
> 1. Pokud má váš účet přístup k více než jednomu tenantovi, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na klienta Azure AD, který chcete použít.
> 1. Podle pokynů [zaregistrujte jednostránkovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) na webu Azure Portal.
> 1. Přidejte novou platformu do **okna Ověřování** registrace aplikace a zaregistrujte identifikátor URI přesměrování:`http://localhost:4200/`
> 1. Tento rychlý start používá [implicitní grantový tok](v2-oauth2-implicit-grant-flow.md). Vyberte **nastavení implicitního udělení** **tokenů ID** a **přístupových tokenů**. ID tokeny a přístupové tokeny jsou povinné, protože tato aplikace se přihlásí u uživatelů a zavolá rozhraní API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure Portal
> Pro ukázku kódu pro tento rychlý start do `redirectUri` `http://localhost:4200/` práce, je třeba přidat as a povolit **implicitní grant**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Proveďte tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-javascript/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-code-sample"></a>Krok 2: Stažení ukázky kódu
>[!div renderon="docs"]
>Chcete-li spustit projekt s webovým serverem pomocí https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular souboru Node.js, klonujte nebo [stáhněte](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip) základní soubory projektu. Otevřete soubory pomocí editoru, jako je visual studio kód.

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
> Nahraďte tyto hodnoty jako takové:
>
>|Název hodnoty|Popis|
>|---------|---------|
>|Enter_the_Application_Id_Here|Na **stránce přehledu** registrace vaší přihlášky je to ID vaší **přihlášky (klienta)** |
>|Enter_the_Cloud_Instance_Id_Here|Toto je instance cloudu Azure. Pro hlavní nebo globální cloud https://login.microsoftonline.comAzure zadejte . Národní cloudy (například Čína) viz [Národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Nastavte jednu z následujících možností: 1) Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID adresáře (tenanta)** nebo **názvem klienta** (například *contoso.microsoft.com*). 2) Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**. 3) Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobníúčty Microsoft*, nahraďte tuto hodnotu **běžnými**. 4) Chcete-li omezit podporu pouze na *osobní účty Microsoft*, nahraďte tuto hodnotu **spotřebiteli**. |
>|Enter_the_Redirect_Uri_Here|Nahradit`http://localhost:4200`|
>|cacheLocation  | (Nepovinné) Nastaví úložiště prohlížeče pro stav auth. Výchozí je sessionStorage.   |
>|storeAuthStateInCookie  | (Nepovinné) Knihovna, která ukládá stav požadavku na ověření. Tento stav je nutný k ověření toků ověřování v souborech cookie prohlížeče. Tento soubor cookie je nastaven pro prohlížeče Internet Explorer a Edge tak, aby vyhovovaly těmto dvěma prohlížečům. Další podrobnosti naleznete v [tématu známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

Další informace o dostupných konfigurovatelných možnostech naleznete [v tématu Inicializaci klientských aplikací](msal-js-initializing-client-applications.md).

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Krok 3: Spuštění projektu

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Krok 4: Spuštění projektu

* Pokud používáte soubor Node.js:

    1. Spusťte server spuštěním následujících příkazů z adresáře projektu:

        ```console
        npm install
        npm start
        ```

    1. Procházet `http://localhost:4200/`.
    1. Vyberte **Přihlásit se**.
    1. Chcete-li volat do aplikace Microsoft Graph, vyberte **profil.**

Po načtení aplikace prohlížečem vyberte **Přihlásit se**. Při prvním přihlášení budete vyzváni k poskytnutí souhlasu s tím, aby aplikace mohla získat přístup k vašemu profilu a přihlásit se k němu. Po úspěšném přihlášení vyberte **profil**a na stránce se zobrazí informace o vašem profilu uživatele.

## <a name="how-the-sample-works"></a>Jak ukázka funguje

![Jak ukázková aplikace v tomto rychlém startu funguje](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> Přejděte do [kurzového úhlu a](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular) dozvíte se, jak se přihlásit k uživateli a získat tokeny.

Projděte si [repo MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js) pro dokumentaci, nejčastější dotazy, problémy a další.
