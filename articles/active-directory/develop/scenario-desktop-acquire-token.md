---
title: Získání tokenu pro volání webového rozhraní API (desktopová aplikace) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak vytvořit desktopovou aplikaci, která volá webová rozhraní API k získání tokenu pro aplikaci.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/06/2021
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: c63ee686ae218a696069465bb8d2d1d7413a998e
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799084"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Aplikace klasické pracovní plochy, která volá webová rozhraní API: získání tokenu

Po vytvoření instance veřejné klientské aplikace ji použijete k získání tokenu, který pak použijete k volání webového rozhraní API.

## <a name="recommended-pattern"></a>Doporučený vzor

Webové rozhraní API je definováno pomocí `scopes` . Bez ohledu na možnosti, které zadáte ve své aplikaci, je použit vzor:

- Systematicky se pokusí získat token z mezipaměti tokenu voláním `AcquireTokenSilent` .
- Pokud se toto volání nezdařilo, použijte `AcquireToken` tok, který chcete použít, který je reprezentován zde `AcquireTokenXX` .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>V MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>V MSAL pro iOS a macOS

Cíl-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
SWIFT

```swift
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
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

V uzlu MSAL získáte tokeny pomocí toku autorizačního kódu s ověřovacím klíčem pro výměnu kódu (PKCE). Uzel MSAL používá mezipaměť tokenů v paměti k zjištění, jestli v mezipaměti nejsou nějaké uživatelské účty. V takovém případě je možné objekt účtu předat `acquireTokenSilent()` metodě pro načtení přístupového tokenu, který je uložený v mezipaměti.

```JavaScript

const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const msalTokenCache = pca.getTokenCache();

let accounts = await msalTokenCache.getAllAccounts();

    if (accounts.length > 0) {

        const silentRequest = {
            account: accounts[0], // Index must match the account that is trying to acquire token silently
            scopes: ["user.read"],
        };
    
        pca.acquireTokenSilent(silentRequest).then((response) => {
            console.log("\nSuccessful silent token acquisition");
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => console.log(error));
    } else {
        const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

        const authCodeUrlParameters = {
            scopes: ["User.Read"],
            redirectUri: "your_redirect_uri",
            codeChallenge: challenge, // PKCE Code Challenge
            codeChallengeMethod: "S256" // PKCE Code Challenge Method 
        };
        
        // get url to sign user in and consent to scopes needed for application
        pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            console.log(response);
        
            const tokenRequest = {
                code: response["authorization_code"],
                codeVerifier: verifier // PKCE Code Verifier 
                redirectUri: "your_redirect_uri",
                scopes: ["User.Read"],
            };
            
            // acquire a token by exchanging the code
            pca.acquireTokenByCode(tokenRequest).then((response) => {
                console.log("\nResponse: \n:", response);
            }).catch((error) => {
                console.log(error);
            });
        }).catch((error) => console.log(JSON.stringify(error)));
    }
```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```
---

Tady jsou různé způsoby, jak získat tokeny v desktopové aplikaci.

## <a name="acquire-a-token-interactively"></a>Interaktivní získání tokenu

Následující příklad ukazuje minimální kód pro interaktivní získání tokenu pro čtení profilu uživatele pomocí Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>V MSAL.NET

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

### <a name="mandatory-parameters"></a>Povinné parametry

`AcquireTokenInteractive` má pouze jeden povinný parametr, ``scopes`` , který obsahuje výčet řetězců definujících obory, pro které je vyžadován token. Pokud je token pro Microsoft Graph, požadované obory najdete v referenčních informacích k rozhraní API každého Microsoft Graph API v části s názvem "oprávnění". Pokud například chcete [Zobrazit seznam kontaktů uživatele](/graph/api/user-list-contacts), je nutné použít rozsah "User. Read", "Contacts. Read". Další informace najdete v tématu informace o [Microsoft Graph oprávnění](/graph/permissions-reference).

V systému Android je také nutné zadat nadřazenou aktivitu pomocí `.WithParentActivityOrWindow` , jak je znázorněno, aby se token vrátil zpět k této nadřazené aktivitě po interakci. Pokud ho nezadáte, vyvolá se při volání výjimka `.ExecuteAsync()` .

