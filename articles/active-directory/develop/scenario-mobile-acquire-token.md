---
title: Získání tokenu pro volání webového rozhraní API (mobilní aplikace) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak vytvořit mobilní aplikaci, která volá webová API. (Získejte token pro aplikaci.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: a77e6c9086a745804c23f431f633d530e2655f16
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868883"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Získání tokenu pro mobilní aplikaci, která volá webová api

Než bude vaše aplikace moci volat chráněná webová api, potřebuje přístupový token. Tento článek vás provede procesem získat token pomocí Knihovny ověřování Microsoft (MSAL).

## <a name="define-a-scope"></a>Definování oboru

Když požadujete token, musíte definovat obor. Obor určuje, k jakým datům má vaše aplikace přístup.

Nejjednodušší způsob, jak definovat obor je kombinovat požadované `App ID URI` webové rozhraní `.default`API s rozsahem . Tato definice říká platformě identit Microsoftu, že vaše aplikace vyžaduje všechny obory, které jsou nastaveny na portálu.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Získat žetony

### <a name="acquire-tokens-via-msal"></a>Získání žetonů přes MSAL

MSAL umožňuje aplikacím získávat tokeny tiše a interaktivně. Při volání `AcquireTokenSilent()` `AcquireTokenInteractive()`nebo , MSAL vrátí přístupový token pro požadované obory. Správný vzor je provést tichý požadavek a pak se vrátit k interaktivní požadavek.

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
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

Nejprve zkuste získat token tiše:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

Pokud msal `MSALErrorInteractionRequired`vrátí , zkuste získat tokeny interaktivně:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL pro iOS a macOS podporuje různé modifikátory získat token interaktivně nebo tiše:
* [Běžné parametry pro získání tokenu](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametry pro získání interaktivního tokenu](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametry pro získání tichého tokenu](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Následující příklad ukazuje minimální kód získat token interaktivně. Příklad používá Microsoft Graph ke čtení profilu uživatele.

```csharp
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

#### <a name="mandatory-parameters-in-msalnet"></a>Povinné parametry v MSAL.NET

`AcquireTokenInteractive`má pouze jeden `scopes`povinný parametr: . Parametr `scopes` vyjmenovává řetězce, které definují obory, pro které je vyžadován token. Pokud je token pro Microsoft Graph, můžete najít požadované obory v odkazu rozhraní API každého rozhraní API Microsoft Graph. V odkazu přejděte do části Oprávnění.

Chcete-li například [uvést kontakty uživatele](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), použijte obor "User.Read", "Contacts.Read". Další informace naleznete v [tématu Odkaz na oprávnění aplikace Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

V systému Android můžete při vytváření aplikace `PublicClientApplicationBuilder`pomocí aplikace určit nadřazenou aktivitu. Pokud v té době nezadáte nadřazenou aktivitu, `.WithParentActivityOrWindow` můžete ji později zadat pomocí jako v následující části. Pokud zadáte nadřazenou aktivitu, token se po interakci vrátí k této nadřazené aktivitě. Pokud nezadáte, pak `.ExecuteAsync()` volání vyvolá výjimku.

#### <a name="specific-optional-parameters-in-msalnet"></a>Specifické volitelné parametry v MSAL.NET

V následujících částech jsou vysvětleny volitelné parametry v MSAL.NET.

##### <a name="withprompt"></a>WithPrompt

Parametr `WithPrompt()` řídí interaktivitu s uživatelem zadáním výzvy.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Třída definuje následující konstanty:

- `SelectAccount`vynutí, aby služba tokenů zabezpečení (STS) představila dialogové okno pro výběr účtu. Dialogové okno obsahuje účty, pro které má uživatel relaci. Tuto možnost můžete použít, pokud chcete, aby uživatel mohl vybrat mezi různými identitami. Tato možnost pohání MSAL k odeslání `prompt=select_account` zprostředkovateli identity.

    Konstanta `SelectAccount` je výchozí a efektivně poskytuje nejlepší možné prostředí na základě dostupných informací. Dostupné informace mohou zahrnovat účet, přítomnost relace pro uživatele a tak dále. Toto výchozí nastavení neměňte, pokud k tomu nemáte dobrý důvod.
- `Consent`umožňuje vyzvat uživatele k udělení souhlasu, i když byl souhlas udělen dříve. V tomto případě MSAL odešle `prompt=consent` zprostředkovateli identity.

    Konstantu `Consent` můžete použít v aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžadují, aby uživatelé viděli dialogové okno souhlasu při každém použití aplikace.
- `ForceLogin`umožňuje službě vyzvat uživatele k zadání pověření i v případě, že výzva není potřeba.

    Tato možnost může být užitečná, pokud se nezdaří získání tokenu a chcete, aby se uživatel znovu přihlásil. V tomto případě MSAL odešle `prompt=login` zprostředkovateli identity. Tuto možnost můžete použít v aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžaduje, aby se uživatel přikaždém přístupu k určitým částem aplikace přihlašoval.
- `Never`je pouze pro rozhraní .NET 4.5 a prostředí Windows Runtime (WinRT). Tato konstanta nevyzve uživatele, ale pokusí se použít soubor cookie, který je uložen ve skrytém vloženém webovém zobrazení. Další informace naleznete [v tématu Používání webových prohlížečů s MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers).

    Pokud se tato `AcquireTokenInteractive` možnost nezdaří, pak vyvolá výjimku, která vás upozorní, že je nutná interakce s uzem. Pak musíte použít `Prompt` jiný parametr.
- `NoPrompt`neodešle výzvu poskytovateli identity.

    Tato možnost je užitečná jenom pro zásady upravit profil ve službě Azure Active Directory B2C. Další informace naleznete v [tématu Specifika B2C](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

`WithExtraScopeToConsent` Modifikátor použijte v rozšířeném scénáři, kde chcete, aby uživatel poskytl předem souhlas s několika prostředky. Tento modifikátor můžete použít, pokud nechcete používat přírůstkový souhlas, který se obvykle používá s MSAL.NET nebo Platforma identit microsoft 2.0. Další informace naleznete [v tématu Mít souhlas uživatele předem pro několik zdrojů](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Zde je příklad kódu:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Další volitelné parametry

Další volitelné parametry aplikace `AcquireTokenInteractive`naleznete v [referenční dokumentaci pro acquiretokeninteractiveparameterbuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Získání žetonů prostřednictvím protokolu

Nedoporučujeme přímo pomocí protokolu získat tokeny. Pokud tak učiníte, aplikace nebude podporovat některé scénáře, které zahrnují jednotné přihlašování (SSO), správu zařízení a podmíněný přístup.

Při použití protokolu získat tokeny pro mobilní aplikace, aby dva požadavky:

* Získejte autorizační kód.
* Vyměňte kód za token.

#### <a name="get-an-authorization-code"></a>Získání autorizačního kódu

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Získání přístupu a aktualizace tokenu

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-mobile-call-api.md)
