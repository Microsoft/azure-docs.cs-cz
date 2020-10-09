---
title: Získání tokenu pro volání webového rozhraní API (jednostránkové aplikace) – Microsoft Identity Platform | Azure
description: Naučte se vytvářet jednostránkové aplikace (získat token pro volání rozhraní API).
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: eeba01a609a1a21ed564c0b9cb78a28a4ad5c95a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80882314"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Jednostránkové aplikace: získání tokenu pro volání rozhraní API

Vzor pro získání tokenů pro rozhraní API s MSAL.js je prvním pokusem o požadavek na pasivní token pomocí `acquireTokenSilent` metody. Při volání této metody knihovna nejprve zkontroluje mezipaměť v úložišti prohlížeče a zjistí, zda existuje platný token a vrátí jej. Pokud není v mezipaměti žádný platný token, pošle se požadavek na bezobslužný token Azure Active Directory (Azure AD) ze skrytého prvku IFRAME. Tato metoda také umožňuje, aby knihovna obnovila tokeny. Další informace o relaci jednotného přihlašování a hodnotách životnosti tokenů ve službě Azure AD najdete v tématu [Životnost tokenů](active-directory-configurable-token-lifetimes.md).

Požadavky na tiché tokeny do služby Azure AD mohou selhat z důvodů, jako je například relace Azure AD s vypršenou platností nebo změna hesla. V takovém případě můžete vyvolat jednu z interaktivních metod (s výzvou uživateli) získat tokeny:

* [Automaticky otevírané okno](#acquire-a-token-with-a-pop-up-window)pomocí `acquireTokenPopup`
* [Přesměrování](#acquire-a-token-with-a-redirect)pomocí `acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Volba mezi prostředím pro místní nebo přesměrování

 V aplikaci nemůžete použít metody automaticky otevíraných oken a přesměrování. Volba mezi zobrazováním překryvných nebo přesměrování závisí na vašem toku aplikace:

* Pokud nechcete, aby se uživatelé během ověřování přesunuli ze stránky hlavní aplikace, doporučujeme místní metodu. Vzhledem k tomu, že k přesměrování ověřování dojde v překryvném okně, zůstane zachován stav hlavní aplikace.

* Pokud mají uživatelé omezení nebo zásady prohlížeče, kde jsou automaticky otevíraná okna zakázaná, můžete použít metodu přesměrování. Použijte metodu přesměrování v prohlížeči Internet Explorer, protože u [automaticky otevíraných oken v aplikaci Internet Explorer jsou známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Můžete nastavit rozsahy rozhraní API, které má přístupový token zahrnout při vytváření žádosti o přístupový token. Všimněte si, že v přístupovém tokenu nemusí být udělené všechny požadované obory. To závisí na souhlasu uživatele.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Získání tokenu v překryvném okně

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující kód kombinuje dříve popsaný vzor s metodami pro místní prostředí:

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

MSAL úhlová obálka poskytuje zachytávací protokolem HTTP, která automaticky získá přístupové tokeny a připojí je k požadavkům HTTP na rozhraní API.

V možnosti konfigurace můžete zadat obory pro rozhraní API `protectedResourceMap` . `MsalInterceptor` vyžádá si tyto obory při automatickém získávání tokenů.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

V případě úspěchu a neúspěšného získání tichého tokenu poskytuje MSALa zpětná volání, ke kterým se můžete přihlásit. Je také důležité pamatovat si předplatné.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Případně můžete explicitně získat tokeny pomocí metod získání tokenu, jak je popsáno v základní MSAL.js knihovně.

---

## <a name="acquire-a-token-with-a-redirect"></a>Získání tokenu pomocí přesměrování

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující vzor je popsaný výše, ale zobrazený s metodou přesměrování pro interaktivní získávání tokenů. Bude nutné zaregistrovat zpětné volání přesměrování, jak bylo zmíněno dříve.

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

## <a name="request-optional-claims"></a>Vyžadovat volitelné deklarace identity

Volitelné deklarace identity můžete použít pro následující účely:

- Zahrnout další deklarace identity v tokenech pro vaši aplikaci.
- Změna chování určitých deklarací identity, které Azure AD vrací v tokenech
- Přidejte a získejte přístup k vlastním deklaracím pro vaši aplikaci.

Chcete-li vyžádat volitelné deklarace identity v nástroji `IdToken` , můžete odeslat objekt deklarací dokument do `claimsRequest` pole `AuthenticationParameters.ts` třídy.

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

Další informace najdete v tématu [volitelné deklarace identity](active-directory-optional-claims.md).

# <a name="angular"></a>[Angular](#tab/angular)

Tento kód je stejný, jak je popsáno výše.

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-spa-call-api.md)
