---
title: Rychlý start JavaScript platforma identit Microsoft – Azure
description: Zjistěte, jak může aplikace JavaScript volat rozhraní API, které vyžaduje přístupové tokeny pomocí Microsoft identity platform.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 305479c8872883e434731b5062b408f97f68cc43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055924"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application"></a>Rychlý start: Přihlašování uživatelů a získání přístupového tokenu z jednostránkové aplikace v jazyce JavaScript

V tomto rychlém startu zjistíte, jak pomocí vzorového kódu, který ukazuje, jak JavaScript jednostránková aplikace (SPA) může přihlásit uživatele osobní účty, účty pracovní a školní účty. JavaScript SPA můžete také získat přístupový token k volání rozhraní Microsoft Graph API nebo libovolné webové rozhraní API.

![Jak funguje ukázkovou aplikaci v rámci tohoto rychlého startu](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Požadavky

Tento rychlý start vyžaduje následující nastavení:
* Chcete-li spustit projekt s Node.js server, stáhněte a nainstalujte [Node.js](https://nodejs.org/en/download/).
* Chcete-li upravit soubory projektu, stáhněte a nainstalujte [Visual Studio Code](https://code.visualstudio.com/download).
* Chcete-li spustit projekt jako řešení sady Visual Studio, stáhněte a nainstalujte [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Zaregistrujte a stáhněte si aplikaci rychlý start
> Pokud chcete spustit aplikaci rychlý start, použijte jednu z následujících možností.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 (Express): Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu
>
> 1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí pracovní nebo školní účet nebo osobní účet Microsoft.
> 1. Pokud váš účet poskytuje přístup k více než jednoho tenanta, vyberte účet v pravém horním rohu a pak nastavte portálu relace do tenanta Azure Active Directory (Azure AD) budete chtít používat.
> 1. Přejděte k novému [portál Azure – registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) podokně.
> 1. Zadejte název pro vaši aplikaci a vyberte **zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automaticky konfigurovat novou aplikaci.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2 (ručně): Registraci a ručně konfiguraci vaší aplikace a ukázku kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace
>
> 1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí pracovní nebo školní účet nebo osobní účet Microsoft.
>
> 1. Pokud váš účet poskytuje přístup k více než jednoho tenanta, vyberte svůj účet v pravém horním rohu a pak nastavte portálu relace do tenanta služby Azure AD chcete použít.
> 1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
> 1. Vyberte **registrace nové**.
> 1. Když **zaregistrovat aplikaci** se zobrazí stránka, zadejte název pro vaši aplikaci.
> 1. V části **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft**.
> 1. V části **identifikátor URI pro přesměrování** části, v rozevíracím seznamu vyberte **webové** platformy a pak nastavte hodnotu na `http://localhost:30662/`.
> 1. Vyberte **Zaregistrovat**. V aplikaci **přehled** stránce si poznamenejte **ID aplikace (klient)** hodnoty pro pozdější použití.
> 1. Tento rychlý start vyžaduje [implicitní tok poskytování](v2-oauth2-implicit-grant-flow.md) povolit. V levém podokně zaregistrovanou aplikaci, vyberte **ověřování**.
> 1. V **upřesňující nastavení** pod **implicitní grant**, vyberte **tokeny typu ID** a **přístupové tokeny** zaškrtávací políčka. Tokeny typu ID a přístupové tokeny jsou povinné, protože tato aplikace potřebuje přihlásit uživatele a volat rozhraní API.
> 1. V horní části podokna vyberte **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure Portal
> Pro ukázkový kód pro tento rychlý start pro práci, budete muset přidat přesměrování identifikátoru URI jako `http://localhost:30662/` a povolit **implicitní grant**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Tyto změny provést pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-javascript/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-project"></a>Krok 2: Stáhněte si projekt

Vyberte možnost, která je vhodná pro vaše vývojové prostředí:

* Spustit projekt s webovým serverem s použitím prostředí Node.js, [stáhnout soubory projektu core](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). Chcete-li otevřít soubory, použijte editor [Visual Studio Code](https://code.visualstudio.com/).

* (Volitelné) Spustit projekt s server služby IIS [stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Extrahujte soubor zip do místní složky (například *C:\Azure-Samples*).



#### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurace aplikace pro JavaScript

> [!div renderon="docs"]
> V *JavaScriptSPA* složku, upravit *index.html*a nastavte `clientID` a `authority` hodnoty v rámci `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> V *JavaScriptSPA* složku, upravit *index.html*a nahraďte `msalConfig` následujícím kódem:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_info_here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="docs"]
>
> Kde:
> - *\<Enter_the_Application_Id_here >* je **ID aplikace (klient)** pro aplikace, které jste zaregistrovali.
> - *\<Enter_the_Tenant_info_here >* nastavena na jednu z následujících možností:
>    - Pokud vaše aplikace podporuje *účty v tomto adresáři organizace*, nahraďte tuto hodnotu **ID Tenanta** nebo **název Tenanta** (například  *contoso.microsoft.com*).
>    - Pokud vaše aplikace podporuje *účty v libovolném adresáři organizace*, nahradí tato hodnota se **organizace**.
>    - Pokud vaše aplikace podporuje *účty v jakékoli organizaci adresáři a osobní účty Microsoft*, nahradí tato hodnota se **běžné**. K omezení podpory *Microsoft osobní účty pouze*, nahradí tato hodnota se **příjemci**.
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)** , **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.
>

#### <a name="step-4-run-the-project"></a>Krok 4: Spuštění projektu

* Pokud používáte [Node.js](https://nodejs.org/en/download/):

    1. Pokud chcete spustit na serveru, spusťte následující příkaz z adresáře projektu:

        ```batch
        npm install
        node server.js
        ```

    1. Otevřete webový prohlížeč a přejděte na `http://localhost:30662/`.
    1. Vyberte **Sign In** ke spouštění přihlášení a pak volat Microsoft Graph API.


* Pokud používáte [sady Visual Studio](https://visualstudio.microsoft.com/downloads/), vyberte projekt řešení a potom stisknutím klávesy F5 spusťte projekt.

Po prohlížeč načítá aplikaci, vyberte **Sign In**. Když se poprvé přihlásíte, budete se výzva k zadání váš souhlas, aby aplikace k profilu a pro přihlášení. Po přihlášení v úspěšně, má být zobrazena informace z vašeho profilu uživatele na stránce.

## <a name="more-information"></a>Další informace

### <a name="msaljs"></a>msal.js

Knihovna MSAL přihlašováním uživatelů a požádá o tokeny, které se používají pro přístup k rozhraní API, který je chráněn platforma identit Microsoft. Rychlý Start *index.html* soubor obsahuje odkaz na knihovnu:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Nejnovější vydaná verze v části můžete nahradit předchozí verzi [uvolní MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Alternativně Pokud máte nainstalované Node.js, můžete stáhnout nejnovější verzi pomocí Node.js Package Manageru (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Kód tohoto rychlého startu také ukazuje, jak inicializovat knihovna MSAL:

```javascript
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

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Kde  |  |
> |---------|---------|
> |`ClientId`     | ID aplikace, aplikace, který je registrovaný na portálu Azure portal.|
> |`authority`    | (Volitelné) Adresa URL autority, která podporuje typy účtů, jak je popsáno výše v části o konfiguraci. Výchozí oprávnění je `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Volitelné) Nastaví úložiště prohlížeče pro ověření stavu. Výchozí hodnota je sessionStorage.   |
> |`storeAuthStateInCookie`  | (Volitelné) Knihovna, která ukládá stav žádosti o ověření, potřebná pro ověření toky ověřování v prohlížeči soubory cookie. Tento soubor cookie je nastavena pro aplikace Internet Explorer a hraničními zařízeními prohlížeče pro zmírnění některých [známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Další informace o dostupných konfigurovatelných možností, které najdete v tématu [inicializovat klientské aplikace](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Přihlašování uživatelů

Následující fragment kódu ukazuje, jak přihlásit uživatele:

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
> | `scopes`   | (Volitelné) Obsahuje obory, které jsou požadovány pro vyjádření souhlasu uživatele na čas přihlášení. Například `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (to znamená `api://<Application ID>/access_as_user`). |

> [!TIP]
> Alternativně můžete chtít použít `loginRedirect` metoda přesměrovat na přihlašovací stránce místo automaticky otevíraném okně aktuální stránku.

### <a name="request-tokens"></a>Požádat o tokeny

Knihovna MSAL používá tři metody k získání tokenů: `acquireTokenRedirect`, `acquireTokenPopup`, a `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`acquireTokenSilent` Obsluhovala token pořízení a obnovení bez nutnosti zásahu uživatele. Po `loginRedirect` nebo `loginPopup` provedení metody poprvé, `acquireTokenSilent` se běžně používá k získání tokenů, které se používají pro přístup k chráněným prostředkům pro pozdější volání metody. Volání na vyžádání nebo tokeny obnovení jsou vytvářeny bezobslužně.

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
> | `scopes`   | Obsahuje obory, které jsou požadovány, který se má vrátit v přístupovém tokenu pro rozhraní API. Například `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (to znamená `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Existují situace, kdy potřebujete vynutit uživatelům, aby komunikovali s koncovým bodem Microsoft identity platform. Příklad:
* Uživatele může být nutné znovu zadat své přihlašovací údaje, protože vypršela platnost hesla.
* Vaše aplikace požaduje přístup k prostředku další obory, které uživatel musí vyjádřit souhlas.
* Vyžádáním dvoufaktorového ověřování.

Doporučené vzorce pro většinu aplikací je volání `acquireTokenSilent` nejprve pak zachytit výjimku a poté zavolejte `acquireTokenPopup` (nebo `acquireTokenRedirect`) spustit interaktivní žádosti.

Volání `acquireTokenPopup` výsledky v automaticky otevíraném okně pro přihlášení. (Nebo `acquireTokenRedirect` výsledkem přesměrování uživatelů na koncový bod Microsoft identity platform.) V tomto okně uživatelé potřebují pracovat prostřednictvím potvrzení přihlašovacích údajů, udělení souhlasu pro požadovaný prostředek nebo dokončení dvojúrovňového ověřování.

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
> Tento rychlý start využívá `loginRedirect` a `acquireTokenRedirect` metody s Microsoft Internet Explorer z důvodu [známý problém](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) souvisejících s zpracování automaticky otevíraných oken v aplikaci Internet Explorer.

## <a name="next-steps"></a>Další postup

Podrobnější podrobného průvodce na vytváření aplikací pro účely tohoto rychlého startu najdete tady:

> [!div class="nextstepaction"]
> [Kurz k přihlášení a volání MS Graphu](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Procházet úložiště MSAL pro dokumentaci, nejčastější dotazy, problémy a další, najdete v tématech:

> [!div class="nextstepaction"]
> [MSAL.js GitHub repo](https://github.com/AzureAD/microsoft-authentication-library-for-js)
