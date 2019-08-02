---
title: Mobilní aplikace, která volá webová rozhraní API – získání tokenu pro aplikaci | Platforma Microsoft identity
description: Naučte se, jak vytvořit mobilní aplikaci, která volá webová rozhraní API (získání tokenu pro aplikaci).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d49717355cab5441d26608fa12333bd1b8b73d44
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413535"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Mobilní aplikace, která volá webová rozhraní API – získá token.

Než budete moct začít volat chráněná webová rozhraní API, bude aplikace potřebovat přístupový token. Tento článek vás provede procesem získání tokenu pomocí knihovny Microsoft Authentication Library (MSAL).

## <a name="scopes-to-request"></a>Rozsahy k vyžádání

Když vyžádáte token, budete muset definovat obor. Obor určuje, k jakým datům může aplikace přistupovat.  

Nejjednodušším řešením je kombinování požadovaného webového rozhraní API `App ID URI` s oborem `.default`. Tím se poradí platforma Microsoftu pro identitu, kterou vaše aplikace vyžaduje pro všechny obory nastavené na portálu.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes: [String] = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Získat tokeny

### <a name="via-msal"></a>Přes MSAL

MSAL umožňuje aplikacím získat bez tichého a interaktivního získávání tokenů. Stačí zavolat tyto metody a MSAL vrátí přístupový token pro požadované obory. Správným vzorem je provedení tichého požadavku a návrat k interaktivnímu požadavku.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

// Get tokens.
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        let nsError = error as NSError

        // interactionRequired means you need to ask the user to sign in. This usually happens
        // when the user's refresh token is expired or when the user has changed the password,
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

    // Token is ready via silent acquisition.
    self.accessToken = result.accessToken
}
```

#### <a name="xamarin"></a>Xamarin

Následující příklad ukazuje minimální kód pro interaktivní získání tokenu pro čtení profilu uživatele pomocí Microsoft Graph.

```CSharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Povinné parametry

`AcquireTokenInteractive`má pouze jeden povinný parametr ``scopes``, který obsahuje výčet řetězců definujících obory, pro které je vyžadován token. Pokud je token pro Microsoft Graph, požadované obory najdete v referenčních informacích k rozhraní API pro každé rozhraní API Microsoft Graph v části s názvem "oprávnění". Pokud například chcete [Zobrazit seznam kontaktů uživatele](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), bude nutné použít rozsah "User. Read", "Contacts. Read". Viz také [Microsoft Graph odkaz na oprávnění](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Pokud jste při sestavování aplikace nezadali tuto možnost, musíte v systému Android zadat také nadřazenou aktivitu (pomocí `.WithParentActivityOrWindow`níže uvedených možností), aby se token po interakci vrátil zpět do této nadřazené aktivity. Pokud ho nezadáte, vyvolá se při volání `.ExecuteAsync()`výjimka.

### <a name="specific-optional-parameters"></a>Konkrétní volitelné parametry

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`slouží k řízení interaktivity s uživatelem zadáním výzvy.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Třída definuje následující konstanty:

- ``SelectAccount``: vynutí, aby služba STS obsahovala dialog pro výběr účtu, který obsahuje účty, pro které má uživatel relaci. Tato možnost je užitečná, když vývojáři aplikací chtějí uživateli zvolit mezi různými identitami. Tato možnost Drives MSAL se ``prompt=select_account`` pošle poskytovateli identity. Tato možnost je výchozí a má dobrou úlohu při poskytování nejlepšího možného prostředí na základě dostupných informací (účtu, přítomnosti relace pro uživatele atd.). ...). Neměňte ho, pokud nemáte dobrý důvod to udělat.
- ``Consent``: umožňuje vývojáři aplikace vynutit, aby se uživateli zobrazila výzva k vyjádření souhlasu i v případě, že byl souhlas udělen dříve. V takovém případě MSAL odesílá `prompt=consent` poskytovateli identity. Tato možnost se dá použít v některých aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžadují, aby se uživateli zobrazoval dialog pro vyjádření souhlasu při každém použití aplikace.
- ``ForceLogin``: povolí vývojáři aplikace, aby se uživateli zobrazila výzva k zadání přihlašovacích údajů, i v případě, že nepotřebujete tuto výzvu uživatele. Tato možnost může být užitečná, pokud se získání tokenu nepovede, aby se uživatel mohl znovu přihlásit. V takovém případě MSAL odesílá `prompt=login` poskytovateli identity. Znovu jsme viděli, že se používá v některých aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžadují, aby se uživatel znovu přihlásil – pokaždé, když přistupují k určitým částem aplikace.
- ``Never``(jenom pro .NET 4,5 a WinRT) se uživateli nezobrazí výzva, ale místo toho se pokusí použít soubor cookie uložený v skrytém vloženém webovém zobrazení (viz níže: Webová zobrazení v MSAL.NET). Použití této možnosti může selhat a v takovém případě `AcquireTokenInteractive` vyvolá výjimku, která oznamuje, že je potřeba interakce uživatelského rozhraní, a vy budete muset použít jiný `Prompt` parametr.
- ``NoPrompt``: Nepošle žádné výzvy poskytovateli identity. Tato možnost je užitečná jenom pro Azure AD B2C úpravou zásad profilu (viz téma [specifické pro B2C](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Tento modifikátor se používá v pokročilém scénáři, kdy chcete uživateli předběžně odsouhlasit několik zdrojů (a nechcete používat přírůstkový souhlas, který se obvykle používá s MSAL.NET nebo Microsoft Identity Platform v 2.0). Podrobnosti najdete v tématu [Postupy: použití souhlasu uživatele před několika prostředky](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="other-optional-parameters"></a>Další nepovinné parametry

Další informace o všech dalších volitelných parametrech `AcquireTokenInteractive` pro AcquireTokenInteractiveParameterBuilder najdete v referenční dokumentaci pro [](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) .

### <a name="via-the-protocol"></a>Přes protokol

Nedoporučujeme používat přímo protokol. Pokud to uděláte, aplikace nebude podporovat některé možnosti jednotného přihlašování (SSO), správy zařízení a podmíněného přístupu.

Když použijete protokol k získání tokenů pro mobilní aplikace, musíte provést dvě požadavky: získání autorizačního kódu a jeho výměna pro token.

#### <a name="get-authorization-code"></a>Získat autorizační kód

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Získání přístupu a aktualizace tokenu

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
