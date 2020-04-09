---
title: Přihlášení k jednostránkové aplikaci & odhlášení – platforma microsoft identity | Azure
description: Přečtěte si, jak vytvořit jednostránkovou aplikaci (přihlášení)
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
ms.openlocfilehash: 7e809def048c95b6688a13ac99783615eb045d11
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885185"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Jednostránková aplikace: Přihlášení a odhlášení

Přečtěte si, jak přidat přihlášení ke kódu jednostránkové aplikace.

Než budete moci získat tokeny pro přístup k rozhraní API ve vaší aplikaci, budete potřebovat kontext ověřeného uživatele. Uživatelé můžete přihlásit k aplikaci v MSAL.js dvěma způsoby:

* [Rozbalovací okno](#sign-in-with-a-pop-up-window)pomocí `loginPopup` metody
* [Přesměrování](#sign-in-with-redirect)pomocí `loginRedirect` metody

Můžete také volitelně předat obory rozhraní API, pro které potřebujete, aby uživatel souhlasil v době přihlášení.

> [!NOTE]
> Pokud vaše aplikace již má přístup k kontextu ověřeného uživatele nebo tokenu ID, můžete přeskočit krok přihlášení a přímo získat tokeny. Podrobnosti naleznete v [tématu SSO bez přihlášení MSAL.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Výběr mezi automaticky otevíranou nebo přesměrovací masovou

V aplikaci nelze použít metody automaticky otevíraných i přesměrovacích adres. Volba mezi automaticky otevírané nebo přesměrovací prostředí závisí na toku aplikace:

* Pokud nechcete, aby se uživatelé během ověřování vzdalovali od hlavní stránky aplikace, doporučujeme metodu automaticky otevíraných míst. Vzhledem k tomu, že přesměrování ověřování se stane v automaticky otevírané okno, stav hlavní aplikace je zachována.

* Pokud mají uživatelé omezení prohlížeče nebo zásady, kde jsou zakázána automaticky otevíraná okna, můžete použít metodu přesměrování. Metodu přesměrování použijte v prohlížeči Internet Explorer, protože jsou [v aplikaci Internet Explorer známy problémy s automaticky otevíranými okny](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Přihlášení pomocí automaticky otevíraných oken

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

# <a name="angular"></a>[Úhlová](#tab/angular)

Obálka MSAL Angular umožňuje zabezpečit konkrétní trasy v `MsalGuard` aplikaci přidáním do definice trasy. Tento stráž vyvolá metodu pro přihlášení při přístupu k této trase.

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

Pro prostředí automaticky otevíraných `popUp` oken povolte možnost konfigurace. Můžete také předat obory, které vyžadují souhlas takto:

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
            consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Přihlášení s přesměrováním

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Metody přesměrování nevrátí slib z důvodu přechodu od hlavní aplikace. Chcete-li zpracovat a získat přístup k vrácené tokeny, je třeba zaregistrovat úspěch a zpětná volání chyb před voláním metody přesměrování.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Úhlová](#tab/angular)

Kód je zde stejný, jak je popsáno výše v části o přihlášení s automaticky otevíraným oknem. Výchozí tok je přesměrování.

> [!NOTE]
> Token ID neobsahuje sypké obory a představuje pouze ověřeného uživatele. Souhlasné obory jsou vráceny v přístupovém tokenu, který získáte v dalším kroku.

---

## <a name="sign-out"></a>Odhlášení

Knihovna MSAL `logout` poskytuje metodu, která vymaže mezipaměť v úložišti prohlížeče a odešle požadavek na odhlášení do služby Azure Active Directory (Azure AD). Po odhlášení knihovna ve výchozím nastavení přesměruje zpět na úvodní stránku aplikace.

Identifikátor URI, do kterého by měl být po `postLogoutRedirectUri`odhlášení přesměrován, můžete nakonfigurovat nastavením . Tento identifikátor URI by měl být také registrován jako identifikátor URI při registraci aplikace.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

# <a name="angular"></a>[Úhlová](#tab/angular)

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

> [!div class="nextstepaction"]
> [Získání tokenu pro aplikaci](scenario-spa-acquire-token.md)
