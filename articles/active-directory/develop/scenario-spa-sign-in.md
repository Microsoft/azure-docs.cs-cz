---
title: Jednostránkové aplikace (přihlásit se) – platforma identit Microsoft
description: Další informace o vytváření jednostránkové aplikace (přihlášení)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138791"
---
# <a name="single-page-application---sign-in"></a>Jednostránková aplikace – přihlášení

Zjistěte, jak přidat přihlášení ke kódu pro vaši jednostránkovou aplikaci.

Než budete moct získat tokeny pro přístup k rozhraní API ve vaší aplikaci, budete potřebovat objekt context ověřeného uživatele. Uživatelům můžete přihlásit do vaší aplikace v MSAL.js dvěma způsoby:

* [Přihlaste se pomocí automaticky otevírané okno](#sign-in-with-a-pop-up-window) pomocí `loginPopup` – metoda
* [Přihlaste se pomocí přesměrování](#sign-in-with-redirect) pomocí `loginRedirect` – metoda

Také v případě potřeby můžete předat obory API, u kterého je nutné o souhlas v době přihlášení.

> [!NOTE]
> Pokud už vaše aplikace má přístup do kontextu ověřeného uživatele nebo id tokenu, můžete přeskočit krok přihlášení a přímo získat tokeny. Další podrobnosti najdete v tématu [jednotné přihlašování bez přihlášení msal.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Volba mezi automaticky otevírané okno nebo přesměrovat prostředí

Kombinace metod automaticky otevírané okno i přesměrování nelze použít ve vaší aplikaci. Volba mezi automaticky otevírané okno nebo přesměrovat prostředí závisí na vaší aplikace flow.

* Pokud nechcete, aby uživatel se přejít mimo vaše hlavní aplikace stránky během ověřování, doporučujeme použít místní metody. Protože ověřování přesměrování se děje v automaticky otevírané okno, stav hlavní aplikace se zachová.

* Existují určité případy, kde je nutné použít metody přesměrování. Pokud uživatelé vaší aplikace mají omezení prohlížeče nebo zásady níž jsou automaticky otevíraná okna windows zakázány, můžete použít metody přesměrování. Používat metody přesměrování se prohlížeč Internet Explorer, protože existují určité [známé problémy s aplikací Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) při zpracování automaticky otevíraná okna.

## <a name="sign-in-with-a-pop-up-window"></a>Přihlaste se pomocí automaticky otevírané okno

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Úhlová

MSAL Angular obálky umožňuje zabezpečit konkrétní trasy do vaší aplikace tím, že pouhé přidání `MsalGuard` do definice trasy. Tato guard se vyvolat metodu pro přihlášení při přístupu k této trase.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Automaticky otevírané okno prostředí, povolte `popUp` možnost konfigurace. Můžete také předat obory, které vyžadují souhlas následujícím způsobem:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Přihlaste se pomocí přesměrování

### <a name="javascript"></a>JavaScript

Metody přesměrování nevrátí příslib z důvodu navigaci pryč z hlavní aplikace. Ke zpracování a přístupové tokeny vrácené, je potřeba zaregistrovat o úspěších a chybách zpětná volání před voláním metody přesměrování.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Úhlová

Sem kód je stejný, jak je popsáno výše v části přihlásit pomocí automaticky otevírané okno oddílu. Výchozí tok je přesměrování.

> [!NOTE]
> ID tokenu neobsahuje odsouhlasené obory a pouze představuje ověřeného uživatele. Odsouhlasené obory jsou vráceny v tokenu přístupu, které získáte v dalším kroku.

## <a name="sign-out"></a>Odhlásit se

Knihovna MSAL poskytuje `logout` metodu, která se vymazání mezipaměti v prohlížeči úložiště a odešle přihlašovací požadavek do služby Azure AD. Po odhlášení je přesměrován zpět na úvodní stránku aplikace ve výchozím nastavení.

Můžete nakonfigurovat URI, ke kterému se by se měla přesměrovat po přihlášení na nastavením `postLogoutRedirectUri`. Pomocí tohoto identifikátoru URI by měl být zaregistrován také jako identifikátor URI odhlašování do vaší registrace aplikace.

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Úhlová

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získání tokenu pro aplikaci](scenario-spa-acquire-token.md)
