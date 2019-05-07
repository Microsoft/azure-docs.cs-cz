---
title: Jednostránková aplikace (získání tokenu pro volání rozhraní API) - platforma identit Microsoft
description: Další informace o vytváření jednostránkové aplikace (získání tokenu pro volání rozhraní API)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138822"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Jednostránková aplikace – získání tokenu pro volání rozhraní API

Vzor pro získávání tokenů pro rozhraní API pomocí MSAL.js, je nejprve pokusí tiché žádosti o token pomocí `acquireTokenSilent` metody. Když tato metoda je volána, knihovně nejprve zkontroluje mezipaměti v prohlížeči úložiště existuje platný token a vrátí jej. Když neexistuje žádný platný token v mezipaměti, odešle ze skrytých iframe tiché žádosti o token do služby Azure Active Directory (Azure AD). Tato metoda také umožňuje knihovna k obnovení tokeny. Další podrobnosti o relaci přihlášení a hodnot životnosti tokenu ve službě Azure AD najdete v tématu [token doby života](active-directory-configurable-token-lifetimes.md).

Tiché žádostí o token do služby Azure AD mohou být neúspěšné pro několik příčin, jako je Azure vypršela platnost relace AD nebo změnu hesla. V takovém případě můžete vyvolat jeden interaktivních metod (které se zobrazí výzva) k získání tokenů.

* [Získat token se automaticky otevírané okno](#acquire-token-with-a-pop-up-window) pomocí `acquireTokenPopup`
* [Získání tokenu s přesměrování](#acquire-token-with-redirect) pomocí `acquireTokenRedirect`

**Volba mezi automaticky otevírané okno nebo přesměrovat prostředí**

 Kombinace metod automaticky otevírané okno i přesměrování nelze použít ve vaší aplikaci. Volba mezi automaticky otevírané okno nebo přesměrovat prostředí závisí na vaší aplikace flow.

* Pokud nechcete, aby uživatel se přejít mimo vaše hlavní aplikace stránky během ověřování, doporučujeme použít místní metody. Protože ověřování přesměrování se děje v automaticky otevírané okno, stav hlavní aplikace se zachová.

* Existují určité případy, kde je nutné použít metody přesměrování. Pokud uživatelé vaší aplikace mají omezení prohlížeče nebo zásady níž jsou automaticky otevíraná okna windows zakázány, můžete použít metody přesměrování. Také se doporučuje používat metody přesměrování se prohlížeč Internet Explorer, protože existují určité [známé problémy s aplikací Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) při zpracování automaticky otevíraná okna.

Můžete nastavit obory API, které chcete zahrnout při sestavování žádost o přístupový token přístupový token. Všimněte si, že všechny požadované obory nemusí být udělena v přístupovém tokenu a závisí na vyjádření souhlasu uživatele.

## <a name="acquire-token-with-a-pop-up-window"></a>Získat token se automaticky otevírané okno

### <a name="javascript"></a>JavaScript

Výše uvedené způsob použití metod pro místní prostředí:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
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

### <a name="angular"></a>Úhlová

Knihovna MSAL Angular obálky zvyšuje pohodlí přidání zachycování HTTP `MsalInterceptor` která bude automaticky tiše získat přístupové tokeny a připojit je na požadavky HTTP do rozhraní API.

Můžete určit obory pro rozhraní API v `protectedResourceMap` možnost konfigurace, která MsalInterceptor bude požadovat, když automaticky získávání tokenů.

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

Pro úspěchy a chyby tiché získání tokenu poskytuje knihovna MSAL Angular zpětná volání, které se můžete přihlásit k odběru. Taky je dobré si uvědomit k odhlášení odběru.

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

Alternativně můžete také explicitně získat tokeny pomocí metody získání tokenu, jak je popsáno v základní knihovně MSAL.js.

## <a name="acquire-token-with-redirect"></a>Získání tokenu pomocí přesměrování

### <a name="javascript"></a>JavaScript

Vzor je popsané výše ale zobrazených metodou přesměrovat k získání tokenu interaktivně. Všimněte si, že budete muset registrovat zpětné volání pro přesměrování, jak je uvedeno výše.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

### <a name="angular"></a>Úhlová

To je stejný, jak je popsáno výše.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-spa-call-api.md)
