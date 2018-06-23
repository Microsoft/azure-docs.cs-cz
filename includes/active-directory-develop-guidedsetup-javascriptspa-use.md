## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Přihlášení uživatele pomocí knihovny ověřování společnosti Microsoft (MSAL)

1.  Vytvořte soubor s názvem `app.js`. Pokud používáte Visual Studio, vyberte projekt (projektu do kořenové složky), klikněte pravým tlačítkem a vyberte: `Add`  >  `New Item`  >  `JavaScript File`:
2.  Přidejte následující kód do vaší `app.js` souboru:

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

        // Show sign-off button
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

Po kliknutí *'volání Microsoft Graph API,* tlačítko poprvé, `callGraphApi` volání metod `loginRedirect` k přihlášení uživatele. Výsledkem této metody přesměrování uživateli *koncového bodu Microsoft Azure Active Directory v2* k řádku a ověření přihlašovacích údajů uživatele. V důsledku úspěšného přihlášení, bude uživatel přesměrován zpět do původního *index.html* stránky a token byl přijat zpracovává `msal.js` a informací obsažených v tokenu se uloží do mezipaměti. Tento token se označuje jako *ID token* a obsahuje základní informace o uživateli, jako je například zobrazované uživatelské jméno. Pokud máte v plánu používat žádná data poskytované tento token k jakýmkoli jiným účelům, budete muset Ujistěte se, že tento token je ověřen back-end serveru zaručit, že byl token vydán pro platného uživatele pro vaši aplikaci.

SPA generované tímto průvodcem neprovede používat přímo z tokenu ID – místo toho volá `acquireTokenSilent` nebo `acquireTokenRedirect` získat *přístupový token* používá k dotazování Microsoft Graph API. Pokud potřebujete vzorku, který ověří ID token, podívejte se na [to](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Githubu active-directory-javascript-singlepageapp-dotnet-webapi-v2 ukázka") ASP používá ukázkovou aplikaci v Githubu – ukázka Rozhraní .NET webového rozhraní API pro ověření tokenu.

#### <a name="getting-a-user-token-interactively"></a>Získání tokenu uživatele interaktivně

Po počáteční přihlášení, nechcete požádejte uživatele k novému ověření pokaždé, když potřebují k vyžádání tokenu pro přístup k prostředkům – tak *acquireTokenSilent* se používá ve většině případů k získávat tokeny. Existují však situace, budete muset vynutit uživatelům interakci s koncovým bodem v2 Azure Active Directory – mezi příklady patří:
- Uživatelé možná muset znovu zadat své přihlašovací údaje, protože vypršela platnost hesla
- Vaše aplikace požaduje přístup k prostředku, který uživatel musí souhlasit s
- Není třeba dvoufaktorové ověřování

Volání *acquireTokenRedirect(scope)* mít za následek uživatele přesměrují na koncový bod služby Azure Active Directory v2 (nebo *acquireTokenPopup(scope)* výsledek v automaticky otevíraném okně) kdy je potřeba interakci uživatelů potvrzení jejich přihlašovacích údajů, udělení souhlasu pro požadovaný prostředek, nebo dokončení dvoufaktorové ověřování.

#### <a name="getting-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění
` acquireTokenSilent` Metoda zpracovává tokenu pořízení a obnovení bez nutnosti zásahu uživatele. Po `loginRedirect` (nebo `loginPopup`) se spustí poprvé, `acquireTokenSilent` je metoda běžně používají k získání tokeny použít přístup k chráněným prostředkům pro následující volání - volání na vyžádání nebo obnovení tokeny jsou vytvářeny bezobslužně.
`acquireTokenSilent` může dojít k selhání v některých případech – třeba hesla vypršela. Aplikace může zpracovat výjimku dvěma způsoby:

1.  Ujistěte se, volání `acquireTokenRedirect` okamžitě, výsledkem výzvy pro uživatele k přihlášení. Tento vzor se často používá v online aplikace tam, kde není žádná neověřené obsah v aplikaci k dispozici pro uživatele. Ukázka generované touto s průvodcem instalací používá tento vzor.

2. Aplikace můžete udělat taky vizuální označení uživateli, které interaktivní přihlášení je povinné, tak, aby si uživatel může vybrat správný čas pro přihlášení, nebo můžete zkusit aplikaci `acquireTokenSilent` později. To se často používá, když uživatel může použít další funkce aplikace bez narušení – například není neověřené obsah k dispozici v aplikaci. V takovém případě uživatel můžete rozhodnout, pokud se chcete přihlásit k chráněnému prostředku nebo aktualizovat zastaralé informace.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Volání rozhraní Graph API Microsoft pomocí tokenu, který jste obdrželi

Přidejte následující kód do vaší `app.js` souboru:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Další informace o volání REST chráněné rozhraní API

V ukázkové aplikaci vytvořit v této příručce `callWebApiWithToken()` metoda se používá k zajištění HTTP `GET` požadavku pro chráněný prostředek, který vyžaduje token a pak se vraťte obsah volajícímu. Tato metoda přidá získal token v *HTTP autorizační hlavičky*. Pro ukázkovou aplikaci vytvořit v této příručce, prostředek je rozhraní Microsoft Graph API *mi* koncového bodu – zobrazí informace o profilu uživatele.

<!--end-collapse-->

## <a name="add-a-method-to-sign-off-the-user"></a>Přidejte metodu k odhlaste uživatele

Přidejte následující kód do vaší `app.js` souboru:

```javascript
/**
 * Sign off the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
