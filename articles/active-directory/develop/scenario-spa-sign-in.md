---
title: Přihlašování & jednostránkové aplikace na jednu stránku
titleSuffix: Microsoft identity platform
description: Informace o tom, jak vytvořit jednostránkovou aplikaci (přihlášení)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bdfffbf8d2e416c87dd5abb8f6383c58ad270231
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584359"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Jednostránkové aplikace: přihlášení a odhlášení

Naučte se, jak přidat přihlášení k kódu pro aplikaci s jednou stránkou.

Než budete moct získat tokeny pro přístup k rozhraním API ve vaší aplikaci, potřebujete kontext ověřeného uživatele. Uživatele můžete přihlašovat do aplikace v MSAL.js dvěma způsoby:

* [Místní okno](#sign-in-with-a-pop-up-window)pomocí `loginPopup` metody
* [Přesměrování](#sign-in-with-redirect)pomocí `loginRedirect` metody

Volitelně můžete také předávat obory rozhraní API, pro které budete potřebovat souhlas uživatele v době přihlášení.

> [!NOTE]
> Pokud má vaše aplikace již přístup k kontextu ověřeného uživatele nebo ID tokenu, můžete přeskočit krok přihlášení a přímo získat tokeny. Podrobnosti najdete v tématu [SSO bez MSAL.js přihlášení](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Volba mezi prostředím pro místní nebo přesměrování

V aplikaci nemůžete použít metody automaticky otevíraných oken a přesměrování. Volba mezi zobrazováním překryvných nebo přesměrování závisí na vašem toku aplikace:

* Pokud nechcete, aby se uživatelé během ověřování přesunuli ze stránky hlavní aplikace, doporučujeme použít místní metodu. Vzhledem k tomu, že k přesměrování ověřování dojde v překryvném okně, zůstane zachován stav hlavní aplikace.

* Pokud uživatelé mají omezení nebo zásady prohlížeče, kde jsou automaticky otevíraná okna zakázaná, můžete použít metodu přesměrování. Použijte metodu přesměrování v prohlížeči Internet Explorer, protože u [automaticky otevíraných oken v aplikaci Internet Explorer jsou známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Přihlášení pomocí automaticky otevíraného okna


# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success

        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();
    
        if (currentAccounts === null) {
            // no accounts detected
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
        }
    
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

Úhlová obálka MSAL umožňuje zabezpečit konkrétní trasy ve vaší aplikaci přidáním `MsalGuard` do definice trasy. Tato ochrana vyvolá metodu pro přihlášení k této trase.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

V případě okna pro zobrazování překryvných oken povolte `popUp` možnost konfigurace. Obory, které vyžadují souhlas, můžete také předat následujícím způsobem:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Přihlášení pomocí přesměrování

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    //handle redirect response

    // In case multiple accounts exist, you can select
    const currentAccounts = myMsal.getAllAccounts();

    if (currentAccounts === null) {
        // no accounts detected
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
    }
}

myMsal.handleRedirectPromise(handleResponse);

myMsal.loginRedirect(loginRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

Metody přesměrování nevrací příslib z důvodu přesunu z hlavní aplikace. Chcete-li zpracovat vrácené tokeny a přistupovat k nim, zaregistrujte před voláním metod přesměrování úspěch a zpětná volání chyb.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

Kód je stejný, jak je popsáno výše v části o přihlášení pomocí překryvného okna. Výchozím tokem je přesměrování.

---

## <a name="sign-out"></a>Odhlášení

Knihovna MSAL poskytuje `logout` metodu, která vymaže mezipaměť v prohlížeči úložiště a pošle žádost o odhlášení Azure Active Directory (Azure AD). Po odhlášení se knihovna přesměruje zpátky na úvodní stránku aplikace ve výchozím nastavení.

Můžete nakonfigurovat identifikátor URI, na který se má přesměrování po odhlášení nastavit `postLogoutRedirectUri` . Tento identifikátor URI by měl být také zaregistrován jako identifikátor URI pro odhlášení v registraci vaší aplikace.

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByUsername(username)
}

myMsal.logout(logoutRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, který získá [token pro aplikaci](scenario-spa-acquire-token.md).