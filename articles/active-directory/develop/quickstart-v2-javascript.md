---
title: Azure AD v2 jazyka JavaScript pro rychlý start | Dokumentace Microsoftu
description: Zjistěte, jak může aplikace JavaScript volat rozhraní API, které vyžadují přístupové tokeny pomocí koncového bodu Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 1b884571707aab71e8a8d124ba68f938e5a63a43
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063740"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Rychlý start: Přihlášení uživatelů a získání přístupového tokenu z aplikace v jazyce JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

V tomto rychlém startu budete zjistěte, jak pomocí vzorového kódu, který ukazuje, jak JavaScript jednostránkové aplikace (SPA) může přihlásit osobní účty, pracovní a školní účty, získání přístupového tokenu pro volání rozhraní Microsoft Graph API nebo libovolné webové rozhraní API.

![Jak ukázková aplikace vygenerované v tomto rychlém startu funguje](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Zaregistrujte si vaši aplikaci a stáhněte si aplikaci rychlý start
>
> ### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace
>
> 1. Přejděte [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app) pro registraci aplikace.
> 1. V **název_aplikace** pole, zadejte název pro vaši aplikaci.
> 1. Ujistěte se, že **instalační program s asistencí** je zaškrtávací políčko nezaškrtnuté a pak vyberte **vytvořit**.
> 1. Klikněte na tlačítko **přidat platformy**a pak vyberte **webové**.
> 1. Ujistěte se, že **povolit implicitní tok** je *zaškrtnutí*.
> 1. V části **adresy URL pro přesměrování** přidat `http://localhost:30662/`.
> 1. Klikněte na **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure portal
> Pro ukázkový kód pro tento rychlý start pro práci, budete muset přidat přesměrování identifikátoru URI jako `http://localhost:30662/` a povolit **implicitní Grant**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Tyto změny provést pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurovali](media/quickstart-v2-javascript/green-check.png) vaše aplikace je nakonfigurovaná s těmito atributy

#### <a name="step-2-download-the-project"></a>Krok 2: Stažení projektu

Jednu z těchto možností vhodná můžete do svého vývojového prostředí.
* [Stáhnout soubory projektu core - pro webový server, jako je například Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Extrahujte soubor zip do místní složky (například **C:\Azure-Samples**).

#### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurace aplikace pro JavaScript

> [!div renderon="docs"]
> Upravit `index.html` a nahraďte `Enter_the_Application_Id_here` pod `applicationConfig` s ID aplikace ID aplikace, které jste právě zaregistrovali.

> [!div class="sxs-lookup" renderon="portal"]
> Upravit `index.html` a nahraďte `applicationConfig` pomocí:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!NOTE]
>Pokud používáte [Node.js](https://nodejs.org/en/download/), *server.js* souboru je nakonfigurovaný pro server zahájit naslouchání na portu 30662.
> Pokud používáte [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), ukázkový kód *.csproj* souboru je nakonfigurovaný pro server zahájit naslouchání na portu 30662.
>

#### <a name="step-4-run-the-project"></a>Krok 4: Spuštění projektu

Pokud pomocí Node.js, v příkazovém řádku můžete spustit následující z adresáře projektu ke spuštění serveru:
 ```batch
 npm install
 node server.js
 ```
Otevřete webový prohlížeč a přejděte do `http://localhost:30662/`. Klikněte na tlačítko **Sign In** tlačítko start přihlásit a pak volat Microsoft Graph API.

Pokud pomocí sady Visual Studio, je nutné vybrat projekt řešení a potom stiskněte klávesu **F5** ke spuštění projektu.

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

### <a name="msal-initialization"></a>Inicializace MSAL

Kód tohoto rychlého startu také ukazuje, jak inicializovat knihovnu:

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Kde  |  |
> |---------|---------|
> |`ClientId`     |Id aplikace z aplikace zaregistrované na webu Azure Portal|
> |`authority`    |Je adresa URL autority. Předání *null* nastaví výchozí oprávnění `https://login.microsoftonline.com/common`. Pokud je vaše aplikace (cílení účty v jednom adresáři jenom) pro jednoho tenanta, nastavte tuto hodnotu na `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| Volá se po ověření přesměruje zpátky do aplikace metodu zpětného volání. Tady `acquireTokenRedirectCallBack` je předán. To má hodnotu null, pokud používáte loginPopup.|
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
> | `scopes`   | (Volitelné) Obsahuje obory žádá o souhlas uživatele na čas přihlášení (např: `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (to znamená `api://<Application ID>/access_as_user` ). Tady `applicationConfig.graphScopes` je předán. |

> [!TIP]
> Alternativně můžete chtít použít `loginRedirect` metoda přesměrovat na přihlašovací stránce místo automaticky otevíraném okně aktuální stránku.


### <a name="request-tokens"></a>Požádat o tokeny

Knihovna MSAL má tři metody použité k získání tokenů: `acquireTokenRedirect`, `acquireTokenPopup` a `acquireTokenSilent`:

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`acquireTokenSilent` Obsluhovala token pořízení a obnovení bez nutnosti zásahu uživatele. Po `loginRedirect` nebo `loginPopup` provedení metody poprvé, `acquireTokenSilent` se běžně používá k získání tokenů, které se používají pro přístup k chráněným prostředkům pro pozdější volání metody. Volání na vyžádání nebo tokeny obnovení jsou vytvářeny bezobslužně.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Kde  |  |
> |---------|---------|
> | `scopes`   | Obsahuje obory, které jsou požadovány, který se má vrátit v přístupovém tokenu pro rozhraní API (např: `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (to znamená `api://<Application ID>/access_as_user` ). Tady `applicationConfig.graphScopes` je předán.|

#### <a name="get-a-user-token-interactively"></a>Získání tokenu uživatele interaktivně

 Existují situace, kdy potřebujete vynutit uživatelům, aby komunikovali s koncovým bodem v2.0 Azure AD. Příklad:
* Uživatelé mohou muset znovu zadat své přihlašovací údaje, protože vypršela platnost hesla
* Vaše aplikace požaduje přístup k prostředku další obory, které uživatel musí vyjádřit souhlas
* Dvoufaktorové ověřování je povinné

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
> [úložiště GitHub msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
