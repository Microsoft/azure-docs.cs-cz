---
title: Jednostránkové aplikace (přihlášení) – Microsoft Identity Platform
description: Naučte se vytvářet jednostránkové aplikace (přihlásit se).
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
ms.openlocfilehash: 7bf614a5523e78fc72918db973ef8d738a171fff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031776"
---
# <a name="single-page-application---sign-in"></a>Jednostránkové aplikace – přihlášení

Naučte se, jak přidat přihlašování k kódu pro aplikaci s jednou stránkou.

Než budete moct získat tokeny pro přístup k rozhraním API ve vaší aplikaci, budete potřebovat kontext ověřeného uživatele. Uživatele můžete do aplikace v MSAL. js přihlašovat dvěma způsoby:

* [Přihlaste se pomocí automaticky otevíraného okna](#sign-in-with-a-pop-up-window) pomocí `loginPopup` metody.
* [Přihlaste se](#sign-in-with-redirect) pomocí `loginRedirect` metody Redirect

Volitelně můžete také předávat obory rozhraní API, pro které budete potřebovat souhlas uživatele v době přihlášení.

> [!NOTE]
> Pokud má vaše aplikace již přístup k kontextu ověřeného uživatele nebo ID tokenu, můžete přeskočit krok přihlášení a přímo získat tokeny. Další podrobnosti najdete v tématu [SSO bez přihlášení msal. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Volba mezi prostředím pro místní nebo přesměrování

V aplikaci nelze použít kombinaci metod pro místní i přesměrování. Volba mezi zobrazováním překryvných nebo přesměrování závisí na toku aplikace.

* Pokud nechcete, aby uživatel přešel ze stránky vaší hlavní aplikace během ověřování, doporučuje se používat místní metody. Vzhledem k tomu, že se přesměrování ověřování provádí v překryvném okně, zůstane zachován stav hlavní aplikace.

* V některých případech může být nutné použít metody přesměrování. Pokud mají uživatelé vaší aplikace omezení nebo zásady prohlížeče, kde jsou automaticky otevíraná okna zakázaná, můžete použít metody přesměrování. Použijte metody přesměrování v prohlížeči Internet Explorer, protože při zpracování překryvných oken dochází k určitým [známým problémům s Internet Explorerem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) .

## <a name="sign-in-with-a-pop-up-window"></a>Přihlaste se pomocí automaticky otevíraného okna.

### <a name="javascript"></a>JavaScript

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

### <a name="angular"></a>Úhlová

Úhlová obálka MSAL umožňuje zabezpečit konkrétní trasy ve vaší aplikaci pouhým přidáním `MsalGuard` do definice trasy. Tato ochrana vyvolá metodu pro přihlášení k této trase.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Pro Automatické zobrazování oken povolte `popUp` možnost konfigurace. Obory, které vyžadují souhlas, můžete také předat následujícím způsobem:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Přihlásit se pomocí přesměrování

### <a name="javascript"></a>JavaScript

Metody přesměrování nevrací příslib z důvodu navigace mimo hlavní aplikaci. Chcete-li zpracovat vrácené tokeny a přistupovat k nim, bude nutné před voláním metod přesměrování zaregistrovat úspěšné a zpětná volání chyb.

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

### <a name="angular"></a>Úhlová

Kód je stejný, jak je popsáno výše v části přihlášení pomocí místního okna. Výchozím tokem je přesměrování.

> [!NOTE]
> Token ID neobsahuje odsouhlasené obory a reprezentuje pouze ověřeného uživatele. Přijaté obory se vrátí v přístupovém tokenu, který získáte v dalším kroku.

## <a name="sign-out"></a>Odhlásit se

Knihovna MSAL poskytuje `logout` metodu, která smaže mezipaměť v úložišti prohlížeče a pošle žádost o odhlášení do Azure AD. Po odhlášení se přesměruje zpátky na úvodní stránku aplikace ve výchozím nastavení.

Můžete nakonfigurovat identifikátor URI, na který se má přesměrovat po odhlášení, `postLogoutRedirectUri`nastavením. Tento identifikátor URI by měl být také zaregistrován jako identifikátor URI pro odhlášení v registraci vaší aplikace.

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
