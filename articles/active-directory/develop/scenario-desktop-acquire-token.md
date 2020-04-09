---
title: Získání tokenu pro volání webového rozhraní API (aplikace klasické pracovní plochy) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak vytvořit desktopovou aplikaci, která volá webová API k získání tokenu pro aplikaci.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a5942a9d614bbb06fadb1d4b16d4c68c007434c7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885314"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Desktopová aplikace, která volá webová api: Získání tokenu

Po vytvoření instance aplikace veřejného klienta ji použijete k získání tokenu, který pak použijete k volání webového rozhraní API.

## <a name="recommended-pattern"></a>Doporučený vzor

Webové rozhraní API je `scopes`definováno jeho . Bez ohledu na zkušenosti, které poskytujete ve vaší aplikaci, vzor, který chcete použít, je:

- Systematicky se pokoušet získat token `AcquireTokenSilent`z mezipaměti tokenu voláním .
- Pokud se toto `AcquireToken` volání nezdaří, použijte tok, `AcquireTokenXX`který chcete použít, který je zde reprezentován .

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

# <a name="macos"></a>[Macos](#tab/macOS)

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
Swift:

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
---

Zde jsou různé způsoby, jak získat tokeny v desktopové aplikaci.

## <a name="acquire-a-token-interactively"></a>Interaktivní získání tokenu

Následující příklad ukazuje minimální kód získat token interaktivně pro čtení profilu uživatele pomocí aplikace Microsoft Graph.

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

