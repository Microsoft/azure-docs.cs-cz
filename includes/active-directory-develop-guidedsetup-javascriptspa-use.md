---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 94d57abc95dabf1da579f6d2105ca6c74140a86f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293700"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Přihlásit uživatele pomocí knihovny Microsoft Authentication Library (MSAL)

1.  Vytvořte soubor s názvem `app.js`. Pokud používáte Visual Studio, vyberte projekt (Kořenová složka projektu), klikněte pravým tlačítkem myši a vyberte: `Add`  >  `New Item`  >  `JavaScript File`:
2.  Přidejte následující kód, který vaše `app.js` souboru:

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show sign-out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });
    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from sign-in
 * @param {object} error - The error string
 * @param {string} tokenType - The token type: For loginRedirect, tokenType = "id_token". For acquireTokenRedirect, tokenType:"access_token".
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Po kliknutí *"Volat Microsoft Graph API"* tlačítko poprvé, `callGraphApi` volání metody `loginRedirect` k přihlášení uživatele. Výsledkem přesměrování uživatele do této metody *koncového bodu Microsoft Azure Active Directory v2* výzvu a ověření přihlašovacích údajů uživatele. V důsledku u úspěšné přihlášení, bude uživatel přesměrován zpět na původní *index.html* stránky a token pro přijetí zpracovány `msal.js` a informací obsažených v tokenu se uloží do mezipaměti. Tento token se označuje jako *ID token* a obsahuje základní informace o uživateli, jako je zobrazované jméno uživatele. Pokud máte v plánu používat data poskytuje tento token pro účely, musíte zajistit, aby že tento token je potvrzen v back-end serveru k zajištění, že byl token vydán platnému uživateli služby pro vaši aplikaci.

Jednostránková aplikace vygenerované Tato příručka neposkytuje využívání ID token – místo toho, které volá `acquireTokenSilent` a/nebo `acquireTokenRedirect` získat *přístupový token* používá k dotazování na rozhraní Microsoft Graph API. Pokud potřebujete vzorku, který ověří ID token, podívejte se na [to](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "active-directory-javascript-singlepageapp-dotnet-webapi-v2 ukázky Githubu") ukázková aplikace v Githubu – Ukázka používá ASP .NET webového rozhraní API pro ověřování tokenů.

#### <a name="getting-a-user-token-interactively"></a>Získání tokenu uživatele interaktivně

Po počátečním přihlášení, nechcete žádat uživatele nutnost opakovaného ověření příslušných pokaždé, když potřebují k vyžádání tokenu pro přístup k prostředku – tedy *acquireTokenSilent* by ve většině případů použít k získání tokenů. Existují však situace, že je potřeba vynutit uživatelům, aby komunikovali s koncovým bodem služby Azure Active Directory v2 – mezi příklady patří:
- Uživatelé mohou muset znovu zadat své přihlašovací údaje, protože vypršela platnost hesla
- Aplikace žádá o přístup k prostředku, který uživatel musí vyjádřit souhlas
- Dvoufaktorové ověřování je povinné

Volání *acquireTokenRedirect(scope)* výsledkem přesměrování uživatelů na koncový bod Azure Active Directory v2 (nebo *acquireTokenPopup(scope)* výsledky v automaticky otevíraném okně) Pokud uživatelé potřebují pracovat potvrzení přihlašovacích údajů, udělení souhlasu pro požadovaný prostředek, nebo dokončení dvoufaktorové ověřování.

#### <a name="getting-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění
` acquireTokenSilent` Obsluhovala token pořízení a obnovení bez nutnosti zásahu uživatele. Po `loginRedirect` (nebo `loginPopup`) provádí poprvé, `acquireTokenSilent` je metoda běžně používá k získání tokenů použít pro přístup k chráněným prostředkům pro pozdější volání – jako volání na vyžádání nebo tokeny obnovení probíhají bezobslužně.
`acquireTokenSilent` nemusí v některých případech – třeba hesla vypršela. Vaše aplikace dokáže zpracovat tuto výjimku dvěma způsoby:

1.  Volání `acquireTokenRedirect` okamžitě, jehož výsledkem výzvy k přihlášení. Tento model se běžně používá v online aplikace tam, kde není žádná neověřená obsah v aplikaci k dispozici pro uživatele. Ukázka vygeneroval tento instalační program s asistencí používá tento model.

2. Aplikace lze také nastavit vizuální označení pro uživatele, který interaktivnímu přihlášení je nutné, takže uživatel může vybrat správný čas pro přihlášení, nebo aplikace může pokus zopakovat, `acquireTokenSilent` později. To se běžně používá, když uživatel může používat další funkce aplikace bez narušení – například je neověřené obsah k dispozici v aplikaci. V takovém případě se uživatel může rozhodnout, pokud chtějí přihlášení pro přístup k chráněnému prostředku nebo aktualizujte zastaralé informace.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Volání rozhraní Microsoft Graph API pomocí tokenu, který jste získali

Přidejte následující kód, který vaše `app.js` souboru:

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Další informace o provádění volání REST chráněné rozhraní API

V ukázkové aplikaci vytvořil tento průvodce `callWebApiWithToken()` aby protokolu HTTP se používá metoda `GET` požadavku na chráněný prostředek, který se vyžaduje token a potom vrátí obsah volajícímu. Tato metoda přidá tokenu získaného v *hlavičku protokolu HTTP ověřování*. Pro ukázkovou aplikaci vytvořené v této příručce, prostředek je v rozhraní Microsoft Graph API *mě* koncový bod – zobrazí informace o profilu uživatele.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Přidejte metodu k neodhlásí uživatele

Přidejte následující kód, který vaše `app.js` souboru:

```javascript
/**
 * Sign out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
