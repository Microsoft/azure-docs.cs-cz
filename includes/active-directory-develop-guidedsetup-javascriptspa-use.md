---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 4502799f3125ea5207dde70704e00105c763e8ce
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54193349"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Přihlásit uživatele pomocí knihovny Microsoft Authentication Library (MSAL)

1. Přidejte následující kód, který vaše `index.html` soubor `<script></script>` značky:

```javascript
//Pass null for default authority (https://login.microsoftonline.com/common)
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack,
    {storeAuthStateInCookie: true, cacheLocation: "localStorage"});

function signIn() {
    myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
        //Login Success
        showWelcomeMessage();
        acquireTokenPopupAndCallMSGraph();
    }, function (error) {
        console.log(error);
    });
}

function acquireTokenPopupAndCallMSGraph() {
    //Call acquireTokenSilent (iframe) to obtain a token for Microsoft Graph
    myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
        callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
    }, function (error) {
        console.log(error);
        // Call acquireTokenPopup (popup window) in case of acquireTokenSilent failure due to consent or interaction required ONLY
        if (error.indexOf("consent_required") !== -1 || error.indexOf("interaction_required") !== -1 || error.indexOf("login_required") !== -1) {
            myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
                callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
            }, function (error) {
                console.log(error);
            });
        }
    });
}

function graphAPICallback(data) {
    //Display user data on DOM
    var divWelcome = document.getElementById('WelcomeMessage');
    divWelcome.innerHTML += " to Microsoft Graph API!!";
    document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
}

function showWelcomeMessage() {
    var divWelcome = document.getElementById('WelcomeMessage');
    divWelcome.innerHTML += 'Welcome ' + myMSALObj.getUser().name;
    var loginbutton = document.getElementById('SignIn');
    loginbutton.innerHTML = 'Sign Out';
    loginbutton.setAttribute('onclick', 'signOut();');
}

// This function can be removed if you do not need to support IE
function acquireTokenRedirectAndCallMSGraph() {
    //Call acquireTokenSilent (iframe) to obtain a token for Microsoft Graph
    myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
      callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
    }, function (error) {
        console.log(error);
        //Call acquireTokenRedirect in case of acquireToken Failure
        if (error.indexOf("consent_required") !== -1 || error.indexOf("interaction_required") !== -1 || error.indexOf("login_required") !== -1) {
            myMSALObj.acquireTokenRedirect(applicationConfig.graphScopes);
        }
    });
}

function acquireTokenRedirectCallBack(errorDesc, token, error, tokenType) {
    if (tokenType === "access_token") {
        callMSGraph(applicationConfig.graphEndpoint, token, graphAPICallback);
    } else {
        console.log("token type is:"+tokenType);
    }
}


// Browser check variables
var ua = window.navigator.userAgent;
var msie = ua.indexOf('MSIE ');
var msie11 = ua.indexOf('Trident/');
var msedge = ua.indexOf('Edge/');
var isIE = msie > 0 || msie11 > 0;
var isEdge = msedge > 0;

//If you support IE, our recommendation is that you sign-in using Redirect APIs
//If you as a developer are testing using Microsoft Edge InPrivate mode, please add "isEdge" to the if check
if (!isIE) {
    if (myMSALObj.getUser()) {// avoid duplicate code execution on page load in case of iframe and popup window.
        showWelcomeMessage();
        acquireTokenPopupAndCallMSGraph();
    }
} else {
    document.getElementById("SignIn").onclick = function () {
        myMSALObj.loginRedirect(applicationConfig.graphScopes);
    };
    if (myMSALObj.getUser() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
        showWelcomeMessage();
        acquireTokenRedirectAndCallMSGraph();
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Po kliknutí **Sign In** tlačítko poprvé, `signIn` volání metody `loginPopup` k přihlášení uživatele. Tato metoda má za následek otevřete okno automaticky otevírané okno s *koncového bodu Microsoft Azure Active Directory v2.0* výzvu a ověření přihlašovacích údajů uživatele. V důsledku u úspěšné přihlášení, bude uživatel přesměrován zpět na původní *index.html* stránky a token pro přijetí zpracovány `msal.js` a informací obsažených v tokenu se uloží do mezipaměti. Tento token se označuje jako *ID token* a obsahuje základní informace o uživateli, jako je zobrazované jméno uživatele. Pokud máte v plánu používat data poskytuje tento token pro účely, musíte zajistit, aby že tento token je potvrzen v back-end serveru k zajištění, že byl token vydán platnému uživateli služby pro vaši aplikaci.

Jednostránková aplikace vygenerované to provede volání `acquireTokenSilent` a/nebo `acquireTokenPopup` získat *přístupový token* používá k dotazování na rozhraní Microsoft Graph API pro informace o profilu uživatele. Pokud potřebujete vzorku, který ověří ID token, podívejte se na [to](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "active-directory-javascript-singlepageapp-dotnet-webapi-v2 ukázky Githubu") ukázková aplikace v Githubu – Ukázka používá ASP .NET webového rozhraní API pro ověřování tokenů.

#### <a name="getting-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Po počátečním přihlášení, nechcete žádat uživatele nutnost opakovaného ověření příslušných pokaždé, když potřebují k vyžádání tokenu pro přístup k prostředku – tedy *acquireTokenSilent* by ve většině případů použít k získání tokenů. Existují však situace, že je potřeba vynutit uživatelům, aby komunikovali s koncovým bodem v2.0 Azure Active Directory – mezi příklady patří:

- Může být potřeba, aby uživatelé znovu zadali svoje přihlašovací údaje, protože vypršela platnost hesla
- Vaše aplikace žádá o přístup k prostředku, ke kterému musí dát uživatel souhlas
- Je nutné dvoufaktorové ověřování

Volání *acquireTokenPopup(scope)* výsledky v automaticky otevíraném okně (nebo *acquireTokenRedirect(scope)* výsledkem přesměrování uživatelů na koncový bod Azure Active Directory v2.0) Pokud uživatelé potřebují k interakce potvrzení přihlašovacích údajů, udělení souhlasu pro požadovaný prostředek, nebo dokončení dvoufaktorového ověřování.

#### <a name="getting-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

` acquireTokenSilent` Obsluhovala token pořízení a obnovení bez nutnosti zásahu uživatele. Po `loginPopup` (nebo `loginRedirect`) provádí poprvé, `acquireTokenSilent` je metoda běžně používá k získání tokenů použít pro přístup k chráněným prostředkům pro pozdější volání – jako volání na vyžádání nebo tokeny obnovení probíhají bezobslužně.
`acquireTokenSilent` nemusí v některých případech – třeba hesla vypršela. Vaše aplikace dokáže zpracovat tuto výjimku dvěma způsoby:

1. Volání `acquireTokenPopup` okamžitě, jehož výsledkem výzvy k přihlášení. Tento model se běžně používá v online aplikace tam, kde není žádná neověřená obsah v aplikaci k dispozici pro uživatele. Ukázka vygeneroval tento instalační program s asistencí používá tento model.

2. Aplikace lze také nastavit vizuální označení pro uživatele, který interaktivnímu přihlášení je nutné, takže uživatel může vybrat správný čas pro přihlášení, nebo aplikace může pokus zopakovat, `acquireTokenSilent` později. To se běžně používá, když uživatel může používat další funkce aplikace bez narušení – například je neověřené obsah k dispozici v aplikaci. V takovém případě se uživatel může rozhodnout, pokud chtějí přihlášení pro přístup k chráněnému prostředku nebo aktualizujte zastaralé informace.

> [!NOTE]
> Výše uvedený kód používá `loginRedirect` a `acquireTokenRedirect` metody, když se používá prohlížeč je aplikace Internet Explorer z důvodu [známý problém](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) souvisejících s zpracování zobrazována místní okna v prohlížeči Internet Explorer.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Volání rozhraní Microsoft Graph API pomocí tokenu, který jste získali

Přidejte následující kód, který vaše `index.html` soubor `<script></script>` značky:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Další informace o provádění volání REST chráněné rozhraní API

V ukázkové aplikaci vytvořil tento průvodce `callMSGraph()` aby protokolu HTTP se používá metoda `GET` požadavku na chráněný prostředek, který se vyžaduje token a potom vrátí obsah volajícímu. Tato metoda přidá tokenu získaného v *hlavičku protokolu HTTP ověřování*. Pro ukázkovou aplikaci vytvořené v této příručce, prostředek je v rozhraní Microsoft Graph API *mě* koncový bod – zobrazí informace o profilu uživatele.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Přidejte metodu k neodhlásí uživatele

Přidejte následující kód, který vaše `index.html` soubor `<script></script>` značky:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```