`AcquireTokenInteractive`má pouze jeden ``scopes``povinný parametr , který obsahuje výčet řetězců, které definují obory, pro které je vyžadován token. Pokud je token pro Microsoft Graph, požadované obory najdete v odkazu rozhraní API každého rozhraní API aplikace Microsoft Graph v části s názvem Oprávnění. Chcete-li například [uvést kontakty uživatele](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), musí být použit obor "User.Read", "Contacts.Read". Další informace naleznete v [tématu Odkaz na oprávnění aplikace Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

V systému Android je také nutné `.WithParentActivityOrWindow`zadat nadřazenou aktivitu pomocí aplikace , jak je znázorněno, aby se token po interakci vrátil k této nadřazené aktivitě. Pokud ji nezadáte, je při volání `.ExecuteAsync()`vyvolána výjimka .

### <a name="specific-optional-parameters-in-msalnet"></a>Specifické volitelné parametry v MSAL.NET

#### <a name="withparentactivityorwindow"></a>SParentActivityOrWindow

UI je důležité, protože je interaktivní. `AcquireTokenInteractive`má jeden konkrétní volitelný parametr, který může určit, pro platformy, které jej podporují, nadřazené ui. Při použití v desktopové aplikaci, `.WithParentActivityOrWindow` má jiný typ, který závisí na platformě.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Poznámky:

- Na .NET Standard, `object` `Activity` očekávané je `UIViewController` na Android, `NSWindow` na iOS, na MAC a `IWin32Window` nebo `IntPr` v systému Windows.
- V systému Windows `AcquireTokenInteractive` je nutné volat z vlákna uživatelského rozhraní tak, aby vložený prohlížeč získá odpovídající kontext synchronizace uživatelského rozhraní. Není volání z vlákna uživatelského rozhraní může způsobit zprávy není správně pumpovat a zablokování scénáře s uživatelského rozhraní. Jedním ze způsobů volání knihoven ověřování Společnosti Microsoft (MSALs) z vlákna uživatelského rozhraní, `Dispatcher` pokud již nejste ve vlákně uživatelského rozhraní, je použití na WPF.
- Pokud používáte WPF, chcete-li získat okno z ovládacího `WindowInteropHelper.Handle` prvku WPF, můžete použít třídu. Pak je volání z ovládacího`this`prvku WPF ( ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`se používá k řízení interaktivity s uživatelem zadáním výzvy.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Třída definuje následující konstanty:

- ``SelectAccount``vynutí, aby sts představildialogové okno výběru účtu, které obsahuje účty, pro které má uživatel relaci. Tato možnost je užitečná, když vývojáři aplikací chtějí uživatelům uvolit výběr mezi různými identitami. Tato možnost pohání MSAL k odeslání ``prompt=select_account`` zprostředkovateli identity. Tato možnost je výchozí. To dělá dobrou práci poskytovat nejlepší možné zkušenosti na základě dostupných informací, jako je například účet a přítomnost relace pro uživatele. Neměňte to, pokud k tomu nemáte dobrý důvod.
- ``Consent``umožňuje vývojáři aplikace vynutit, aby byl uživatel vyzván k udělení souhlasu, a to i v případě, že byl souhlas udělen dříve. V tomto případě MSAL odešle `prompt=consent` zprostředkovateli identity. Tuto možnost lze použít v některých aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžadují, aby se uživateli při každém použití aplikace zobrazovalo dialogové okno souhlasu.
- ``ForceLogin``umožňuje vývojáři aplikace, aby uživatel vyzván i o pověření službou, a to i v případě, že tento uživatel výzvu nemusí být potřeba. Tato možnost může být užitečné, aby uživatel přihlásit znovu, pokud získání tokenu selže. V tomto případě MSAL odešle `prompt=login` zprostředkovateli identity. Někdy se používá v aplikacích zaměřených na zabezpečení, kde zásadsprávné řízení organizace vyžaduje, aby se uživatel znovu přihlašoval pokaždé, když přistupuje k určitým částem aplikace.
- ``Never``(pouze pro rozhraní .NET 4.5 a WinRT) nevyzve uživatele, ale místo toho se pokusí použít soubor cookie uložený ve skrytém vloženém webovém zobrazení. Další informace naleznete v tématu webová zobrazení v MSAL.NET. Použití této možnosti může selhat. V takovém `AcquireTokenInteractive` případě vyvolá výjimku upozornit, že je potřeba interakce ui. Budete muset použít jiný `Prompt` parametr.
- ``NoPrompt``neodešle žádné výzvy poskytovateli identity. Tato možnost je užitečná jenom pro zásady profilu profilu B2C služby Azure Active Directory (Azure AD). Další informace naleznete [v tématu Azure AD B2C specifika](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Tento modifikátor se používá v rozšířeném scénáři, kde chcete, aby uživatel předem souhlasil s několika prostředky předem a nechcete používat přírůstkový souhlas, který se obvykle používá s MSAL.NET/the platformou identit y Microsoft. Další informace naleznete [v tématu Mít souhlas uživatele předem pro několik zdrojů](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>S CustomWebUi

Webové uživatelské prostředí je mechanismus pro vyvolání prohlížeče. Tento mechanismus může být vyhrazený ovládací prvek webového prohlížeče uživatelského prostředí nebo způsob, jak delegovat otevření prohlížeče.
MSAL poskytuje webové implementace uživatelského prostředí pro většinu platforem, ale existují případy, kdy můžete chtít hostovat prohlížeč sami:

- Platformy, které nejsou explicitně pokryty MSAL, například Blazor, Unity a Mono na stolních počítačích.
- Chcete ui otestovat aplikaci a používat automatizovaný prohlížeč, který lze použít se selenem.
- Prohlížeč a aplikace, které spouštějí MSAL jsou v samostatných procesech.

##### <a name="at-a-glance"></a>Na první pohled

Chcete-li toho dosáhnout, dáte `start Url`MSAL , který musí být zobrazen v prohlížeči volby tak, aby koncový uživatel může zadat položky, jako je jejich uživatelské jméno.
Po dokončení ověřování, vaše aplikace musí předat `end Url`zpět do MSAL , který obsahuje kód poskytovaný Azure AD.
Hostitel `end Url` je vždy `redirectUri`. Chcete-li provést zachycení `end Url`, proveďte jednu z následujících věcí:

- Monitor prohlížeč přesměruje, dokud `redirect Url` je hit.
- Chcete, aby prohlížeč přesměroval na adresu URL, kterou sledujete.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi je bod rozšiřitelnosti

`WithCustomWebUi`je bod rozšiřitelnosti, který můžete použít k poskytnutí vlastního ui ve veřejných klientských aplikacích. Můžete také nechat uživatele projít /Authorize koncového bodu zprostředkovatele identity a nechat ho přihlásit a souhlas. MSAL.NET pak můžete uplatnit ověřovací kód a získat token. Například se používá v sadě Visual Studio mít elektrony aplikace (například Visual Studio Feedback) poskytují webové interakce, ale nechat MSAL.NET k většině práce. Můžete také použít, pokud chcete poskytnout automatizaci uživatelského rozhraní. Ve veřejných klientských aplikacích MSAL.NET používá standard Proof Key for Code Exchange (PKCE), aby bylo zajištěno, že je respektováno zabezpečení. Kód lze uplatnit pouze MSAL.NET. Další informace naleznete v tématu [RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Použití s VlastníWebUi

Chcete-li použít `.WithCustomWebUI`, postupujte takto.

  1. Implementujte rozhraní `ICustomWebUi`. Další informace naleznete na [tomto webu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implementujte `AcquireAuthorizationCodeAsync`jednu metodu a přijměte adresu URL autorizačního kódu vypočítanou MSAL.NET. Pak nechte uživatele projít interakci s poskytovatelem identity a vrátit zpět adresu URL, podle které by poskytovatel identity volal implementaci zpět spolu s autorizačním kódem. Pokud máte problémy, implementace `MsalExtensionException` by měla vyvolat výjimku pěkně spolupracovat s MSAL.
  2. Při `AcquireTokenInteractive` volání použijte `.WithCustomUI()` modifikátor předávání instance vlastního webového uživatelského uživatelského nastavení.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Příklady implementace ICustomWebUi v automatizaci testů: SeleniumWebUI

Tým MSAL.NET přepsal testy ui použít tento mechanismus rozšiřitelnosti. Pokud máte zájem, podívejte se na [seleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) třídy v MSAL.NET zdrojový kód.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Poskytněte skvělé zkušenosti s SystemWebViewOptions

Od MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)můžete zadat:

- Identifikátor URI pro`BrowserRedirectError`zobrazení ( ) nebo`HtmlMessageError`fragment HTML, který se má zobrazit ( ) v případě chyb přihlášení nebo souhlasu v systémovém webovém prohlížeči.
- Identifikátor URI pro`BrowserRedirectSuccess`použití v zařízení (`HtmlMessageSuccess`) nebo fragment HTML, který se má zobrazit ( ) v případě úspěšného přihlášení nebo souhlasu.
- Akce, která má být spuštěna pro spuštění systémového prohlížeče. Můžete zadat vlastní implementaci nastavením delegáta. `OpenBrowserAsync` Třída také poskytuje výchozí implementaci pro `OpenWithEdgeBrowserAsync` dva `OpenWithChromeEdgeBrowserAsync` prohlížeče: a pro Microsoft Edge a [Microsoft Edge na chromu](https://www.windowscentral.com/faq-edge-chromium), v uvedeném pořadí.

Chcete-li použít tuto strukturu, napište něco jako následující příklad:

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

#### <a name="other-optional-parameters"></a>Další volitelné parametry

Další informace o všech ostatních `AcquireTokenInteractive`volitelných parametrech aplikace naleznete v tématu [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

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

# <a name="python"></a>[Python](#tab/python)

MSAL Python neposkytuje interaktivní metodu získat token přímo. Místo toho vyžaduje, aby aplikace odeslala žádost o autorizaci při implementaci toku interakce uživatele, aby získala autorizační kód. Tento kód pak může `acquire_token_by_authorization_code` být předán metodě získat token.

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

# <a name="macos"></a>[Macos](#tab/macOS)

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

Swift:

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
---

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows

Pokud chcete přihlásit uživatele domény v doméně nebo počítači se službou Azure AD, použijte integrované ověřování systému Windows (IWA).

### <a name="constraints"></a>Omezení

- Integrované ověřování systému Windows je použitelné pouze pro *federované+* uživatele, to znamená pro uživatele vytvořené ve službě Active Directory a podporované službou Azure AD. Uživatelé vytvořená přímo ve službě Azure AD bez podpory služby Active Directory, označovaná jako *spravovaní* uživatelé, nemohou tento tok ověřování používat. Toto omezení nemá vliv na tok uživatelského jména a hesla.
- IWA je pro aplikace napsané pro rozhraní .NET Framework, .NET Core a platformy Usb Platform (UPW).
- IWA neobcemvíce a ověřování vícefaktorové (MFA). Pokud je nakonfigurováno vícefaktorové ověřování, iwa může selhat, pokud je vyžadována výzva vícefaktorové ověřování, protože vícefaktorové ověřování vyžaduje interakci uživatele.
  > [!NOTE]
  > Tahle je ošemetná. IWA není interaktivní, ale vícefaktorové ověřování vyžaduje interaktivitu uživatelů. Nemáte řídit, kdy poskytovatel identity požaduje MFA, které mají být provedeny, správce klienta dělá. Z našich pozorování je vyžadováno vícefaktorové ověřování, když se přihlásíte z jiné země, když není připojeno přes VPN k podnikové síti a někdy i při připojení přes VPN. Neočekávejte deterministický soubor pravidel. Azure AD používá AI průběžně zjistit, pokud je vyžadováno vícefaktorové. Vraťte se zpět k uživatelské výzvě, jako je interaktivní ověřování nebo tok kódu zařízení, pokud iwa selže.

- Orgán, který `PublicClientApplicationBuilder` byl schválen, musí být:
  - Tenanted formuláře `https://login.microsoftonline.com/{tenant}/`, `tenant` kde je buď GUID, který představuje ID klienta nebo domény přidružené k tenantovi.
  - Pro všechny pracovní a `https://login.microsoftonline.com/organizations/`školní účty: .
  - Osobní účty Microsoft nejsou podporovány. Nelze použít /common nebo /consumers tenants.

- Vzhledem k tomu, že integrované ověřování systému Windows je tichý tok:
  - Uživatel vaší aplikace musí mít dříve souhlas s použitím aplikace.
  - Nebo správce klienta musí mít dříve souhlas pro všechny uživatele v tenantovi používat aplikaci.
  - Jinými slovy:
    - Buď jste jako vývojář vybrali tlačítko **Grant** na webu Azure portal pro sebe.
    - Nebo správce klienta vybral **souhlas správce udělení nebo odvolání pro {doménu klienta}** na kartě oprávnění rozhraní **API** registrace pro aplikaci. Další informace naleznete v tématu [Přidání oprávnění k přístupu k webovým sítím API](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis).
    - Nebo jste poskytli způsob, jak mohou uživatelé souhlasit s aplikací. Další informace naleznete [v tématu Žádost o souhlas jednotlivých uživatelů](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent).
    - Nebo jste poskytli způsob, jak správce klienta souhlas s aplikací. Další informace naleznete v [tématu Admin consent](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant).

- Tento tok je povolen pro desktopové aplikace .NET, .NET Core a UPW.

Další informace o souhlasu naleznete v [tématu Oprávnění a souhlas platformy identit společnosti Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).

### <a name="learn-how-to-use-it"></a>Naučte se, jak ji používat

# <a name="net"></a>[.NET](#tab/dotnet)

V MSAL.NET je třeba použít:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Obvykle potřebujete pouze jeden`scopes`parametr ( ). V závislosti na způsobu, jakým správce systému Windows nastavil zásady, nemusí být aplikacím v počítači se systémem Windows povoleno vyhledat přihlášeného uživatele. V takovém případě použijte druhou `.WithUsername()`metodu a předajte uživatelské jméno přihlášeného uživatele ve `joe@contoso.com`formátu HLAVNÍ HO DUP. Na .NET Core je k dispozici pouze přetížení s uživatelským jménem, protože platforma .NET Core nemůže požádat o uživatelské jméno operačního systému.

Následující ukázka představuje nejaktuálnější případ s vysvětlením druhu výjimek, které můžete získat, a jejich skutečnosti snižující závažnost rizika.

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

Seznam možných modifikátorů na AcquireTokenByIntegratedWindowsAuthentication naleznete v tématu [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Tento výňatek je ze [vzorků msal Java dev .](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

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

# <a name="python"></a>[Python](#tab/python)

Tento tok ještě není podporován v MSAL Pythonu.

# <a name="macos"></a>[Macos](#tab/macOS)

Tento tok se nevztahuje na MacOS.

---

## <a name="username-and-password"></a>Uživatelské jméno a heslo

Token můžete také získat zadáním uživatelského jména a hesla. Tento tok je omezený a nedoporučuje se, ale stále existují případy použití, kde je to nezbytné.

### <a name="this-flow-isnt-recommended"></a>Tento tok se nedoporučuje

Tento tok *se nedoporučuje,* protože s vaší aplikace požádat uživatele o jeho heslo není zabezpečené. Další informace naleznete [v tématu Jaké je řešení rostoucího problému hesel?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Upřednostňovaným tokem pro tiché získání tokenu v počítačích s doménami Windows je [integrované ověřování systému Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Můžete také použít [tok kódu zařízení](https://aka.ms/msal-net-device-code-flow).

> [!NOTE]
> Použití uživatelského jména a hesla je užitečné v některých případech, například scénáře DevOps. Ale pokud chcete použít uživatelské jméno a heslo v interaktivních scénářích, kde zadáte vlastní uživatelské rozhraní, přemýšlejte o tom, jak se od něj odklonit. Použitím uživatelského jména a hesla se vzdáváte řady věcí:
>
> - Základní principy moderní identity. Heslo může získat phished a přehrány, protože sdílené tajné klíče mohou být zachyceny. Je to nekompatibilní s heslem.
> - Uživatelé, kteří potřebují provádět vícefaktorové hodnocení, se nemohou přihlásit, protože neexistuje žádná interakce.
> - Uživatelé nelze provést jednotné přihlašování (SSO).

### <a name="constraints"></a>Omezení

Platí také následující omezení:

- Tok uživatelského jména a hesla není kompatibilní s podmíněným přístupem a vícefaktorovým ověřováním. V důsledku toho pokud vaše aplikace běží v tenantovi Azure AD, kde správce tenanta vyžaduje vícefaktorové ověřování, nemůžete použít tento tok. To dělá mnoho organizací.
- Funguje pouze pro pracovní a školní účty (ne MSA).
- Tok je k dispozici na ploše .NET a .NET Core, ale ne na UPW.

### <a name="b2c-specifics"></a>Specifika B2C

Další informace naleznete v [tématu Pověření hesla vlastníka prostředku (ROPC) s B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Použijte ji

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`obsahuje metodu `AcquireTokenByUsernamePassword`.

Následující ukázka představuje zjednodušený případ.

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

Následující ukázka představuje nejaktuálnější případ s vysvětlením druhu výjimek, které můžete získat, a jejich skutečnosti snižující závažnost rizika.

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
                    .ExecuteAync();
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

Další informace o všech modifikátorech, `AcquireTokenByUsernamePassword`které lze použít , naleznete v [tématu AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Následující výňatek je ze [vzorků dev MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

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

# <a name="python"></a>[Python](#tab/python)

Tento výňatek je z [msal python dev ukázky](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

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

# <a name="macos"></a>[Macos](#tab/macOS)

Tento tok není podporován na MSAL pro macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Nástroj příkazového řádku bez webového prohlížeče

### <a name="device-code-flow"></a>Tok kódu zařízení

Pokud píšete nástroj příkazového řádku, který nemá webové ovládací prvky a nemůžete nebo nechcete používat předchozí toky, musíte použít tok kódu zařízení.

Interaktivní ověřování pomocí Azure AD vyžaduje webový prohlížeč. Další informace naleznete v [tématu Použití webových prohlížečů](https://aka.ms/msal-net-uses-web-browser). Při ověřování uživatelů na zařízeních nebo operačních systémech, které neposkytují webový prohlížeč, umožňuje tok kódu zařízení uživateli používat jiné zařízení, například počítač nebo mobilní telefon, k interaktivnímu přihlášení. Pomocí toku kódu zařízení aplikace získá tokeny prostřednictvím dvoustupňového procesu, který je určen pro tato zařízení nebo operačních systémů. Příklady takových aplikací jsou aplikace, které běží na iOT nebo nástroje příkazového řádku (CLI). Myšlenka spoje, že:

1. Kdykoli je vyžadováno ověření uživatele, aplikace poskytuje kód pro uživatele. Uživatel je požádán, aby použil jiné zařízení, například smartphone připojený k internetu, k přejděte na adresu URL, `https://microsoft.com/devicelogin`například . Poté je uživatel vyzván k zadání kódu. To bylo hotovo, webová stránka vede uživatele prostřednictvím normální ověřování prostředí, které zahrnuje výzvy souhlasu a vícefaktorové ověřování, v případě potřeby.

2. Po úspěšném ověření aplikace příkazového řádku obdrží požadované tokeny prostřednictvím zadního kanálu a používá je k provádění volání webového rozhraní API, které potřebuje.

### <a name="use-it"></a>Použijte ji

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`obsahuje metodu `AcquireTokenWithDeviceCode`s názvem .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Tato metoda bere jako parametry:

- Chcete-li `scopes` požádat o přístupový token.
- Zpětné volání, které `DeviceCodeResult`přijímá .

  ![Vlastnosti DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Následující ukázkový kód představuje nejaktuálnější případ s vysvětlením druhu výjimek, které můžete získat, a jejich zmírnění.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

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
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
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

Tento výňatek je ze [vzorků msal Java dev .](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

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

# <a name="python"></a>[Python](#tab/python)

Tento výňatek je z [msal python dev ukázky](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

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

# <a name="macos"></a>[Macos](#tab/macOS)

Tento tok se nevztahuje na MacOS.

---

## <a name="file-based-token-cache"></a>Mezipaměť tokenů založených na souborech

V MSAL.NET je ve výchozím nastavení k dispozici mezipaměť tokenů v paměti.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serializace je přizpůsobitelná v desktopových aplikacích pro Windows a webových aplikacích nebo webových apich

V případě rozhraní .NET Framework a .NET Core, pokud nechcete dělat nic navíc, mezipaměť tokenů v paměti trvá po dobu trvání aplikace. Chcete-li pochopit, proč serializace není k dispozici inas, nezapomeňte, že msal .NET desktop nebo .NET Core aplikace mohou být konzolové nebo Windows aplikace (které by měly přístup k systému souborů), *ale také* webové aplikace nebo webové rozhraní API. Tyto webové aplikace a webová api mohou používat některé specifické mechanismy mezipaměti, jako jsou databáze, distribuované mezipaměti a mezipaměti Redis. Chcete-li mít trvalé aplikace mezipaměti tokenů v rozhraní .NET desktop nebo .NET Core, budete muset přizpůsobit serializaci.

Třídy a rozhraní, které se účastní serializace mezipaměti tokenů, jsou následující typy:

- ``ITokenCache``, který definuje události, které se přihlásí k odběru požadavků na serializaci mezipaměti tokenů, a metody serializace nebo deserializace mezipaměti v různých formátech (ADAL v3.0, MSAL 2.x a MSAL 3.x = ADAL v5.0).
- ``TokenCacheCallback``je zpětné volání předané události, takže můžete zpracovat serializace. Budou voláni s argumenty ``TokenCacheNotificationArgs``typu .
- ``TokenCacheNotificationArgs``poskytuje pouze ``ClientId`` aplikaci a odkaz na uživatele, pro kterého je token k dispozici.

  ![Diagram serializace mezipaměti tokenů](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET vytvoří mezipaměti tokenů pro `IToken` vás a poskytuje cache `UserTokenCache` při `AppTokenCache` volání aplikace a vlastnosti. Nemáte implementovat rozhraní sami. Vaše odpovědnost, při implementaci vlastní serializace mezipaměti tokenů, je:
>
> - Reagovat `BeforeAccess` `AfterAccess` na události, nebo jejich *asynchronní* protějšek. Delegát`BeforeAccess` je zodpovědný za rekonstrukci mezipaměti. Delegát `AfterAccess` je zodpovědný za serializaci mezipaměti.
> - Pochopit, že část těchto událostí ukládat nebo načíst objekty BLOB, které jsou předány prostřednictvím argumentu události do libovolného úložiště, které chcete.

Strategie se liší v závislosti na tom, pokud píšete serializaci mezipaměti tokenů pro veřejnou klientskou aplikaci, jako je například desktop, nebo důvěrná klientská aplikace, jako je například webová aplikace nebo webové rozhraní API nebo aplikace daemon.

Vzhledem k tomu, MSAL v2.x, máte několik možností. Vaše volba závisí na tom, zda chcete serializovat mezipaměť pouze do formátu MSAL.NET, což je sjednocená formátová mezipaměť, která je běžná s MSAL, ale také napříč platformami. Nebo můžete také chtít podporovat [serializaci starší](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) mezipaměti tokenu ADAL v3.

Přizpůsobení serializace mezipaměti tokenů pro sdílení stavu spři mezi ADAL.NET 3.x, ADAL.NET 5.x a MSAL.NET je vysvětleno v části ukázkové [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Jednoduchá serializace mezipaměti tokenů (pouze MSAL)

Následující příklad je naivní implementace vlastní serializace mezipaměti tokenů pro desktopové aplikace. Zde je mezipaměť tokenů uživatele v souboru ve stejné složce jako aplikace.

Po sestavení aplikace povolíte serializaci voláním ``TokenCacheHelper.EnableSerialization()`` a `UserTokenCache`předáním aplikace .

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
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

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

Náhled serializátoru na základě souborů mezipaměti tokenů kvality produktu pro veřejné klientské aplikace pro desktopové aplikace spuštěné v systémech Windows, Mac a Linux je k dispozici v open source knihovně [Microsoft.Identity.Client.Extensions.Msal.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) Můžete zahrnout do svých aplikací z následujícího balíčku NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Upozornění: Knihovna Microsoft.Identity.Client.Extensions.Msal je rozšíření mMSAL.NET. Třídy v těchto knihovnách může učinit jejich cestu do MSAL.NET v budoucnu, jak je nebo s nejnovější změny.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serializace mezipaměti se dvěma tokeny (sjednocená vyrovnávací paměť MSAL + ADAL v3)

Serializaci mezipaměti tokenů můžete chtít implementovat pomocí formátu sjednocené mezipaměti. Tento formát je společný pro ADAL.NET 4.x a MSAL.NET 2.x a s jinými msaly stejné generace nebo staršími na stejné platformě. Nechte se inspirovat následujícím kódem:

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání webového rozhraní API z aplikace klasické pracovní plochy](scenario-desktop-call-api.md)