### <a name="specific-optional-parameters-in-msalnet"></a>Konkrétní volitelné parametry v MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Uživatelské rozhraní je důležité, protože je interaktivní. `AcquireTokenInteractive` má jeden konkrétní volitelný parametr, který může určit, pro platformy, které ho podporují, nadřazené uživatelské rozhraní. Při použití v desktopové aplikaci `.WithParentActivityOrWindow` má jiný typ, který závisí na platformě. Alternativně můžete vynechat volitelný parametr nadřazeného okna a vytvořit okno, pokud nechcete ovládat, kde se na obrazovce zobrazí přihlašovací dialog. To se dá použít pro aplikace, které jsou založené na příkazovém řádku, které se používají k předávání volání do jakékoli jiné back-end služby a nepotřebují žádná okna pro interakci s uživatelem.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Mark

- V .NET Standard se očekává, že `object` se nachází `Activity` v Androidu, v `UIViewController` iOS, `NSWindow` na Macu a `IWin32Window` `IntPr` v systému Windows.
- V systému Windows je nutné volat `AcquireTokenInteractive` z vlákna uživatelského rozhraní, aby vložený prohlížeč dostal příslušný kontext synchronizace uživatelského rozhraní. Nevolání z vlákna uživatelského rozhraní může způsobit, že se v uživatelském rozhraní nemusejí správně vygenerovat zprávy o scénářích a zablokování. Jedním ze způsobů, jak volat knihovny MSALs (Microsoft Authentication Library) z vlákna uživatelského rozhraní, pokud nejste ve vlákně uživatelského rozhraní, je použití `Dispatcher` v WPF.
- Pokud používáte WPF, chcete-li získat okno z ovládacího prvku WPF, můžete použít `WindowInteropHelper.Handle` třídu. Pak je volání z ovládacího prvku WPF ( `this` ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` slouží k řízení interaktivity s uživatelem zadáním výzvy.

![Obrázek znázorňující pole ve struktuře výzvy Tyto konstantní hodnoty řídí interakce s uživatelem tím, že definují typ výzvy zobrazený metodou WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

Třída definuje následující konstanty:

- ``SelectAccount`` vynutí, aby služba STS obsahovala dialogové okno Výběr účtu, které obsahuje účty, pro které má uživatel relaci. Tato možnost je užitečná, když vývojáři aplikací chtějí umožnit uživatelům výběr mezi různými identitami. Tato možnost Drives MSAL se pošle ``prompt=select_account`` poskytovateli identity. Tato možnost je výchozí. Na základě dostupných informací, jako je například účet a přítomnost relace pro uživatele, je vhodné zajistit nejlepší možné prostředí. Neměňte ho, pokud nemáte dobrý důvod to udělat.
- ``Consent`` umožňuje vývojáři aplikace vynutit, aby se uživateli zobrazila výzva k vyjádření souhlasu, a to i v případě, že byl souhlas udělen dříve. V takovém případě MSAL odesílá `prompt=consent` poskytovateli identity. Tato možnost se dá použít v některých aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžadují, aby se uživateli zobrazovalo dialogové okno souhlasu při každém použití aplikace.
- ``ForceLogin`` umožňuje, aby vývojář aplikace uživateli zobrazil výzvu k zadání přihlašovacích údajů, a to i v případě, že se tato výzva uživateli nemusí potřebovat. Tato možnost může být užitečná, pokud chcete, aby se uživatel znovu přihlásil, pokud se nepovede k získání tokenu. V takovém případě MSAL odesílá `prompt=login` poskytovateli identity. Někdy se používá v aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžadují, aby se uživatel znovu přihlásí při každém přístupu k určitým částem aplikace.
- ``Never`` (jenom pro .NET 4,5 a WinRT) se uživatel nevyzve, ale pokusí se použít soubor cookie uložený v skrytém vloženém webovém zobrazení. Další informace najdete v tématu věnovaném webovým zobrazením v MSAL.NET. Použití této možnosti může selhat. V takovém případě `AcquireTokenInteractive` vyvolá výjimku, která oznamuje, že je potřeba interakce uživatelského rozhraní. Budete muset použít jiný `Prompt` parametr.
- ``NoPrompt`` nepošle žádné výzvy poskytovateli identity. Tato možnost je užitečná jenom pro Azure Active Directory (Azure AD) B2C upravit zásady profilu. Další informace najdete v tématu [Azure AD B2C specifických](https://aka.ms/msal-net-b2c-specificities)pro.

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Tento modifikátor se používá v pokročilém scénáři, kdy chcete uživateli předběžně odsouhlasit několik zdrojů a nechcete používat přírůstkový souhlas, který se obvykle používá s MSAL.NET/the platformou Microsoft identity. Další informace najdete v tématu o tom, že [Uživatel souhlasí s několika prostředky](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Webové uživatelské rozhraní je mechanismus k vyvolání prohlížeče. Tento mechanismus může být vyhrazeným ovládacím prvkem uživatelského rozhraní WebBrowser nebo způsobem, jak delegovat otevření prohlížeče.
MSAL poskytuje implementace webového uživatelského rozhraní pro většinu platforem, ale v některých případech můžete chtít hostovat prohlížeč sami:

- Platformy, které nejsou explicitně pokryté MSAL, například Blazor, Unity a mono na stolních počítačích.
- Chcete testovat aplikaci v uživatelském rozhraní a používat automatizovaný prohlížeč, který lze použít se systémem selen.
- Prohlížeč a aplikace, které používají MSAL, jsou v samostatných procesech.

##### <a name="at-a-glance"></a>Ve zkratce

K dosažení tohoto cíle přiřadíte MSAL `start Url` , který se musí zobrazit v prohlížeči podle volby, aby koncový uživatel mohl zadávat položky, jako je například uživatelské jméno.
Po dokončení ověřování se aplikace musí předat zpátky do MSAL `end Url` , která obsahuje kód poskytovaný službou Azure AD.
Hostitel `end Url` je vždy `redirectUri` . K zachycení `end Url` proveďte jednu z následujících akcí:

- Sledujte přesměrování prohlížeče `redirect Url` , dokud není dosaženo.
- Přesměrujte prohlížeč na adresu URL, kterou sledujete.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi je bod rozšiřitelnosti.

`WithCustomWebUi` je bod rozšiřitelnosti, který můžete použít k poskytnutí vlastního uživatelského rozhraní ve veřejných klientských aplikacích. Můžete také nechat uživatele projít koncovým bodem/Authorize poskytovatele identity a nechat si ho přihlašovat a vyjádřit souhlas. MSAL.NET může následně uplatnit ověřovací kód a získat token. Například se používá v aplikaci Visual Studio k tomu, aby aplikace Electrons (například zpětná vazba sady Visual Studio) poskytovala webové interakce, ale ponechala MSAL.NET, aby provede většinu práce. Můžete ji také použít, pokud chcete poskytnout automatizaci uživatelského rozhraní. Ve veřejných klientských aplikacích MSAL.NET používá ověřovací klíč pro výměnu kódu (PKCE) Standard k zajištění dodržování zabezpečení. Jenom MSAL.NET můžou uplatnit kód. Další informace najdete v [dokumentu RFC 7636-kontrolní klíč pro výměnu kódu pomocí veřejných klientů OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Použití WithCustomWebUi

Chcete-li použít `.WithCustomWebUI` , postupujte podle těchto kroků.

  1. Implementujte rozhraní `ICustomWebUi`. Další informace najdete na [tomto webu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implementujte jednu `AcquireAuthorizationCodeAsync` metodu a přijměte adresu URL autorizačního kódu vypočítanou pomocí MSAL.NET. Potom umožníte uživateli projít interakci se zprostředkovatelem identity a vrátit zpět adresu URL, kterou by zprostředkovatel identity volal jako vaši implementaci zpátky spolu s autorizačním kódem. Pokud máte problémy, vaše implementace by měla vyvolat `MsalExtensionException` výjimku, která bude s MSAL úzce spolupracovat.
  2. Ve svém `AcquireTokenInteractive` volání použijte `.WithCustomUI()` Modifikátor předání instance vlastního webového uživatelského rozhraní.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Příklady implementace ICustomWebUi v automatizaci testů: SeleniumWebUI

Tým MSAL.NET přepsal testy uživatelského rozhraní pro použití tohoto mechanismu rozšiřitelnosti. Pokud vás zajímá, podívejte se na třídu [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) ve zdrojovém kódu MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Zajištění skvělých zkušeností s SystemWebViewOptions

Z MSAL.NET 4,1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) můžete zadat:

- Identifikátor URI, který má jít na ( `BrowserRedirectError` ) nebo fragment HTML pro zobrazení ( `HtmlMessageError` ) v případě chyb přihlášení nebo souhlasu v systémovém webovém prohlížeči.
- Identifikátor URI, který se má `BrowserRedirectSuccess` `HtmlMessageSuccess` v případě úspěšného přihlášení nebo vyjádření souhlasu zobrazit () nebo FRAGMENTu HTML
- Akce, která se má spustit pro spuštění prohlížeče systému. Můžete zadat vlastní implementaci nastavením `OpenBrowserAsync` delegáta. Třída také poskytuje výchozí implementaci pro dva prohlížeče: `OpenWithEdgeBrowserAsync` a `OpenWithChromeEdgeBrowserAsync` pro Microsoft Edge a [Microsoft Edge na Chromu v](https://www.windowscentral.com/faq-edge-chromium)uvedeném pořadí.

Chcete-li použít tuto strukturu, napište něco jako v následujícím příkladu:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Další nepovinné parametry

Další informace o všech dalších volitelných parametrech pro `AcquireTokenInteractive` najdete v tématu [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>V MSAL pro iOS a macOS

Cíl-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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

SWIFT

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

V uzlu MSAL získáte tokeny pomocí toku autorizačního kódu s ověřovacím klíčem pro výměnu kódu (PKCE). Proces má dva kroky: nejprve aplikace získá adresu URL, kterou lze použít ke generování autorizačního kódu. Tato adresa URL se dá otevřít v prohlížeči podle volby, kde uživatel může zadat svoje přihlašovací údaje a bude přesměrován zpátky na `redirectUri` (zaregistrované během registrace aplikace) pomocí autorizačního kódu. Za druhé, aplikace předá autorizační kód přijatý metodě, `acquireTokenByCode()` která ho vyměňuje pro přístupový token.

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };
    
    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python neposkytuje přímo metodu pro získání tokenu. Místo toho vyžaduje, aby aplikace odeslala požadavek na autorizaci ve své implementaci toku interakce uživatele, aby získala autorizační kód. Tento kód lze následně předat `acquire_token_by_authorization_code` metodě pro získání tokenu.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])

```
---

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows

Pokud se chcete přihlásit k doméně uživatele v doméně nebo počítači připojeném k Azure AD, použijte integrované ověřování systému Windows (IWA).

### <a name="constraints"></a>Omezení

- Integrované ověřování systému Windows lze použít pouze pro *federované a* uživatele, tedy pro uživatele vytvořené ve službě Active Directory a na základě služby Azure AD. Uživatelé, kteří vytvořili přímo ve službě Azure AD bez zálohování služby Active Directory, se nazývají *spravované* uživatele, nemůžou tento tok ověřování používat. Toto omezení neovlivňuje tok uživatelského jména a hesla.
- IWA je pro aplikace napsané pro platformy .NET Framework, .NET Core a Univerzální platforma Windows (UWP).
- IWA neobejde službu [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md). Pokud je nakonfigurované vícefaktorové ověřování, může IWA selhat, pokud se vyžaduje výzva MFA, protože MFA vyžaduje zásah uživatele.
  
    IWA je neinteraktivní, ale MFA vyžaduje interaktivitu uživatele. Nebudete ovládat, kdy zprostředkovatel identity požaduje, aby bylo provedeno MFA, správce tenanta. Vícefaktorové ověřování se vyžaduje v případě, že se přihlašujete z jiné země nebo oblasti, když se nepřipojí přes síť VPN k podnikové síti, a někdy i při připojení přes VPN. Neočekává se deterministické sady pravidel. Azure AD používá AI k nepřetržitému učení, jestli je potřeba MFA. Přejít zpět na výzvu uživatele, jako je interaktivní ověřování nebo tok kódu zařízení, pokud IWA dojde k chybě.

- Předaná autorita `PublicClientApplicationBuilder` musí být:
  - Tenant ve formuláři `https://login.microsoftonline.com/{tenant}/` , kde `tenant` je buď identifikátor GUID, který představuje ID tenanta nebo doménu přidruženou k tenantovi.
  - Pro všechny pracovní a školní účty: `https://login.microsoftonline.com/organizations/` .
  - Osobní účty Microsoft nejsou podporované. Nemůžete použít klienty/běžné nebo/consumers.

- Protože integrované ověřování systému Windows je tichý tok:
  - Uživatel vaší aplikace musí mít dřív souhlas s používáním aplikace.
  - Nebo je nutné, aby správce tenanta předtím souhlasil se všemi uživateli v tenantovi, aby mohli aplikaci používat.
  - Jinými slovy:
    - Vy jako vývojář jste vybrali tlačítko **udělit** v Azure Portal pro sebe.
    - Nebo správce tenanta na kartě **oprávnění API** registrace pro aplikaci vybral tlačítko **udělení a odvolání souhlasu správce pro {tenant Domain}** . Další informace najdete v tématu [Přidání oprávnění pro přístup k webovému rozhraní API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).
    - Nebo jste poskytli způsob, jak uživatelům udělit souhlas s aplikací. Další informace najdete v tématu [vyžádání souhlasu jednotlivých uživatelů](./v2-permissions-and-consent.md#requesting-individual-user-consent).
    - Nebo jste poskytli způsob, jak správci tenanta udělit souhlas s aplikací. Další informace najdete v tématu [souhlas správce](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

- Tento tok je povolený pro aplikace .NET Desktop, .NET Core a UWP.

Další informace o souhlasu najdete v tématu [oprávnění a souhlas platformy Microsoft Identity Platform](./v2-permissions-and-consent.md).

### <a name="learn-how-to-use-it"></a>Naučte se používat

# <a name="net"></a>[.NET](#tab/dotnet)

V MSAL.NET použijte:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Obvykle potřebujete pouze jeden parametr ( `scopes` ). V závislosti na tom, jak správce Windows zásady nastavil, nemusí mít aplikace na počítači s Windows povoleno vyhledávání přihlášeného uživatele. V takovém případě použijte druhou metodu `.WithUsername()` a předejte uživatelské jméno přihlášeného uživatele jako formát hlavního názvu uživatele (UPN), například `joe@contoso.com` .

Následující příklad prezentuje nejaktuálnější případ s vysvětlením druhu výjimek, které můžete získat a jejich zmírnění.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Seznam možných modifikátorů v AcquireTokenByIntegratedWindowsAuthentication naleznete v tématu [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Tento extrakce pochází z [ukázek Java pro vývoj v MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Tento tok se nevztahuje na macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Tento tok se v uzlu MSAL ještě nepodporuje.

# <a name="python"></a>[Python](#tab/python)

Tento tok se ještě v MSAL Pythonu nepodporuje.

---

## <a name="username-and-password"></a>Uživatelské jméno a heslo

Token můžete získat také zadáním uživatelského jména a hesla. Tento tok je omezený a nedoporučuje se, ale stále existují případy použití, kde je to nezbytné.

### <a name="this-flow-isnt-recommended"></a>Tento tok se nedoporučuje.

Uživatelské jméno a tok hesla se *nedoporučují* , protože aplikace požádá uživatele o heslo, protože není zabezpečené. Další informace najdete v tématu [co je řešení rostoucího problému s hesly?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) Upřednostňovaný tok pro získání tokenu v tichém režimu na počítačích připojených k doméně systému Windows je [integrované ověřování systému Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Můžete také použít [tok kódu zařízení](https://aka.ms/msal-net-device-code-flow).

Použití uživatelského jména a hesla je užitečné v některých případech, například ve scénářích DevOps. Pokud ale chcete použít uživatelské jméno a heslo v interaktivních scénářích, kde máte vlastní uživatelské rozhraní, vezměte v úvahu, jak z něho opustit. Pomocí uživatelského jména a hesla získáte několik věcí:

- Základní principy moderní identity. Heslo může být nepřístupné a přehrajteelné, protože sdílený tajný klíč je možné zachytit. Nejedná se o nekompatibilní bez hesla.
- Uživatelé, kteří potřebují provést MFA, se nemohou přihlásit, protože neexistují žádné interakce.
- Uživatelé nemůžou provádět jednotné přihlašování (SSO).

### <a name="constraints"></a>Omezení

Platí taky následující omezení:

- Tok uživatelského jména a hesla není kompatibilní s podmíněným přístupem a službou Multi-Factor Authentication. V důsledku toho, pokud vaše aplikace běží v tenantovi Azure AD, kde správce tenanta vyžaduje vícefaktorové ověřování, nemůžete tento tok použít. Mnoho organizací to udělat.
- Funguje jenom pro pracovní a školní účty (ne MSA).
- Tok je dostupný na platformě .NET Desktop a .NET Core, ale ne na UWP.

### <a name="b2c-specifics"></a>B2C specifické

Další informace najdete v tématu [přihlašovací údaje pro heslo vlastníka prostředku (ROPC) pomocí B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Použít

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`obsahuje metodu `AcquireTokenByUsernamePassword` .

Následující příklad představuje zjednodušený případ.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

Následující příklad prezentuje nejaktuálnější případ s vysvětlením druhu výjimek, které můžete získat a jejich zmírnění.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Další informace o všech modifikátorech, které lze použít pro `AcquireTokenByUsernamePassword` , naleznete v tématu [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Následující extrakce pochází z [ukázek Java pro vývoj v MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Tento tok není podporován v MSAL pro macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Tento extrakce pochází z [ukázek pro vývoj uzlů MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/username-password). V následujícím fragmentu kódu se uživatelské jméno a heslo pevně zakódované jenom pro ilustraci. To by mělo být zabráněno v produkčním prostředí. Místo toho se doporučuje základní uživatelské rozhraní, které vyzve uživatele k zadání uživatelského jména nebo hesla. 

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

// For testing, enter your username and password below.
// In production, replace this with a UI prompt instead.
const usernamePasswordRequest = {
    scopes: ["user.read"],
    username: "", // Add your username here
    password: "", // Add your password here
};

pca.acquireTokenByUsernamePassword(usernamePasswordRequest).then((response) => {
    console.log("acquired token by password grant");
}).catch((error) => {
    console.log(error);
});
```

# <a name="python"></a>[Python](#tab/python)

Tento extrakce pochází ze [vzorků pro vývoj v Pythonu MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

---

## <a name="command-line-tool-without-a-web-browser"></a>Nástroj příkazového řádku bez webového prohlížeče

### <a name="device-code-flow"></a>Tok kódu zařízení

Pokud vytváříte nástroj příkazového řádku, který nemá webové ovládací prvky, a nemůžete nebo nechcete používat předchozí toky, použijte tok kódu zařízení.

Interaktivní ověřování pomocí Azure AD vyžaduje webový prohlížeč. Další informace najdete v tématu [použití webových prohlížečů](https://aka.ms/msal-net-uses-web-browser). K ověřování uživatelů v zařízeních nebo operačních systémech, které neposkytují webový prohlížeč, tok kódu zařízení umožňuje uživateli používat k interaktivnímu přihlášení jiné zařízení, jako je například počítač nebo mobilní telefon. Pomocí toku kódu zařízení aplikace získá tokeny prostřednictvím procesu se dvěma kroky, který je navržený pro tato zařízení nebo operační systémy. Příklady takových aplikací jsou aplikace, které běží na iOT nebo nástrojích příkazového řádku (CLI). Nápad je následující:

1. Vždy, když je vyžadováno ověření uživatele, aplikace poskytuje kód pro uživatele. Uživatel se vyzve k použití jiného zařízení, jako je například smartphone připojeného k Internetu, k přechodu na adresu URL, například `https://microsoft.com/devicelogin` . Pak se uživateli zobrazí výzva k zadání kódu. To se provede, když webová stránka provede uživatele normálním prostředím ověřování, které zahrnuje výzvy k vyjádření souhlasu a službu Multi-Factor Authentication (v případě potřeby).

2. Po úspěšném ověření obdrží aplikace příkazového řádku požadované tokeny prostřednictvím back-Channel a použije je k provedení volání webového rozhraní API.

### <a name="use-it"></a>Použít

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`obsahuje metodu s názvem `AcquireTokenWithDeviceCode` .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Tato metoda přijímá jako parametry:

- `scopes`Pro vyžádání přístupového tokenu pro.
- Zpětné volání, které přijímá `DeviceCodeResult` .

  ![Vlastnosti DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Následující vzorový kód představuje stručný přehled většiny současných případů a vysvětlení druhu výjimek, které můžete získat a jejich zmírnění. Plně funkční ukázku kódu najdete v tématu [Active-Directory-dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) na GitHubu.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Tento extrakce pochází z [ukázek Java pro vývoj v MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Tento tok se nevztahuje na macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Tento extrakce pochází z [ukázek pro vývoj uzlů MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/device-code).

```JavaScript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const deviceCodeRequest = {
    deviceCodeCallback: (response) => (console.log(response.message)),
    scopes: ["user.read"],
    timeout: 20,
};

pca.acquireTokenByDeviceCode(deviceCodeRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

# <a name="python"></a>[Python](#tab/python)

Tento extrakce pochází ze [vzorků pro vývoj v Pythonu MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

---

## <a name="file-based-token-cache"></a>Mezipaměť tokenů založená na souborech

V MSAL.NET je ve výchozím nastavení poskytována mezipaměť tokenů v paměti.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serializace je přizpůsobitelná v aplikacích klasické pracovní plochy systému Windows a ve webových aplikacích nebo webových rozhraních API

V případě .NET Framework a .NET Core neuděláte nic dalšího, mezipaměť tokenů v paměti trvá po dobu trvání aplikace. Aby bylo možné pochopit, proč se serializace neposkytuje, pamatujte, že MSAL aplikace .NET Desktop nebo .NET Core můžou být aplikace konzoly nebo systému Windows (které by měly přístup k systému souborů), *ale také* webové aplikace nebo webová rozhraní API. Tyto webové aplikace a webová rozhraní API můžou používat určité konkrétní mechanismy ukládání do mezipaměti, jako jsou databáze, distribuované mezipaměti a mezipaměti Redis. Pokud chcete mít aplikaci trvalé mezipaměti tokenů v rozhraní .NET Desktop nebo .NET Core, budete muset serializaci přizpůsobit.

Třídy a rozhraní, které jsou součástí serializace mezipaměti tokenů, jsou následující typy:

- ``ITokenCache``, který definuje události pro přihlášení k odběru požadavků na serializaci mezipaměti tokenů, a metody pro serializaci nebo deserializaci mezipaměti v různých formátech (ADAL v 3.0, MSAL 2. x a MSAL 3. x = ADAL v 5.0).
- ``TokenCacheCallback`` je zpětné volání předané do událostí, aby bylo možné zpracovat serializaci. Budou volány s argumenty typu ``TokenCacheNotificationArgs`` .
- ``TokenCacheNotificationArgs`` poskytuje pouze aplikaci ``ClientId`` a odkaz na uživatele, pro který je token k dispozici.

  ![Diagram serializace mezipaměti tokenů](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET vytvoří mezipaměť tokenů za vás a poskytne `IToken` mezipaměť při volání `UserTokenCache` vlastností aplikace a `AppTokenCache` . Nechcete implementovat rozhraní sami. Vaše zodpovědnost při implementaci serializace mezipaměti vlastního tokenu je následující:
>
> - Reakce na `BeforeAccess` `AfterAccess` události a nebo jejich *asynchronní* protějšky. `BeforeAccess`Delegát zodpovídá za deserializaci mezipaměti. `AfterAccess`Delegát zodpovídá za serializaci mezipaměti.
> - Pochopení, že součást těchto událostí ukládá nebo načítá objekty blob, které jsou předány argumentem události do libovolného úložiště, které chcete.

Strategie se liší v závislosti na tom, jestli píšete serializaci mezipaměti tokenů pro veřejnou klientskou aplikaci, jako je stolní počítač nebo důvěrná klientská aplikace, jako je webová aplikace nebo webové rozhraní API nebo aplikace typu démon.

Vzhledem k tomu, že MSAL v2. x, máte několik možností. Vaše volba závisí na tom, jestli chcete mezipaměť serializovat jenom do formátu MSAL.NET, což je sjednocená mezipaměť formátu, která je společná pro MSAL, ale také napříč platformami. Případně můžete také chtít podporovat serializaci mezipaměti pro [starší verze](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) knihovny ADAL v3.

Přizpůsobení serializace mezipaměti tokenů pro sdílení stavu jednotného přihlašování mezi ADAL.NET 3. x, ADAL.NET 5. x a MSAL.NET je vysvětleno v části ukázka [Active-Directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Jednoduchá serializace mezipaměti tokenů (jenom MSAL)

Následující příklad je Naive implementace vlastního serializace mezipaměti tokenů pro aplikace klasické pracovní plochy. Tady je mezipaměť tokenu uživatele v souboru ve stejné složce jako aplikace, nebo v případě, že je aplikace [zabalená desktopová aplikace](/windows/msix/desktop/desktop-to-uwp-behind-the-scenes), a to v případě jednotlivých uživatelů na složku aplikace. Úplný kód naleznete v následující ukázce: [Active-Directory-dotnet-Desktop-MSGraph-v2](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2).

Po sestavení aplikace povolte serializaci voláním ``TokenCacheHelper.EnableSerialization()`` a předáním aplikace `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Tato pomocná třída vypadá jako následující fragment kódu:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
   try
   {
    // For packaged desktop apps (MSIX packages) the executing assembly folder is read-only. 
    // In that case we need to use Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path + "\msalcache.bin" 
    // which is a per-app read/write folder for packaged apps.
    // See https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-behind-the-scenes
    CacheFilePath = System.IO.Path.Combine(Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path, "msalcache.bin3");
   }
   catch (System.InvalidOperationException)
   {
    // Fall back for an un-packaged desktop app
    CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin";
   }
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static string CacheFilePath { get; private set; }

  private static readonly object FileLock = new object();

  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Ve verzi Preview je možné serializátor založený na souborové mezipaměti s tokeny kvality produktu pro klientské aplikace běžící v systému Windows, Mac a Linux, které jsou k dispozici v open source knihovně [Microsoft. identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . Můžete ji zahrnout do svých aplikací z následujícího balíčku NuGet: [Microsoft. identity. Client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Právní omezení: Knihovna Microsoft. identity. Client. Extensions. Msal je rozšířením nad MSAL.NET. Třídy v těchto knihovnách mohou v budoucnu vést do MSAL.NET, jak je, nebo s nezměněnými změnami.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serializace mezipaměti s duálním tokenem (MSAL Unified cache + ADAL V3)

Je možné, že budete chtít implementovat serializaci mezipaměti tokenů s formátem Unified cache. Tento formát je společný jako ADAL.NET 4. x a MSAL.NET 2. x a s jinými MSALs stejné generace nebo staršími verzemi na stejné platformě. Získejte nechte inspirovat pomocí následujícího kódu:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Tentokrát pomocná třída vypadá jako následující kód:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Upřesnit Přístup k tokenům v mezipaměti uživatele v aplikacích a službách na pozadí

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [zavolejte webové rozhraní API z desktopové aplikace](scenario-desktop-call-api.md).
