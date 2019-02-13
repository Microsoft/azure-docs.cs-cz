---
title: Azure AD v2 jazyka JavaScript pro rychlý start | Dokumentace Microsoftu
description: Zjistěte, jak může aplikace JavaScript volat rozhraní API, které vyžadují přístupové tokeny pomocí koncového bodu Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a97e03f3c195b9fbd0ee7a09950414b7a940c7c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217475"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Rychlý start: Přihlašování uživatelů a získání přístupového tokenu z aplikace v jazyce JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

V tomto rychlém startu budete zjistěte, jak pomocí vzorového kódu, který ukazuje, jak JavaScript jednostránkové aplikace (SPA) může přihlásit osobní účty, pracovní a školní účty, získání přístupového tokenu pro volání rozhraní Microsoft Graph API nebo libovolné webové rozhraní API.

![Jak funguje ukázková aplikace vygenerovaná v tomto rychlém startu](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Zaregistrujte a stáhněte si aplikaci rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Express] [Možnost 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu
>
> 1. Přejděte na [Azure Portal – Registrace aplikace (Preview)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Zadejte název vaší aplikace a klikněte na **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace
>
> 1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) pro registraci aplikace.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak **Registrace aplikací (Preview) > Nová registrace**.
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
* [Stáhnout soubory projektu core - pro webový server, jako je například Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Extrahujte soubor zip do místní složky, například **C:\Azure-Samples**.

#### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurace aplikace pro JavaScript

> [!div renderon="docs"]
> Upravit `index.html` a nastavit `clientID` a `authority` hodnoty v rámci `applicationConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Upravit `index.html` a nahraďte `applicationConfig` pomocí:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!div renderon="docs"]
>
> Kde:
> - Hodnota `Enter_the_Application_Id_here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.
> - Hodnota `Enter_the_Tenant_Info_Here` je nastavená na jednu z následujících možností:
>   - Pokud vaše aplikace podporuje režim **Účty jen v tomto organizačním adresáři**, nahraďte tuto hodnotu za **ID tenanta** nebo **Název tenanta** (například contoso.microsoft.com).
>   - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>   - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři a osobní účty Mircosoft**, nahraďte tuto hodnotu za `common`.
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

> [!NOTE]
> Server je nakonfigurovaná k naslouchání na portu 30662 v *server.js* ve [Node.js](https://nodejs.org/en/download/) projektu a *.csproj* ve [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)projektu.
>

#### <a name="step-4-run-the-project"></a>Krok 4: Spuštění projektu

* Pokud používáte Node.js:

    1. Spusťte následující příkaz z adresáře projektu ke spuštění serveru:

        ```batch
        npm install
        node server.js
        ```

    1. Otevřete webový prohlížeč a přejděte do `http://localhost:30662/`.
    1. Klikněte na tlačítko **Sign In** tlačítko start přihlásit a pak volat Microsoft Graph API.


* Pokud pomocí sady Visual Studio, je nutné vybrat projekt řešení a potom stiskněte klávesu **F5** ke spuštění projektu.

## <a name="more-information"></a>Další informace

### <a name="msaljs"></a>*msal.js*

Knihovna MSAL je knihovna používaná k přihlášení uživatelů a žádosti o tokeny pro přístup k rozhraní API chráněné službou Microsoft Azure Active Directory (Azure AD). Tento rychlý Start *index.html* obsahuje odkaz na knihovnu:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

Případně pokud máte Node nainstalované, můžete ji stáhnout prostřednictvím npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Kód tohoto rychlého startu také ukazuje, jak inicializovat knihovnu:

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, applicationConfig.authority, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Kde  |  |
> |---------|---------|
> |`ClientId`     |ID aplikace, kterou jste zaregistrovali na portálu Azure Portal|
> |`authority`    |Je adresa URL autority. Předání *null* nastaví výchozí oprávnění `https://login.microsoftonline.com/common`. Pokud je vaše aplikace (cílení účty v jednom adresáři jenom) pro jednoho tenanta, nastavte tuto hodnotu na `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| Volá se po ověření přesměruje do aplikace metodu zpětného volání. Tady `acquireTokenRedirectCallBack` je předán. To má hodnotu null, pokud používáte loginPopup.|
> |`options`  |Kolekce nepovinných parametrů. V tomto případě `storeAuthStateInCookie` a `cacheLocation` jsou volitelné konfigurace. Zobrazit [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) podrobné informace o možnostech. |

### <a name="sign-in-users"></a>Přihlašování uživatelů

Následující fragment kódu ukazuje, jak přihlásit uživatele:

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Kde  |  |
> |---------|---------|
> | `scopes`   | (Volitelné) Obsahuje obory žádá o souhlas uživatele během přihlášení. Například `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (to znamená `api://<Application ID>/access_as_user` ). Tady `applicationConfig.graphScopes` je předán. |

> [!TIP]
> Alternativně můžete chtít použít `loginRedirect` metoda přesměrovat na přihlašovací stránce místo automaticky otevíraném okně aktuální stránku.

### <a name="request-tokens"></a>Požádat o tokeny

Knihovna MSAL má tři metody použité k získání tokenů: `acquireTokenRedirect`, `acquireTokenPopup` a `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`acquireTokenSilent` Obsluhovala token pořízení a obnovení bez nutnosti zásahu uživatele. Po `loginRedirect` nebo `loginPopup` provedení metody poprvé, `acquireTokenSilent` se běžně používá k získání tokenů, které se používají pro přístup k chráněným prostředkům pro pozdější volání metody. Volání na vyžádání nebo tokeny obnovení jsou vytvářeny bezobslužně.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Kde  |  |
> |---------|---------|
> | `scopes`   | Obsahuje obory, které jsou požadovány, který se má vrátit v přístupovém tokenu pro rozhraní API. Například `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (to znamená `api://<Application ID>/access_as_user`). Tady `applicationConfig.graphScopes` je předán.|

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Existují situace, kdy potřebujete vynutit uživatelům, aby komunikovali s koncovým bodem v2.0 Azure AD. Příklad:
* Uživatelé mohou muset znovu zadat své přihlašovací údaje, protože vypršela platnost hesla
* Vaše aplikace požaduje přístup k prostředku další obory, které uživatel musí vyjádřit souhlas
* Je nutné dvoufaktorové ověřování

Doporučené vzorce pro většinu aplikací je volání `acquireTokenSilent` nejprve, pak zachytit výjimku a následně zavolat `acquireTokenRedirect` (nebo `acquireTokenPopup`) spustit interaktivní žádosti.

Volání `acquireTokenPopup(scope)` výsledky v automaticky otevíraném okně pro přihlášení (nebo `acquireTokenRedirect(scope)` výsledkem přesměrování uživatelů na koncový bod Azure AD v2.0) Pokud uživatelé potřebují pracovat prostřednictvím potvrzení své přihlašovací údaje, udělení souhlasu pro požadovaný prostředek, nebo dokončení dvoufaktorového ověřování.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> Tento rychlý start využívá `loginRedirect` a `acquireTokenRedirect` metody, když se používá prohlížeč je aplikace Internet Explorer z důvodu [známý problém](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) souvisejících s zpracování zobrazována místní okna v prohlížeči Internet Explorer.

## <a name="next-steps"></a>Další postup

Podrobnější podrobné informace o tom, jak vytvořit aplikaci pro tento rychlý start vyzkoušejte si následující kurz jazyka JavaScript.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Přečtěte si postup, jak vytvořit aplikaci pro tento rychlý start

> [!div class="nextstepaction"]
> [Kurz volání rozhraní Graph API](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Procházet úložiště MSAL pro dokumentaci, nejčastější dotazy, problémy a další

> [!div class="nextstepaction"]
> [msal.js GitHub repo](https://github.com/AzureAD/microsoft-authentication-library-for-js)
