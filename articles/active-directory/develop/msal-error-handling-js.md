---
title: Zpracování chyb a výjimek v MSAL.js
titleSuffix: Microsoft identity platform
description: Naučte se zpracovávat chyby a výjimky, problémy s deklarací identity podmíněného přístupu a opakované pokusy v MSAL.jsch aplikacích.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761345"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>Zpracování chyb a výjimek v MSAL.js

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>Zpracování chyb v MSAL.js

MSAL.js poskytuje objekty chyb, které abstraktní a klasifikují různé typy běžných chyb. Poskytuje také rozhraní pro přístup k určitým podrobnostem o chybách, jako jsou například chybové zprávy, aby je správně spolupracovaly.

### <a name="error-object"></a>Error – objekt

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Rozšířením třídy Error máte přístup k následujícím vlastnostem:
- `AuthError.message`: Totéž jako `errorMessage` .
- `AuthError.stack`: Trasování zásobníku pro vyvolané chyby.

### <a name="error-types"></a>Typy chyb

K dispozici jsou následující typy chyb:

- `AuthError`: Základní třída Error pro knihovnu MSAL.js, která se používá také pro neočekávané chyby.

- `ClientAuthError`: Error – třída, která označuje problém s ověřením klienta. Většina chyb, které pocházejí z knihovny, bude ClientAuthErrors. Tyto chyby jsou výsledkem akcí, jako je volání metody Login v případě, že přihlášení již probíhá, uživatel zruší přihlášení atd.

- `ClientConfigurationError`: Error Class, je `ClientAuthError` vyvoláno před provedením požadavků, pokud jsou zadané parametry konfigurace uživatele poškozené nebo chybí.

- `ServerError`: Error Class, představuje chybové řetězce odeslané ověřovacím serverem. Můžou to být chyby, jako jsou neplatné formáty nebo parametry požadavků nebo jakékoli jiné chyby, které brání serveru v ověřování nebo autorizaci uživatele.

- `InteractionRequiredAuthError`: Error Class, rozšiřuje, `ServerError` aby představovala chyby serveru, které vyžadují interaktivní volání. Tato chyba je vyvolána, `acquireTokenSilent` Pokud je uživatel nutný k interakci se serverem za účelem poskytnutí pověření nebo souhlasu pro ověřování/autorizaci. Kódy chyb zahrnují `"interaction_required"` , `"login_required"` a `"consent_required"` .

Pro zpracování chyb v tokůch ověřování pomocí metod přesměrování ( `loginRedirect` , `acquireTokenRedirect` ) bude nutné zaregistrovat zpětné volání, které je voláno s funkcí úspěch nebo neúspěch po přesměrování pomocí `handleRedirectCallback()` metody, následovně:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Metody pro místní prostředí ( `loginPopup` , `acquireTokenPopup` ) vracejí příslibů, takže můžete použít vzor Promise (. then a. catch) k jejich zpracování, jak je znázorněno níže:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Chyby, které vyžadují interakci

Při pokusu o použití neinteraktivní metody získání tokenu, jako je například `acquireTokenSilent` , ale MSAL by nedokázala tuto chybu provést v tichém režimu, se vrátí chyba.

Možné důvody:

- musíte se přihlásit.
- musíte souhlasit.
- musíte projít prostředím Multi-Factor Authentication.

Nápravou je zavolat interaktivní metodu, například `acquireTokenPopup` nebo `acquireTokenRedirect` :

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Při tichém získávání tokenů (pomocí `acquireTokenSilent` ) pomocí MSAL.js může vaše aplikace obdržet chyby, [](../azuread-dev/conditional-access-dev-guide.md) když rozhraní API, ke kterému se snažíte získat přístup, vyžaduje zásadu MFA, jako je například zásada MFA.

Vzor pro zpracování této chyby je provést interaktivní volání metody získat token v MSAL.js, jako je například `acquireTokenPopup` nebo `acquireTokenRedirect` jako v následujícím příkladu:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

K interaktivnímu získání tokenu se uživateli zobrazí výzva a dává jim možnost splnit požadované zásady podmíněného přístupu.

Při volání rozhraní API, které vyžaduje podmíněný přístup, můžete z rozhraní API obdržet výzvu s deklarací identity. V takovém případě můžete předat deklarace vracené v chybě do `claimsRequest` pole `AuthenticationParameters.ts` třídy, aby splňovala příslušné zásady. 

Další informace najdete v tématu [vyžádání dalších deklarací identity](active-directory-optional-claims.md) .


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Další kroky

Zvažte možnost povolit [protokolování MSAL.js](msal-logging-js.md) , které vám pomůžou diagnostikovat a ladit problémy.
