---
title: Přihlášení k samoobslužné aplikaci & přihlašování – Microsoft Identity Platform | Azure
description: Informace o tom, jak vytvořit jednostránkovou aplikaci (přihlášení)
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
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb75aa53051e7e3c424ffe131cda61324fe86b1a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159960"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Jednostránkové aplikace: přihlášení a odhlášení

Naučte se, jak přidat přihlášení k kódu pro aplikaci s jednou stránkou.

Než budete moct získat tokeny pro přístup k rozhraním API ve vaší aplikaci, potřebujete kontext ověřeného uživatele. Uživatele můžete do aplikace v MSAL. js přihlašovat dvěma způsoby:

* [Automaticky otevírané okno](#sign-in-with-a-pop-up-window)pomocí metody `loginPopup`
* [Přesměrování](#sign-in-with-redirect)pomocí metody `loginRedirect`

Volitelně můžete také předávat obory rozhraní API, pro které budete potřebovat souhlas uživatele v době přihlášení.

> [!NOTE]
> Pokud má vaše aplikace již přístup k kontextu ověřeného uživatele nebo ID tokenu, můžete přeskočit krok přihlášení a přímo získat tokeny. Podrobnosti najdete v tématu [SSO bez přihlášení MSAL. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Volba mezi prostředím pro místní nebo přesměrování

V aplikaci nemůžete použít metody automaticky otevíraných oken a přesměrování. Volba mezi zobrazováním překryvných nebo přesměrování závisí na vašem toku aplikace:

* Pokud nechcete, aby se uživatelé během ověřování přesunuli ze stránky hlavní aplikace, doporučujeme použít místní metodu. Vzhledem k tomu, že k přesměrování ověřování dojde v překryvném okně, zůstane zachován stav hlavní aplikace.

* Pokud uživatelé mají omezení nebo zásady prohlížeče, kde jsou automaticky otevíraná okna zakázaná, můžete použít metodu přesměrování. Použijte metodu přesměrování v prohlížeči Internet Explorer, protože u [automaticky otevíraných oken v aplikaci Internet Explorer jsou známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Přihlášení pomocí automaticky otevíraného okna

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

MSALá obálka umožňuje zabezpečit konkrétní trasy ve vaší aplikaci přidáním `MsalGuard` do definice trasy. Tato ochrana vyvolá metodu pro přihlášení k této trase.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Pro Automatické zobrazování oken povolte možnost konfigurace `popUp`. Obory, které vyžadují souhlas, můžete také předat následujícím způsobem:

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
---

## <a name="sign-in-with-redirect"></a>Přihlášení pomocí přesměrování

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Metody přesměrování nevrací příslib z důvodu přesunu z hlavní aplikace. Aby bylo možné zpracovat a přistupovat ke vráceným tokenům, je nutné před voláním metod přesměrování zaregistrovat úspěšné a zpětné volání chyb.

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

Kód je stejný, jak je popsáno výše v části o přihlášení pomocí překryvného okna. Výchozím tokem je přesměrování.

> [!NOTE]
> Token ID neobsahuje odsouhlasené obory a reprezentuje pouze ověřeného uživatele. Přijaté obory se vrátí v přístupovém tokenu, který získáte v dalším kroku.

---

## <a name="sign-out"></a>Odhlášení

Knihovna MSAL poskytuje metodu `logout`, která vymaže mezipaměť v prohlížeči úložiště a pošle žádost o odhlášení do Azure Active Directory (Azure AD). Po odhlášení se knihovna přesměruje zpátky na úvodní stránku aplikace ve výchozím nastavení.

Můžete nakonfigurovat identifikátor URI, na který se má přesměrovat po odhlášení, nastavením `postLogoutRedirectUri`. Tento identifikátor URI by měl být také zaregistrován jako identifikátor URI pro odhlášení v registraci vaší aplikace.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

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

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získání tokenu pro aplikaci](scenario-spa-acquire-token.md)
