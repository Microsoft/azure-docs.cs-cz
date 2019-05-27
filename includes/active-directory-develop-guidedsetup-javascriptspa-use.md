---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8785f89a335f0ae7d983f267176da1656aee57a0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121836"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Přihlásit uživatele pomocí knihovny Microsoft Authentication Library (MSAL)

1. Přidejte následující kód, který vaše `index.html` soubor `<script></script>` značky:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };

    var graphConfig = {
        graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
    };

    // this can be used for login or token request, however in more complex situations
    // this can have diverging options
    var requestObj = {
        scopes: ["user.read"]
    };

    var myMSALObj = new Msal.UserAgentApplication(msalConfig);
    // Register Callbacks for redirect flow
    myMSALObj.handleRedirectCallback(authRedirectCallBack);


    function signIn() {

        myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
            //Login Success
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }).catch(function (error) {
            console.log(error);
        });
    }

    function acquireTokenPopupAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
        });
    }


    function graphAPICallback(data) {
        document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
    }


    function showWelcomeMessage() {
        var divWelcome = document.getElementById('WelcomeMessage');
        divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
        var loginbutton = document.getElementById('SignIn');
        loginbutton.innerHTML = 'Sign Out';
        loginbutton.setAttribute('onclick', 'signOut();');
    }


    //This function can be removed if you do not need to support IE
    function acquireTokenRedirectAndCallMSGraph() {
         //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
         myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
             callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
         }).catch(function (error) {
             console.log(error);
             // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
             // Call acquireTokenRedirect
             if (requiresInteraction(error.errorCode)) {
                 myMSALObj.acquireTokenRedirect(requestObj);
             }
         });
     }


    function authRedirectCallBack(error, response) {
        if (error) {
            console.log(error);
        }
        else {
            if (response.tokenType === "access_token") {
                callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
            } else {
                console.log("token type is:" + response.tokenType);
            }
        }
    }

    function requiresInteraction(errorCode) {
        if (!errorCode || !errorCode.length) {
            return false;
        }
        return errorCode === "consent_required" ||
            errorCode === "interaction_required" ||
            errorCode === "login_required";
    }

    // Browser check variables
    var ua = window.navigator.userAgent;
    var msie = ua.indexOf('MSIE ');
    var msie11 = ua.indexOf('Trident/');
    var msedge = ua.indexOf('Edge/');
    var isIE = msie > 0 || msie11 > 0;
    var isEdge = msedge > 0;
    //If you support IE, our recommendation is that you sign-in using Redirect APIs
    //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
    // can change this to default an experience outside browser use
    var loginType = isIE ? "REDIRECT" : "POPUP";

    if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
    }
    else if (loginType === 'REDIRECT') {
        document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
        };
        if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
    } else {
        console.error('Please set a valid login type');
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Po kliknutí **Sign In** tlačítko poprvé, `signIn` volání metody `loginPopup` k přihlášení uživatele. Tato metoda má za následek otevřete okno automaticky otevírané okno s *koncového bodu Microsoft identity platform* výzvu a ověření přihlašovacích údajů uživatele. V důsledku u úspěšné přihlášení, bude uživatel přesměrován zpět na původní *index.html* stránky a token pro přijetí zpracovány `msal.js` a informací obsažených v tokenu se uloží do mezipaměti. Tento token se označuje jako *ID token* a obsahuje základní informace o uživateli, jako je zobrazované jméno uživatele. Pokud máte v plánu používat data poskytuje tento token pro účely, musíte zajistit, aby že tento token je potvrzen v back-end serveru k zajištění, že byl token vydán platnému uživateli služby pro vaši aplikaci.

Jednostránková aplikace vygenerované to provede volání `acquireTokenSilent` a/nebo `acquireTokenPopup` získat *přístupový token* používá k dotazování na rozhraní Microsoft Graph API pro informace o profilu uživatele. Pokud potřebujete vzorku, který ověří ID token, podívejte se na [to](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "active-directory-javascript-singlepageapp-dotnet-webapi-v2 ukázky Githubu") ukázková aplikace v Githubu – Ukázka používá ASP .NET webového rozhraní API pro ověřování tokenů.

#### <a name="getting-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Po počátečním přihlášení, nechcete žádat uživatele nutnost opakovaného ověření příslušných pokaždé, když potřebují k vyžádání tokenu pro přístup k prostředku – tedy *acquireTokenSilent* by ve většině případů použít k získání tokenů. Existují však situace, že je potřeba vynutit uživatelům, aby komunikovali s koncovým bodem platforma identit Microsoft – mezi příklady patří:

- Může být potřeba, aby uživatelé znovu zadali svoje přihlašovací údaje, protože vypršela platnost hesla
- Vaše aplikace žádá o přístup k prostředku, ke kterému musí dát uživatel souhlas
- Je nutné dvoufaktorové ověřování

Volání *acquireTokenPopup* výsledky v automaticky otevíraném okně (nebo *acquireTokenRedirect* výsledkem přesměrování uživatelů na koncový bod Microsoft identity platform) Pokud uživatelé potřebují pracovat buď potvrzení své přihlašovací údaje, udělení souhlasu pro požadovaný prostředek nebo dokončení dvoufaktorového ověřování.

#### <a name="getting-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`acquireTokenSilent` Obsluhovala token pořízení a obnovení bez nutnosti zásahu uživatele. Po `loginPopup` (nebo `loginRedirect`) provádí poprvé, `acquireTokenSilent` je metoda běžně používá k získání tokenů použít pro přístup k chráněným prostředkům pro pozdější volání – jako volání na vyžádání nebo tokeny obnovení probíhají bezobslužně.
`acquireTokenSilent` nemusí v některých případech – třeba hesla vypršela. Vaše aplikace dokáže zpracovat tuto výjimku dvěma způsoby:

1. Volání `acquireTokenPopup` okamžitě, jehož výsledkem výzvy k přihlášení. Tento model se běžně používá v online aplikace tam, kde není žádná neověřená obsah v aplikaci k dispozici pro uživatele. Ukázka vygeneroval tento instalační program s asistencí používá tento model.

2. Aplikace lze také nastavit vizuální označení pro uživatele, který interaktivnímu přihlášení je nutné, takže uživatel může vybrat správný čas pro přihlášení, nebo aplikace může pokus zopakovat, `acquireTokenSilent` později. To se běžně používá, když uživatel může používat další funkce aplikace bez narušení – například je neověřené obsah k dispozici v aplikaci. V takovém případě se uživatel může rozhodnout, pokud chtějí přihlášení pro přístup k chráněnému prostředku nebo aktualizujte zastaralé informace.

> [!NOTE]
> Tento rychlý start využívá `loginRedirect` a `acquireTokenRedirect` metody, když se používá prohlížeč je aplikace Internet Explorer z důvodu [známý problém](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) souvisejících s zpracování zobrazována místní okna v prohlížeči Internet Explorer.
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
