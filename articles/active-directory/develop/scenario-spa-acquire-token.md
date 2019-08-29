---
title: Jednostránkové aplikace (získání tokenu pro volání rozhraní API) – Microsoft Identity Platform
description: Naučte se vytvářet jednostránkové aplikace (získat token pro volání rozhraní API).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135715"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Jednostránkové aplikace – získání tokenu pro volání rozhraní API

Vzor pro získání tokenů pro rozhraní API pomocí MSAL. js slouží jako první pokus o požadavek na pasivní token pomocí `acquireTokenSilent` metody. Při volání této metody knihovna nejprve zkontroluje mezipaměť v úložišti prohlížeče, aby viděli, zda existuje platný token a vrátí jej. Pokud v mezipaměti není žádný platný token, pošle se požadavek na bezobslužný token Azure Active Directory (Azure AD) ze skrytého prvku IFRAME. Tato metoda také umožňuje, aby knihovna obnovila tokeny. Další informace o relaci jednotného přihlašování a hodnotách životnosti tokenů ve službě Azure AD najdete v tématu [Životnost tokenů](active-directory-configurable-token-lifetimes.md).

Požadavky na tiché tokeny do služby Azure AD mohou selhat z některých důvodů, jako je například relace Azure AD s vypršenou platností nebo změna hesla. V takovém případě můžete vyvolat jednu z interaktivních metod (s výzvou uživatele) získat tokeny.

* [Získání tokenu pomocí překryvného okna](#acquire-token-with-a-pop-up-window) pomocí`acquireTokenPopup`
* [Získání tokenu pomocí přesměrování](#acquire-token-with-redirect)`acquireTokenRedirect`

**Volba mezi prostředím pro místní nebo přesměrování**

 V aplikaci nelze použít kombinaci metod pro místní i přesměrování. Volba mezi zobrazováním překryvných nebo přesměrování závisí na toku aplikace.

* Pokud nechcete, aby uživatel přešel ze stránky vaší hlavní aplikace během ověřování, doporučuje se používat místní metody. Vzhledem k tomu, že se přesměrování ověřování provádí v překryvném okně, zůstane zachován stav hlavní aplikace.

* V některých případech může být nutné použít metody přesměrování. Pokud mají uživatelé vaší aplikace omezení nebo zásady prohlížeče, kde jsou automaticky otevíraná okna zakázaná, můžete použít metody přesměrování. Doporučujeme také použít metody přesměrování v prohlížeči Internet Explorer, protože při zpracování automaticky otevíraných oken dochází k určitým [známým problémům s Internet Explorerem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) .

Můžete nastavit rozsahy rozhraní API, které má přístupový token zahrnout při vytváření žádosti o přístupový token. Všimněte si, že se v přístupovém tokenu nemůžou udělit všechny požadované obory a závisí na tom, jestli uživatel souhlasí.

## <a name="acquire-token-with-a-pop-up-window"></a>Získání tokenu pomocí překryvného okna

### <a name="javascript"></a>JavaScript

Výše uvedený vzor pomocí metod pro místní prostředí:

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

Úhlová obálka MSAL poskytuje pohodlí při přidávání zachytávací protokolem HTTP, který automaticky získá přístupové tokeny a připojí je k požadavkům HTTP na rozhraní API.

V `protectedResourceMap` možnosti konfigurace můžete zadat obory pro rozhraní API, které bude MsalInterceptor vyžadovat při automatickém získávání tokenů.

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

V případě úspěchu a selhání získání tichého tokenu MSAL úhlové zpětné volání, které se můžete přihlásit k odběru. Je také důležité pamatovat si předplatné.

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

Alternativně můžete také explicitně získat tokeny pomocí metod získat token, jak je popsáno v základní knihovně MSAL. js.

## <a name="acquire-token-with-redirect"></a>Získání tokenu pomocí přesměrování

### <a name="javascript"></a>JavaScript

Vzor je popsaný výše, ale zobrazuje se s metodou přesměrování k interaktivnímu získání tokenu. Bude nutné zaregistrovat zpětné volání přesměrování, jak je uvedeno výše.

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

## <a name="request-for-optional-claims"></a>Požadavek na volitelné deklarace identity
V aplikaci si můžete vyžádat volitelné deklarace identity a určit tak další deklarace identity, které se mají zahrnout do tokenů pro vaši aplikaci. Aby bylo možné vyžádat volitelné deklarace identity ve službě id_token, můžete do pole claimsRequest třídy AuthenticationParameters. TS odeslat objekt deklarací dokument.

Pro následující účely můžete použít volitelné deklarace identity:

- Chcete-li zahrnout další deklarace identity v tokenech pro vaši aplikaci.
- Změna chování určitých deklarací identity, které Azure AD vrací v tokenech
- Přidejte a získejte přístup k vlastním deklaracím pro vaši aplikaci.


### <a name="javascript"></a>JavaScript
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
Další informace o volitelných deklaracích, rezervace [volitelných deklarací identity](active-directory-optional-claims.md)


### <a name="angular"></a>Úhlová

To je stejné, jak je popsáno výše.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-spa-call-api.md)
