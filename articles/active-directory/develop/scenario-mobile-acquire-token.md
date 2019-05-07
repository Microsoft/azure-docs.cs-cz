---
title: Mobilní aplikace, že volání webového rozhraní API – získání tokenu pro aplikaci | Platforma identit Microsoft
description: Informace o sestavení mobilní aplikace, která volá webové rozhraní API (získání tokenu pro aplikaci)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6933bfbbff574495655ef9065a786fa313b02bd6
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075172"
---
# <a name="mobile-app-that-calls-web-apis---acquire-a-token"></a>Mobilní aplikace, která volá webové rozhraní API – získání tokenu

Před zahájením volání chráněného webového rozhraní API, aplikace potřebovat přístupový token. Tato část vás provede procesu k získání tokenu pomocí knihovny Microsoft Authentication Library (MSAL).

## <a name="scopes-to-request"></a>Obory žádosti

Pokud s žádostí o tokeny, obor je vždy vyžaduje. Obor určuje, jaká data vaše aplikace může mít přístup k.  

Nejjednodušším způsobem je zkombinovat požadované webového rozhraní API `App ID URI` s oborem `.default`. Microsoft identity říká, že vaše aplikace vyžaduje všechny obory, nastavte na portálu.

Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

iOS
```swift
let scopes: [String] = ["https://graph.microsoft.com/.default"]
```

Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="acquiring-tokens"></a>Získávání tokenů

### <a name="via-msal"></a>prostřednictvím MSAL

MSAL umožňuje aplikacím získat tokeny, bez upozornění a interaktivně. Stačí tyto metody volat a MSAL vrátí zpět přístupový token pro požadované obory. Správné vzor je provést tichou požadavku a nouzového řešení ověření interaktivní žádosti.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently
// Result is in our silent callback (success or error)
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or >1 account */
        }
    }
});    

[...]

// No accounts found, interactively request a token 
// TODO: Create an interactive callback to catch successful or failed request
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

```swift
// Initialize our app 
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

// Get tokens
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        let nsError = error as NSError

        // interactionRequired means we need to ask the user to sign-in. This usually happens
        // when the user's Refresh Token is expired or if the user has changed their password
        // among other possible reasons.
        if (nsError.domain == MSALErrorDomain) {
            if (nsError.code == MSALError.interactionRequired.rawValue) {    
                DispatchQueue.main.async {
                    guard let applicationContext = self.applicationContext else { return }
                    let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
                    applicationContext.acquireToken(with: parameters) { (result, error) in
                        if let error = error {
                            self.updateLogging(text: "Could not acquire token: \(error)")
                            return
                        }

                        guard let result = result else {
                            self.updateLogging(text: "Could not acquire token: No result returned")
                            return
                        }
                        
                        // Token is ready via interaction!
                        self.accessToken = result.accessToken
                    }
                }
                return
            }
        }

        self.updateLogging(text: "Could not acquire token silently: \(error)")
        return
    }
    guard let result = result else {
        self.updateLogging(text: "Could not acquire token: No result returned")
        return
    }

    // Token is ready via silent acquisition 
    self.accessToken = result.accessToken
}
```

#### <a name="xamarin"></a>Xamarin

```CSharp
string[] scopes = new string["https://graph.microsoft.com/.default"];
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException e)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="via-protocol"></a>přes protokol

Nedoporučujeme přejít přímo proti protokolu. Vaše aplikace, nebude moct scénářů mnoho jednotné přihlašování (SSO) a nesmí být schopni poskytovat podporu veškerá Správa zařízení a scénáře podmíněného přístupu.

Při získávání tokenů pro mobilní aplikace pomocí protokolu, budete potřebovat k podání žádostí o 2: získání autorizačního kódu a exchange pro token. 

#### <a name="getting-authorization-code"></a>Získání autorizačního kódu

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="getting-access-and-refresh-token"></a>Získání tokenu přístupu a aktualizace

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-mobile-call-api.md)
