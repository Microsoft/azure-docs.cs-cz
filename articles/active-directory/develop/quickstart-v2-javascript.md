---
title: Microsoft Identity Platform JavaScript – rychlý Start – Azure
description: Přečtěte si, jak můžou aplikace JavaScriptu volat rozhraní API, které vyžaduje přístupové tokeny pomocí platformy Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988c73236d9f5ef360ded03bca36a4bb24ebd308
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71290820"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application"></a>Rychlý start: Přihlášení uživatelů a získání přístupového tokenu z jednostránkové aplikace v JavaScriptu

V tomto rychlém startu se dozvíte, jak používat ukázku kódu, která předvádí, jak se dá jednostránková aplikace v JavaScriptu (SPA) přihlašovat uživatelům osobních účtů, pracovních účtů a školních účtů. K volání rozhraní API pro Microsoft Graph nebo libovolné webové rozhraní API může získat přístupový token, a to prostřednictvím JavaScriptu.

![Jak ukázková aplikace v tomto rychlém startu funguje](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Požadavky

Tento rychlý Start vyžaduje následující nastavení:
* Chcete-li spustit projekt se serverem Node. js, Stáhněte a nainstalujte [Node. js](https://nodejs.org/en/download/).
* Chcete-li upravit soubory projektu, Stáhněte a nainstalujte [Visual Studio Code](https://code.visualstudio.com/download).
* Chcete-li spustit projekt jako řešení sady Visual Studio, Stáhněte a nainstalujte [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrace a stažení aplikace pro rychlý Start
> Chcete-li spustit aplikaci pro rychlý Start, použijte kteroukoli z následujících možností.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1 (Express): Zaregistrujte a automaticky nakonfigurujte svoji aplikaci a Stáhněte si ukázku kódu.
>
> 1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
> 1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte účet v pravém horním rohu a nastavte relaci portálu na tenanta Azure Active Directory (Azure AD), který chcete použít.
> 1. Přejít na nové podokno [Azure Portal-registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) .
> 1. Zadejte název vaší aplikace a vyberte **Registrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2 (ruční): Registrace a ruční konfigurace vaší aplikace a ukázky kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte svoji aplikaci.
>
> 1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
>
> 1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu a pak nastavte relaci portálu na tenanta Azure AD, kterého chcete použít.
> 1. Přejít na stránku Microsoft Identity Platform for Developers [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Vyberte **Nová registrace**.
> 1. Po zobrazení stránky **Registrovat aplikaci** zadejte název vaší aplikace.
> 1. V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
> 1. V části **identifikátor URI pro přesměrování** vyberte v rozevíracím seznamu **webovou** platformu a nastavte hodnotu na `http://localhost:30662/`.
> 1. Vyberte **Zaregistrovat**. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
> 1. Tento rychlý Start vyžaduje, aby byl povolený [tok implicitního udělení](v2-oauth2-implicit-grant-flow.md) . V levém podokně registrované aplikace vyberte **ověřování**.
> 1. V části **Upřesnit nastavení** v části **implicitní udělení**vyberte zaškrtávací políčka **tokeny ID** a **přístupové tokeny** . Tokeny ID a přístupové tokeny jsou povinné, protože tato aplikace musí přihlašovat uživatele a volat rozhraní API.
> 1. V horní části podokna vyberte **Save (Uložit**).

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu pro tento rychlý Start fungovala, je nutné přidat identifikátor URI přesměrování jako `http://localhost:30662/` a povolit **implicitní udělení**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-javascript/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-project"></a>Krok 2: Stáhnout projekt

Vyberte možnost, která je vhodná pro vaše vývojové prostředí:

* Chcete-li spustit projekt s webovým serverem pomocí Node. js, [Stáhněte si základní soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). K otevření souborů použijte editor, například [Visual Studio Code](https://code.visualstudio.com/).

* Volitelné Chcete-li spustit projekt se serverem služby IIS, [Stáhněte projekt sady Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Extrahujte soubor zip do místní složky (například *C:\Azure-Samples*).



#### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurace aplikace JavaScriptu

> [!div renderon="docs"]
> Ve složce *JavaScriptSPA* upravte *index. html* `clientID` a nastavte hodnoty a `authority` v části `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Ve složce *JavaScriptSPA* upravte *index. html*a nahraďte `msalConfig` následujícím kódem:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_info_here",
        redirectURI: "http://localhost:30662/"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="portal"]
> > [!NOTE]
> > Tento rychlý Start podporuje Enter_the_Supported_Account_Info_Here.


> [!div renderon="docs"]
>
> Kde:
> - Enter_the_Application_Id_here > je **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali.  *\<*
> - Enter_the_Tenant_info_here > je nastavená na jednu z následujících možností:  *\<*
>    - Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například *contoso.Microsoft.com*).
>    - Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**.
>    - Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tutohodnotu běžnými. Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu **příjemci**.
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)** , **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.
>

#### <a name="step-4-run-the-project"></a>Krok 4: Spuštění projektu

* Pokud používáte [Node. js](https://nodejs.org/en/download/):

    1. Chcete-li spustit server, spusťte následující příkaz z adresáře projektu:

        ```batch
        npm install
        node server.js
        ```

    1. Otevřete webový prohlížeč a pokračujte na `http://localhost:30662/`.
    1. Vyberte **Přihlásit** se a spusťte přihlášení a pak zavolejte Microsoft Graph API.


* Pokud používáte [aplikaci Visual Studio](https://visualstudio.microsoft.com/downloads/), vyberte řešení projektu a potom vyberte F5 pro spuštění projektu.

Poté, co prohlížeč načte aplikaci, vyberte možnost **Přihlásit**se. Při prvním přihlášení se zobrazí výzva k zadání vašeho souhlasu, který aplikaci umožní přístup k vašemu profilu a přihlášení. Po úspěšném přihlášení by se na stránce měly zobrazit informace o vašem profilu uživatele.

## <a name="more-information"></a>Další informace

### <a name="msaljs"></a>msal.js

Knihovna MSAL se přihlásí uživatelům a požádá o tokeny, které se používají pro přístup k rozhraní API, které je chráněné platformou Microsoft identity. Soubor *. html* s indexem pro rychlý Start obsahuje odkaz na knihovnu:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Předchozí verzi můžete nahradit nejnovější vydanou verzí ve [verzích MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Případně, pokud máte nainstalován Node. js, si můžete stáhnout nejnovější verzi prostřednictvím Správce balíčků Node. js (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Kód pro rychlý Start také ukazuje, jak inicializovat knihovnu MSAL:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
        redirectURI: "http://localhost:30662/"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Kde  |  |
> |---------|---------|
> |`clientId`     | ID aplikace, která je zaregistrována v Azure Portal.|
> |`authority`    | Volitelné Adresa URL autority, která podporuje typy účtů, jak je popsáno výše v části konfigurace. Výchozí autorita je `https://login.microsoftonline.com/common`. |
> |`redirectURI`     | Registrační identifikátor URI pro odpověď nebo přesměrování aplikace byl nakonfigurován. V tomto případě `http://localhost:30662/`. |
> |`cacheLocation`  | Volitelné Nastaví úložiště prohlížeče pro stav ověřování. Výchozí hodnota je sessionStorage.   |
> |`storeAuthStateInCookie`  | Volitelné Knihovna, ve které je uložen stav žádosti o ověření, který je požadován pro ověření toků ověřování v souborech cookie prohlížeče. Tento soubor cookie je nastaven pro prohlížeče IE a Edge, aby se zmírnily určité [známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Další informace o dostupných konfigurovatelných možnostech najdete v tématu [inicializace klientských aplikací](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Přihlášení uživatelů

Následující fragment kódu ukazuje, jak přihlašovat uživatele:

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Kde  |  |
> |---------|---------|
> | `scopes`   | Volitelné Obsahuje obory, které jsou požadovány pro souhlas uživatele v době přihlášení. Například `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová `api://<Application ID>/access_as_user`rozhraní API (tj.). |

> [!TIP]
> Alternativně můžete chtít použít `loginRedirect` metodu pro přesměrování aktuální stránky na přihlašovací stránku místo na místní okno.

### <a name="request-tokens"></a>Žádosti o tokeny

MSAL používá tři metody k získání tokenů `acquireTokenRedirect`: `acquireTokenPopup`, a.`acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`acquireTokenSilent` Metoda zpracovává získání a obnovení tokenu bez zásahu uživatele. Po prvním `loginPopup` spuštění metody `loginRedirect` nebo sejednáometodu,kteráseběžněpoužívákzískánítokenůpoužívanýchkpřístupukchráněnýmprostředkůmpro`acquireTokenSilent` následná volání. Volání požadavků na požadavky nebo obnovení tokenů se provádí tiše.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> |Kde  |  |
> |---------|---------|
> | `scopes`   | Obsahuje požadované obory, které mají být vráceny v přístupovém tokenu pro rozhraní API. Například `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová `api://<Application ID>/access_as_user`rozhraní API (tj.).|

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Existují situace, kdy potřebujete vynutit, aby uživatelé mohli pracovat s koncovým bodem Microsoft Identity Platform. Příklad:
* Uživatelé možná budou muset znovu zadat svoje přihlašovací údaje, protože vypršela platnost hesla.
* Vaše aplikace požaduje přístup k dalším oborům prostředků, ke kterým uživatel musí vyjádřit souhlas.
* Je vyžadováno dvojúrovňové ověřování.

Obvyklý doporučený vzor pro většinu aplikací je zavolat `acquireTokenSilent` nejprve a pak zachytit výjimku a pak voláním `acquireTokenPopup` (nebo `acquireTokenRedirect`) spustit interaktivní požadavek.

`acquireTokenPopup` Volání výsledků v překryvném okně pro přihlášení. (Nebo `acquireTokenRedirect` má za následek přesměrování uživatelů na koncový bod Microsoft Identity Platform.) V tomto okně musí uživatelé interaktivně pracovat tím, že potvrdí své přihlašovací údaje, udělí souhlas požadovanému prostředku nebo dokončí ověřování pomocí dvou faktorů.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> [!NOTE]
> V `loginRedirect` tomto rychlém startu `acquireTokenRedirect` se používá metoda a v aplikaci Microsoft Internet Explorer, protože se jedná o [známý problém](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) související se zpracováním překryvných oken aplikací Internet Explorer.

## <a name="next-steps"></a>Další kroky

Podrobnější návod k sestavování aplikace pro tento rychlý Start najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Kurz pro přihlášení a volání MS graphu](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

K procházení úložiště MSAL pro dokumentaci, nejčastější dotazy, problémy a další informace najdete v těchto tématech:

> [!div class="nextstepaction"]
> [MSAL.js GitHub repo](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
