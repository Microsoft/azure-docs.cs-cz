---
title: Získání tokenu pro volání webového rozhraní API (jednostránkové aplikace) – platforma identit Microsoftu | Azure
description: Zjistěte, jak vytvořit jednostránkovou aplikaci (získat token pro volání rozhraní API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: d5d48a2fc7aca184cf8b6e7761584a8800ca5151
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160062"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Jednostránková aplikace: Získání tokenu pro volání rozhraní API

Vzor pro získání tokenů pro api s MSAL.js je nejprve pokus `acquireTokenSilent` o požadavek tichý token pomocí metody. Při volání této metody knihovna nejprve zkontroluje mezipaměť v úložišti prohlížeče, aby zjistila, zda existuje platný token a vrátí jej. Pokud žádný platný token je v mezipaměti, odešle požadavek na tichý token do služby Azure Active Directory (Azure AD) ze skrytého prvku iframe. Tato metoda také umožňuje knihovně obnovit tokeny. Další informace o jedné přihlašování relace a token u disponuální životnost i ve službě Azure AD, najdete v [tématu token životnosti](active-directory-configurable-token-lifetimes.md).

Požadavky na tichý token pro Azure AD může selhat z důvodů, jako je vypršela platnost relace Azure AD nebo změna hesla. V takovém případě můžete vyvolat jednu z interaktivních metod (které vyzve uživatele) k získání tokenů:

* [Automaticky otevírané okno](#acquire-a-token-with-a-pop-up-window), pomocí`acquireTokenPopup`
* [Přesměrování](#acquire-a-token-with-a-redirect), pomocí`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Výběr mezi automaticky otevíranou nebo přesměrovací

 V aplikaci nelze použít metody automaticky otevíraných i přesměrovacích adres. Volba mezi automaticky otevírané nebo přesměrovací prostředí závisí na toku aplikace:

* Pokud nechcete, aby se uživatelé během ověřování vzdalovali od hlavní stránky aplikace, doporučujeme metodu automaticky otevíraných míst. Vzhledem k tomu, že přesměrování ověřování se stane v automaticky otevírané okno, stav hlavní aplikace je zachována.

* Pokud mají uživatelé omezení prohlížeče nebo zásady, kde jsou zakázána automaticky otevíraná okna, můžete použít metodu přesměrování. Metodu přesměrování použijte v prohlížeči Internet Explorer, protože jsou [v aplikaci Internet Explorer známy problémy s automaticky otevíranými okny](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Můžete nastavit obory rozhraní API, které chcete, aby přístupový token zahrnoval při vytváření požadavku na přístupový token. Všimněte si, že všechny požadované obory nemusí být uděleny v přístupovém tokenu. To závisí na souhlasu uživatele.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Získání tokenu s automaticky otevíraným oknem

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující kód kombinuje dříve popsaný vzor s metodami pro vyskakovací prostředí:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

Obálka MSAL Angular poskytuje zachycovač HTTP, který automaticky získá přístupové tokeny tiše a připojí je k požadavkům HTTP k souborům API.

Obory pro api můžete zadat `protectedResourceMap` v možnosti konfigurace. `MsalInterceptor`bude požadovat tyto obory při automatickém získávání tokenů.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

Pro úspěch a neúspěch silent token akvizice MSAL Angular poskytuje zpětná volání, které můžete přihlásit k odběru. Je také důležité mít na paměti, odhlásit.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternativně můžete explicitně získat tokeny pomocí metody získat tokeny, jak je popsáno v základní knihovně MSAL.js.

---

## <a name="acquire-a-token-with-a-redirect"></a>Získání tokenu s přesměrováním

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující vzor je popsáno dříve, ale zobrazí se s metodou přesměrování získat tokeny interaktivně. Budete muset zaregistrovat zpětné volání přesměrování, jak bylo uvedeno výše.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Požadovat volitelné deklarace identity

Volitelné deklarace identity můžete použít pro následující účely:

- Zahrnout další deklarace identity v tokenech pro vaši aplikaci.
- Změňte chování určitých deklarací, které Azure AD vrátí v tokenech.
- Přidejte a získejte přístup k vlastním deklaracím identity pro vaši aplikaci. 

Chcete-li požádat o volitelné deklarace identity v aplikace `IdToken`, můžete odeslat objekt stringified claims do `claimsRequest` pole `AuthenticationParameters.ts` třídy.

```javascript
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

Další informace naleznete v [tématu Volitelné deklarace identity](active-directory-optional-claims.md).

# <a name="angular"></a>[Angular](#tab/angular)

Tento kód je stejný, jak je popsáno výše.

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-spa-call-api.md)
