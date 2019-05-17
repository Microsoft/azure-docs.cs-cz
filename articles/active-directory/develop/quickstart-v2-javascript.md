---
title: Rychlý start Microsoft identity platform JavaScript | Azure
description: Zjistěte, jak může aplikace JavaScript volat rozhraní API, které vyžadují přístupové tokeny ve platforma identit Microsoft.
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
ms.openlocfilehash: 23003186aa413e313578c57616ae03c435f140e1
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785400"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application-spa"></a>Rychlý start: Přihlašování uživatelů a získání přístupového tokenu z jednostránkové aplikace (SPA) jazyka JavaScript

V tomto rychlém startu budete zjistěte, jak pomocí vzorového kódu, který ukazuje, jak JavaScript jednostránková aplikace (SPA) můžou osobní účty, pracovní a školní účty a získání přístupového tokenu pro volání rozhraní Microsoft Graph API nebo libovolné webové rozhraní API.

![Ukazuje, jak ukázková aplikace vygenerované v tomto rychlém startu funguje](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto rychlého startu budete potřebovat následující nastavení:
* Spustit projekt s serveru node.js
    * Instalovat [Node.js](https://nodejs.org/en/download/)
    * Nainstalujte [Visual Studio Code](https://code.visualstudio.com/download) k úpravám souborů projektu
* Chcete-li spustit projekt jako řešení sady Visual Studio, nainstalujte [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Zaregistrujte a stáhněte si aplikaci rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Express] [Možnost 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte k novému [portál Azure – registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) podokně.
> 1. Zadejte název vaší aplikace a klikněte na **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte svoji aplikaci.
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
> 1. Vyberte **registrace nové**.
> 1. Když **zaregistrovat aplikaci** se zobrazí stránka, zadejte název pro vaši aplikaci.
> 1. V části **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft**.
> 1. Vyberte **webové** platformu v rámci **identifikátor URI pro přesměrování** tématu a nastavte hodnotu na `http://localhost:30662/`.
> 1. Až budete hotovi, vyberte **Zaregistrovat**.  V aplikaci **přehled** stránce si poznamenejte **ID aplikace (klient)** hodnotu.
> 1. Tento rychlý start vyžaduje [implicitní tok poskytování](v2-oauth2-implicit-grant-flow.md) povolit. V levém navigačním podokně zaregistrovanou aplikaci vyberte **ověřování**.
> 1. V **upřesňující nastavení**v části **implicitní grant**, oboje povolili **tokeny typu ID** a **přístupové tokeny** zaškrtávací políčka. Tokeny typu ID a přístupové tokeny jsou povinné, protože tato aplikace potřebuje přihlásit uživatele a volat rozhraní API.
> 1. Vyberte **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure Portal
> Pro ukázkový kód pro tento rychlý start pro práci, budete muset přidat přesměrování identifikátoru URI jako `http://localhost:30662/` a povolit **implicitní Grant**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Tyto změny provést pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-javascript/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-project"></a>Krok 2: Stáhněte si projekt

Jednu z těchto možností vhodná můžete do svého vývojového prostředí.
* [Stáhnout soubory projektu core](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip), ke spuštění s webovým serverem pomocí Node.js. Chcete-li otevřít soubory, použijte editor, například [Visual Studio Code](https://code.visualstudio.com/).

* (Volitelné) [Stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)můžete spustit pomocí serveru služby IIS. Extrahujte soubor zip do místní složky, například **C:\Azure-Samples**.



#### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurace aplikace pro JavaScript

> [!div renderon="docs"]
> Ve složce *JavaScriptSPA*, upravit `index.html` a nastavit `clientID` a `authority` hodnoty v rámci `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Ve složce *JavaScriptSPA*, upravit `index.html` a nahraďte `msalConfig` pomocí:

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

```
> [!div renderon="docs"]
>
> Kde:
> - Hodnota `Enter_the_Application_Id_here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.
> - Hodnota `Enter_the_Tenant_Info_Here` je nastavená na jednu z následujících možností:
>   - Pokud vaše aplikace podporuje režim **Účty jen v tomto organizačním adresáři**, nahraďte tuto hodnotu za **ID tenanta** nebo **Název tenanta** (například contoso.microsoft.com).
>   - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>   - Pokud vaše aplikace podporuje **účty v jakékoli organizaci adresáři a osobní účty Microsoft**, nahradí tato hodnota se `common`. K omezení podpory *Microsoft osobní účty pouze*, nahradí tato hodnota se `consumers`.
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.
>

#### <a name="step-4-run-the-project"></a>Krok 4: Spuštění projektu

* Pokud používáte [Node.js](https://nodejs.org/en/download/):

    1. Spusťte následující příkaz z adresáře projektu ke spuštění serveru:

        ```batch
        npm install
        node server.js
        ```

    1. Otevřete webový prohlížeč a přejděte do `http://localhost:30662/`.
    1. Klikněte na tlačítko **Sign In** tlačítko start přihlásit a pak volat Microsoft Graph API.


* Pokud používáte [sady Visual Studio](https://visualstudio.microsoft.com/downloads/), ujistěte se, že vyberte projekt řešení a potom stiskněte klávesu **F5** ke spuštění projektu.

Po prohlížeč načítá aplikaci, klikněte na tlačítko **Sign In**.  Když se poprvé přihlásíte, budete se výzva k zadání váš souhlas, aby aplikace k profilu a pro přihlášení. V úspěšném přihlášení měli byste vidět údajům vašeho uživatelského profilu zobrazí na stránce.

## <a name="more-information"></a>Další informace

### <a name="msaljs"></a>*msal.js*

Knihovna MSAL je knihovna používaná k přihlášení uživatelů a žádosti o tokeny pro přístup k rozhraní API chráněné službou Microsoft identity platform. Tento rychlý Start *index.html* obsahuje odkaz na knihovnu:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Verze nad můžete nahradit nejnovější vydanou verzi v části [uvolní MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Alternativně Pokud máte Node nainstalované, můžete stáhnout na nejnovější verzi pomocí npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Kód tohoto rychlého startu také ukazuje, jak inicializovat knihovnu:

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
> |`ClientId`     |ID aplikace, kterou jste zaregistrovali na portálu Azure Portal|
> |`authority`    | (Volitelné) Je adresu URL autority jak je popsáno v předchozí části konfigurace pro podporu typů účtů. Výchozí oprávnění je `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Volitelné) Tím se nastaví úložiště prohlížeče pro ověření stavu. Výchozí hodnota je sessionStorage.   |
> |`storeAuthStateInCookie`  | (Volitelné) Knihovny uloží stav žádosti o ověření požadované pro ověření toků ověřování v prohlížeči soubory cookie. Tato hodnota je nastavena pro aplikace Internet Explorer a hraničními zařízeními prohlížeče pro zmírnění některých [známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

 Zobrazit [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) podrobné informace o konfigurovatelných možností, které jsou k dispozici.

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
> | `scopes`   | (Volitelné) Obsahuje obory žádá o souhlas uživatele během přihlášení. Například `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (to znamená `api://<Application ID>/access_as_user` ). |

> [!TIP]
> Alternativně můžete chtít použít `loginRedirect` metoda přesměrovat na přihlašovací stránce místo automaticky otevíraném okně aktuální stránku.

### <a name="request-tokens"></a>Požádat o tokeny

Knihovna MSAL má tři metody použité k získání tokenů: `acquireTokenRedirect`, `acquireTokenPopup` a `acquireTokenSilent`

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

Existují situace, kdy potřebujete vynutit uživatelům, aby komunikovali s koncovým bodem platforma identit Microsoft. Příklad:
* Uživatelé mohou muset znovu zadat své přihlašovací údaje, protože vypršela platnost hesla
* Vaše aplikace požaduje přístup k prostředku další obory, které uživatel musí vyjádřit souhlas
* Je nutné dvoufaktorové ověřování

Doporučené vzorce pro většinu aplikací je volání `acquireTokenSilent` nejprve, pak zachytit výjimku a následně zavolat `acquireTokenPopup` (nebo `acquireTokenRedirect`) spustit interaktivní žádosti.

Volání `acquireTokenPopup` výsledky v automaticky otevíraném okně pro přihlášení (nebo `acquireTokenRedirect` výsledkem přesměrování uživatelů na koncový bod Microsoft identity platform) Pokud uživatelé potřebují spolu komunikují prostřednictvím potvrzení přihlašovacích údajů, poskytne svůj souhlas k požadované prostředek, nebo dokončení dvoufaktorového ověřování.

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
> Tento rychlý start využívá `loginRedirect` a `acquireTokenRedirect` metody, když se používá prohlížeč je aplikace Internet Explorer z důvodu [známý problém](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) souvisejících s zpracování zobrazována místní okna v prohlížeči Internet Explorer.

## <a name="next-steps"></a>Další postup

Podrobnější podrobné informace o tom, jak vytvořit aplikaci pro tento rychlý start vyzkoušejte si následující kurz jazyka JavaScript.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Přečtěte si postup, jak vytvořit aplikaci pro tento rychlý start

> [!div class="nextstepaction"]
> [Kurz k přihlášení a volání MS Graphu](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Procházet úložiště MSAL pro dokumentaci, nejčastější dotazy, problémy a další

> [!div class="nextstepaction"]
> [MSAL.js GitHub repo](https://github.com/AzureAD/microsoft-authentication-library-for-js)
