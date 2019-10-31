---
title: Aplikace klasické pracovní plochy, která volá webová rozhraní API (získá token pro aplikaci) – Microsoft Identity Platform
description: Informace o tom, jak vytvořit desktopovou aplikaci, která volá webová rozhraní API (získání tokenu pro aplikaci |)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce79a2dcbb0d79d84019c350eb4693160c8f7d50
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175459"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Aplikace klasické pracovní plochy, která volá webová rozhraní API – získat token

Po vytvoření instance veřejné klientské aplikace ji použijete k získání tokenu, který pak použijete k volání webového rozhraní API.

## <a name="recommended-pattern"></a>Doporučený vzor

Webové rozhraní API je definováno jeho `scopes`. Bez ohledu na možnosti, které zadáte ve své aplikaci, je vzor, který chcete použít:

- Systematické pokus o získání tokenu z mezipaměti tokenů voláním `AcquireTokenSilent`
- Pokud se toto volání nezdařilo, použijte tok `AcquireToken`, který chcete použít (zde reprezentované `AcquireTokenXX`).

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>V MSAL.NET

```CSharp
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

# <a name="javatabjava"></a>[Java](#tab/java)

```java
CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();
    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

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
---

Tady je podrobné informace o různých způsobech získání tokenů v desktopové aplikaci.

## <a name="acquiring-a-token-interactively"></a>Interaktivní získání tokenu

Následující příklad ukazuje minimální kód pro interaktivní získání tokenu pro čtení profilu uživatele pomocí Microsoft Graph.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>V MSAL.NET

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

`AcquireTokenInteractive` má ``scopes``pouze jeden povinný parametr, který obsahuje výčet řetězců definujících obory, pro které je vyžadován token. Pokud je token pro Microsoft Graph, požadované obory najdete v referenčních informacích k rozhraní API pro každé rozhraní API Microsoft Graph v části s názvem "oprávnění". Pokud například chcete [Zobrazit seznam kontaktů uživatele](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), bude nutné použít rozsah "User. Read", "Contacts. Read". Viz také [Microsoft Graph odkaz na oprávnění](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

V systému Android je nutné zadat také nadřazenou aktivitu (pomocí `.WithParentActivityOrWindow`, viz níže), aby se token po interakci vrátil zpět do této nadřazené aktivity. Pokud ho nezadáte, vyvolá se při volání `.ExecuteAsync()`výjimka.

### <a name="specific-optional-parameters-in-msalnet"></a>Konkrétní volitelné parametry v MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Je to interaktivní, uživatelské rozhraní je důležité. `AcquireTokenInteractive` má jeden konkrétní volitelný parametr, který umožňuje určit, pro platformy, které ho podporují, nadřazené uživatelské rozhraní. Při použití v desktopové aplikaci má `.WithParentActivityOrWindow` v závislosti na platformě jiný typ:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Mark

- V .NET Standard je očekávaný `object` `Activity` na Androidu, na `UIViewController` v systému iOS, na `NSWindow` na Macu a na `IWin32Window` nebo `IntPr` ve Windows.
- V systému Windows je nutné volat `AcquireTokenInteractive` z vlákna uživatelského rozhraní tak, aby vložený prohlížeč dostal příslušný kontext synchronizace uživatelského rozhraní.  Nevolání z vlákna uživatelského rozhraní může způsobit, že zprávy nezpůsobí správné nebo zablokování scénářů pomocí uživatelského rozhraní. Jedním ze způsobů, jak volat MSAL z vlákna uživatelského rozhraní, pokud nejste na vlákně uživatelského rozhraní, se už používá `Dispatcher` v WPF.
- Pokud používáte WPF, chcete-li získat okno z ovládacího prvku WPF, můžete použít třídu `WindowInteropHelper.Handle`. Volání je pak z ovládacího prvku WPF (`this`):

  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` slouží k řízení interaktivity s uživatelem zadáním výzvy.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Třída definuje následující konstanty:

- ``SelectAccount``: vynutí, aby služba STS obsahovala dialog pro výběr účtu, který obsahuje účty, pro které má uživatel relaci. Tato možnost je užitečná, když vývojáři aplikací chtějí uživateli zvolit mezi různými identitami. Tato možnost Drives MSAL odesílá ``prompt=select_account`` poskytovateli identity. Tato možnost je výchozí a má dobrou úlohu při poskytování nejlepšího možného prostředí na základě dostupných informací (účtu, přítomnosti relace pro uživatele atd.). ...). Neměňte ho, pokud nemáte dobrý důvod to udělat.
- ``Consent``: umožňuje vývojáři aplikace vynutit, aby se uživateli zobrazila výzva k vyjádření souhlasu i v případě, že byl souhlas udělen dříve. V tomto případě MSAL odesílá `prompt=consent` poskytovateli identity. Tato možnost se dá použít v některých aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžadují, aby se uživateli zobrazoval dialog pro vyjádření souhlasu při každém použití aplikace.
- ``ForceLogin``: umožňuje vývojářům aplikací, aby se uživateli zobrazila výzva k zadání přihlašovacích údajů, i v případě, že by tento uživatel nemohl být nutný. Tato možnost může být užitečná, pokud se získání tokenu nepovede, aby se uživatel mohl znovu přihlásit. V tomto případě MSAL odesílá `prompt=login` poskytovateli identity. Znovu jsme viděli, že se používá v některých aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžadují, aby se uživatel znovu přihlásil – pokaždé, když přistupují k určitým částem aplikace.
- ``Never`` (jenom pro .NET 4,5 a WinRT) se uživateli nezobrazí výzva, ale místo toho se pokusí použít soubor cookie uložený v skrytém vloženém webovém zobrazení (viz níže: Webová zobrazení v MSAL.NET). Použití této možnosti může selhat a v takovém případě `AcquireTokenInteractive` vyvolá výjimku, která upozorní, že je potřeba interakce uživatelského rozhraní, a vy budete muset použít jiný parametr `Prompt`.
- ``NoPrompt``: nepošle žádné výzvy poskytovateli identity. Tato možnost je užitečná jenom pro Azure AD B2C úpravou zásad profilu (viz téma [specifické pro B2C](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Tento modifikátor se používá v pokročilém scénáři, kdy chcete uživateli předběžně odsouhlasit několik zdrojů (a nechcete používat přírůstkový souhlas, který se obvykle používá s MSAL.NET nebo platformou Microsoft Identity). Podrobnosti najdete v tématu [Postupy: použití souhlasu uživatele před několika prostředky](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Webové uživatelské rozhraní je mechanismus k vyvolání prohlížeče. Tento mechanismus může být vyhrazeným ovládacím prvkem uživatelského rozhraní WebBrowser nebo způsobem, jak delegovat otevření prohlížeče.
MSAL poskytuje implementace webového uživatelského rozhraní pro většinu platforem, ale přesto existují případy, kdy by bylo vhodné hostovat prohlížeč sami:

- platformy, které nejsou výslovně popsané v MSAL, například Blazor, Unity, mono na desktopu
- Chcete testovat aplikaci v uživatelském rozhraní a chcete použít automatizovaný prohlížeč, který lze použít se systémem selen
- prohlížeč a aplikace běžící v MSAL se nacházejí v samostatných procesech.

##### <a name="at-a-glance"></a>První pohled

K tomu budete mít MSAL `start Url`, který se musí zobrazit v prohlížeči podle volby, aby koncový uživatel mohl zadat své uživatelské jméno atd. Po dokončení ověření bude vaše aplikace muset předat zpátky MSAL `end Url`, který obsahuje kód poskytovaný službou Azure AD.
Hostitelem `end Url` je vždy `redirectUri`. K zachycení `end Url` můžete:

- sledovat přesměrování prohlížeče, dokud se nedosáhne `redirect Url` nebo
- Přesměrujte prohlížeč na adresu URL, kterou sledujete.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi je bod rozšiřitelnosti.

`WithCustomWebUi` je bod rozšíření, který umožňuje poskytnout vlastní uživatelské rozhraní ve veřejných klientských aplikacích a umožnit uživateli projít koncový bod/Authorize zprostředkovatele identity a nechat ho přihlašovat a vyjádřit souhlas. MSAL.NET může, pak uplatnit ověřovací kód a získat token. Je to pro instanci, která se používá v sadě Visual Studio k tomu, aby Electrons aplikace (pro instanci a zpětnou vazbu) poskytovala webové interakce, ale ponechala MSAL.NET, aby pro většinu práce. Můžete ji také použít, pokud chcete poskytnout automatizaci uživatelského rozhraní. Ve veřejných klientských aplikacích MSAL.NET používá PKCE Standard ([RFC 7636-kontrolní klíč pro výměnu kódu pomocí veřejných klientů OAuth](https://tools.ietf.org/html/rfc7636)), aby se zajistilo dodržování zabezpečení: jenom MSAL.NET může uplatnit kód.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Jak používat WithCustomWebUi

Aby bylo možné použít `.WithCustomWebUI`, je třeba:

  1. Implementujte rozhraní `ICustomWebUi` (viz [zde](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). V podstatě budete muset implementovat jednu metodu `AcquireAuthorizationCodeAsync` přijetí adresy URL autorizačního kódu (vypočítaného MSAL.NET), aby uživatel procházel prostřednictvím interakce se zprostředkovatelem identity a pak vrátil zpět adresu URL, kterou by měl zprostředkovatel identity. volá se vaše implementace zpátky (včetně autorizačního kódu). Pokud máte problémy, vaše implementace by měla vyvolat výjimku `MsalExtensionException`, aby bylo možné s MSALně spolupracovat.
  2. Ve vašem volání `AcquireTokenInteractive` můžete použít modifikátor `.WithCustomUI()` předání instance vlastního webového uživatelského rozhraní.

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Příklady implementace ICustomWebUi v programu test Automation – SeleniumWebUI

Tým MSAL.NET přepsal vaše testy uživatelského rozhraní pro využití tohoto mechanismu rozšíření. V případě, že se zajímáte, můžete se podívat na třídu [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) ve zdrojovém kódu MSAL.NET

##### <a name="providing-a-great-experience-with-systemwebviewoptions"></a>Zajištění skvělých zkušeností s SystemWebViewOptions

Z MSAL.NET 4,1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) můžete zadat:

- identifikátor URI, na který se má přejít (`BrowserRedirectError`), nebo fragment HTML, který se má zobrazit (`HtmlMessageError`) v případě chyb přihlášení nebo souhlasu ve webovém prohlížeči systému
- identifikátor URI, na který se má přejít (`BrowserRedirectSuccess`), nebo fragment HTML, který se má zobrazit (`HtmlMessageSuccess`) v případě úspěšného přihlášení nebo souhlasu
- akce, která se má spustit pro spuštění prohlížeče systému. V takovém případě můžete zadat vlastní implementaci nastavením delegáta `OpenBrowserAsync`. Třída také poskytuje výchozí implementaci pro dva prohlížeče: `OpenWithEdgeBrowserAsync` a `OpenWithChromeEdgeBrowserAsync`, v uvedeném pořadí pro Microsoft Edge a [Microsoft Edge na Chromu](https://www.windowscentral.com/faq-edge-chromium).

Chcete-li použít tuto strukturu, můžete napsat něco jako následující:

```CSharp
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

Další informace o všech dalších volitelných parametrech pro `AcquireTokenInteractive` najdete v referenční dokumentaci pro [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) .

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java neposkytuje přímo metodu pro získání tokenu. Místo toho vyžaduje, aby aplikace odesílala požadavek na autorizaci ve své implementaci toku interakce s uživatelem, aby získala autorizační kód, který se pak může předat metodě `acquireToken` pro získání tokenu.

```java
AuthorizationCodeParameters parameters =  AuthorizationCodeParameters.builder(
                authorizationCode, redirectUri)
                .build();
CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();
    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL Python neposkytuje přímo metodu pro získání tokenu. Místo toho vyžaduje, aby aplikace odesílala požadavek na autorizaci ve své implementaci toku interakce s uživatelem, aby získala autorizační kód, který se pak může předat metodě `acquire_token_by_authorization_code` pro získání tokenu.

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

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
---

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows

Pokud se chcete přihlásit k doméně uživatele v doméně nebo počítači připojeném k Azure AD, musíte použít integrované ověřování systému Windows.

### <a name="constraints"></a>Omezení

- Integrované ověřování systému Windows (IWA) je možné použít pouze pro **federované** uživatele, tedy pro uživatele vytvořené v adresáři Active Directory a pro zálohování pomocí Azure Active Directory. Uživatelé, kteří vytvořili přímo v AAD, bez samoobslužných uživatelů, kteří nejsou **spravováni** službou AD – nemůžou tento tok ověřování používat. Toto omezení neovlivňuje tok uživatelského jména a hesla.
- IWA je pro aplikace napsané pro .NET Framework, .NET Core a platformy UWP.
- IWA neobejde MFA (Multi-Factor Authentication). Pokud je nakonfigurované vícefaktorové ověřování, může IWA selhat, pokud se vyžaduje výzva MFA, protože MFA vyžaduje zásah uživatele.
  > [!NOTE]
  > Tato jedna z nich je obtížné. IWA je neinteraktivní, ale MFA vyžaduje interaktivitu uživatele. Neřídíte, kdy zprostředkovatel identity požaduje, aby bylo provedeno MFA, správce tenanta. Vícefaktorové ověřování se vyžaduje v případě, že se přihlásíte z jiné země, pokud se nepřipojí přes síť VPN k podnikové síti, a někdy i při připojení přes VPN. Neočekává se deterministické sady pravidel, Azure Active Directory používá AI k nepřetržité učení, jestli je vyžadováno MFA. Pokud IWA dojde k chybě, měli byste přejít na výzvu uživatele (interaktivní ověřování nebo tok kódu zařízení).

- Autorita předaná `PublicClientApplicationBuilder` musí být:
  - tenant-Ed (z formuláře `https://login.microsoftonline.com/{tenant}/`, kde `tenant` je buď identifikátor GUID představující ID tenanta, nebo doména přidružená k tenantovi.
  - pro všechny pracovní a školní účty (`https://login.microsoftonline.com/organizations/`)
  - Osobní účty Microsoft nejsou podporované (nemůžete použít klienty/běžné nebo/consumers).

- Protože integrované ověřování systému Windows je tichý tok:
  - předtím, než uživatel vaší aplikace používal aplikaci, se musí dřív znovu přisouhlasit.
  - nebo správce tenanta musí předtím, než se dřív poslal všem uživatelům v tenantovi, aby aplikaci používal.
  - Jinými slovy:
    - buď jako vývojář stiskne na Azure Portal tlačítko **udělit** ,
    - nebo správce tenanta na kartě **oprávnění API** registrace pro aplikaci stiskne tlačítko **udělit nebo odvolat souhlas správce pro {tenant Domain}** (viz téma [Přidání oprávnění pro přístup k webovým rozhraním API](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)).
    - nebo jste poskytli způsob, jak uživatelům udělit souhlas s aplikací (viz [vyžádání souhlasu jednotlivého uživatele](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)).
    - nebo jste poskytli způsob, jak správci tenanta udělit souhlas s aplikací (viz téma [souhlas správce](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)).

- Tento tok je povolený pro aplikace .NET Desktop, .NET Core a Windows Universal (UWP).

Další informace o souhlasu najdete v tématu [oprávnění a souhlas platformy Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) .

### <a name="how-to-use-it"></a>Jak ji použít

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

V MSAL.NET je potřeba použít

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Obvykle potřebujete pouze jeden parametr (`scopes`). V závislosti na tom, jakým způsobem správce Windows zásady nainstaloval, může být možné, že aplikace ve vašem počítači s Windows nemůžou vyhledat přihlášeného uživatele. V takovém případě použijte druhou metodu `.WithUsername()` a předejte uživatelské jméno přihlášeného uživatele jako formát UPN – `joe@contoso.com`. V rozhraní .NET Core je k dispozici pouze přetížení přebírající uživatelské jméno, protože platforma .NET Core nemůže požádat o uživatelské jméno na operační systém.

Následující příklad prezentuje nejaktuálnější případ s vysvětlením druhu výjimek, které můžete získat a jejich zmírnění.

```CSharp
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

Seznam možných modifikátorů v AcquireTokenByIntegratedWindowsAuthentication naleznete v tématu [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

# <a name="javatabjava"></a>[Java](#tab/java)

Toto je extrakce z [MSALch ukázek Java pro vývoj](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/). Tady je třída, která se používá v MSAL ukázek Java ke konfiguraci ukázek: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
         .authority(TestData.AUTHORITY_ORGANIZATION)
         .telemetryConsumer(new Telemetry.MyTelemetryConsumer().telemetryConsumer)
         .build();

 IntegratedWindowsAuthenticationParameters parameters =
         IntegratedWindowsAuthenticationParameters.builder(
                 Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE), TestData.USER_NAME)
                 .build();

 Future<IAuthenticationResult> future = app.acquireToken(parameters);

 IAuthenticationResult result = future.get();

 return result;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Tento tok se ještě v MSAL Pythonu nepodporuje.

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Tento tok se nevztahuje na MacOS.

---

## <a name="username--password"></a>Uživatelské jméno a heslo

Token můžete získat také zadáním uživatelského jména a hesla. Tento tok je omezený a nedoporučuje se, ale stále existují případy použití, kde je to nezbytné.

### <a name="this-flow-isnt-recommended"></a>Tento tok se nedoporučuje.

Tento tok se **nedoporučuje** , protože aplikace, která žádá uživatele o heslo, není zabezpečená. Další informace o tomto problému najdete v [tomto článku](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Upřednostňovaný tok pro získání tokenu v tichém režimu na počítačích připojených k doméně systému Windows je [integrované ověřování systému Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). V opačném případě můžete také použít [tok kódu zařízení](https://aka.ms/msal-net-device-code-flow) .

> [!NOTE]
> I když je to v některých případech užitečné (scénáře DevOps), pokud chcete použít uživatelské jméno a heslo v interaktivních scénářích, kde máte vlastní uživatelské rozhraní, měli byste si představit, jak z něho odejít. Pomocí uživatelského jména a hesla si můžete vydávat několik věcí:
>
> - základní principy moderní identity: heslo se zachová a přehraje. Protože máme tento koncept sdíleného tajného kódu, který se dá zachytit.
> Nejedná se o nekompatibilní bez hesla.
> - Uživatelé, kteří potřebují provést MFA, se nebudou moct přihlásit (protože není k dispozici žádná interakce).
> - Uživatelé nebudou moct provádět jednotné přihlašování.

### <a name="constraints"></a>Omezení

Platí taky následující omezení:

- Tok uživatelského jména a hesla není kompatibilní s podmíněným přístupem a službou Multi-Factor Authentication: v případě, že vaše aplikace běží v tenantovi Azure AD, kde správce tenanta vyžaduje vícefaktorové ověřování, nemůžete tento tok použít. Mnoho organizací to udělat.
- Funguje jenom pro pracovní a školní účty (ne MSA).
- Tok je dostupný na platformě .NET Desktop a .NET Core, ale ne na UWP.

### <a name="b2c-specifics"></a>B2C specifické

[Další informace o použití ROPC s B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Jak ho používat?

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`obsahuje metodu `AcquireTokenByUsernamePassword`

Následující příklad představuje zjednodušený případ.

```CSharp
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

```CSharp
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

Podrobnosti o všech modifikátorech, které lze použít na `AcquireTokenByUsernamePassword`, naleznete v tématu [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

# <a name="javatabjava"></a>[Java](#tab/java)

Toto je extrakce z [MSALch ukázek Java pro vývoj](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/). Tady je třída, která se používá v MSAL ukázek Java ke konfiguraci ukázek: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_ORGANIZATION)
        .build();

UserNamePasswordParameters parameters = UserNamePasswordParameters.builder(
        Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
        TestData.USER_NAME,
        TestData.USER_PASSWORD.toCharArray())
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();

    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Toto je extrakce z [ukázek vývoje MSAL Pythonu](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Tento tok není podporován v MSAL pro macOS.

---

## <a name="command-line-tool-without-web-browser"></a>Nástroj příkazového řádku (bez webového prohlížeče)

### <a name="device-code-flow"></a>Tok kódu zařízení

Pokud píšete nástroj příkazového řádku (který neobsahuje webové ovládací prvky) a nemůžete nebo nechcete používat předchozí toky, budete muset použít tok kódu zařízení.

Interaktivní ověřování pomocí Azure AD vyžaduje webový prohlížeč (podrobnosti najdete v tématu [použití webových prohlížečů](https://aka.ms/msal-net-uses-web-browser)). Chcete-li však ověřovat uživatele v zařízeních nebo operačních systémech, které neposkytují webový prohlížeč, tok kódu zařízení umožňuje, aby uživatel používal k interaktivnímu přihlášení jiné zařízení (např. jiný počítač nebo mobilní telefon). Pomocí toku kódu zařízení aplikace získá tokeny prostřednictvím procesu se dvěma kroky, hlavně navržených pro tato zařízení/operačních systémech. Příklady takových aplikací jsou aplikace spuštěné v iOT nebo nástroje příkazového řádku (CLI). Nápad je následující:

1. Když se vyžaduje ověření uživatele, aplikace poskytne kód a vyzve uživatele k použití jiného zařízení (například smartphone připojeného k Internetu) k přechodu na adresu URL (například `https://microsoft.com/devicelogin`), kde se uživateli zobrazí výzva k zadání kódu. To se provede, když se webová stránka bude v případě potřeby dát uživateli do normálního prostředí ověřování, včetně výzev k vyjádření souhlasu a vícefaktorového ověřování.

2. Po úspěšném ověření obdrží aplikace příkazového řádku požadované tokeny prostřednictvím back-Channel a použije je k provedení volání webového rozhraní API.

### <a name="how-to-use"></a>Jak používat?

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`obsahuje metodu s názvem `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Tato metoda přijímá jako parametry:

- `scopes` pro vyžádání přístupového tokenu pro
- Zpětné volání, které získá `DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Následující vzorový kód představuje nejaktuálnější případ s vysvětlením druhu výjimek, které můžete získat, a jejich zmírnění.

```CSharp
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
# <a name="javatabjava"></a>[Java](#tab/java)

Toto je extrakce z [MSALch ukázek Java pro vývoj](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/). Tady je třída, která se používá v MSAL ukázek Java ke konfiguraci ukázek: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();

Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) -> {
    System.out.println(deviceCode.message());
};

CompletableFuture<IAuthenticationResult> future = app.acquireToken(
        DeviceCodeFlowParameters.builder(
                Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                deviceCodeConsumer)
                .build());

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception of type - " + ex.getClass());
        System.out.println("message - " + ex.getMessage());
        return "Unknown!";
    }
    System.out.println("Returned ok - " + res);

    return res;
});

future.join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Toto je extrakce z [ukázek vývoje MSAL Pythonu](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Tento tok se nevztahuje na MacOS.

---

## <a name="file-based-token-cache"></a>Mezipaměť tokenů založená na souborech

V MSAL.NET je ve výchozím nastavení poskytována mezipaměť tokenů v paměti.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Serializace je přizpůsobitelná v aplikacích klasické pracovní plochy Windows a Web Apps/Web API

V případě .NET Framework a .NET Core neuděláte nic dalšího, mezipaměť tokenů v paměti trvá po dobu trvání aplikace. Aby bylo možné pochopit, proč není serializace k dispozici, zapamatujte si, že aplikace MSAL .NET Desktop/Core můžou být aplikace konzoly nebo systému Windows (které by měly přístup k systému souborů), **ale také** webové aplikace nebo webové rozhraní API. Tyto webové aplikace a webová rozhraní API můžou používat určité konkrétní mechanismy ukládání do mezipaměti, jako jsou databáze, distribuované mezipaměti, mezipaměti Redis a tak dále. Chcete-li mít aplikaci pro mezipaměť trvalého tokenu v rozhraní .NET Desktop nebo Core, je nutné tuto serializaci přizpůsobit.

Třídy a rozhraní, které jsou součástí serializace mezipaměti tokenů, jsou následující typy:

- ``ITokenCache``, který definuje události pro přihlášení k odběru požadavků na serializaci mezipaměti tokenu a metody pro serializaci nebo deserializaci mezipaměti v různých formátech (ADAL v 3.0, MSAL 2. x a MSAL 3. x = ADAL v 5.0)
- ``TokenCacheCallback`` je zpětné volání předávané do událostí, aby bylo možné zpracovat serializaci. budou volány s argumenty typu ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` poskytuje pouze ``ClientId`` aplikace a odkaz na uživatele, pro který je token k dispozici.

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET vytvoří mezipaměť tokenů za vás a poskytne vám `IToken` cache při volání vlastností `UserTokenCache` a `AppTokenCache` aplikace. Nechcete implementovat rozhraní sami. Vaše zodpovědnost při implementaci serializace mezipaměti vlastního tokenu je následující:
>
> - Reagují na `BeforeAccess` a `AfterAccess` "události" (nebo se jedná o *asynchronní* protějšek). `BeforeAccess` delegát zodpovídá za deserializaci mezipaměti, zatímco `AfterAccess` jedna je zodpovědná za serializaci mezipaměti.
> - Součást těchto událostí ukládá nebo načítá objekty blob, které jsou předány argumentem události do libovolného úložiště, které chcete.

Strategie se liší v závislosti na tom, jestli píšete serializaci mezipaměti tokenů pro veřejnou klientskou aplikaci (Desktop), nebo důvěrnou klientskou aplikaci (webovou aplikaci/webové rozhraní API, démon App).

Vzhledem k tomu, že MSAL v2. x máte několik možností, v závislosti na tom, jestli chcete mezipaměť serializovat pouze do formátu MSAL.NET (Unified Format cache, která je společná s MSAL, ale i napříč platformami), nebo pokud chcete podporovat i [starší](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) mezipaměť tokenů. serializace knihovny ADAL v3.

Přizpůsobení serializace mezipaměti tokenů pro sdílení stavu jednotného přihlašování mezi ADAL.NET 3. x, ADAL.NET 5. x a MSAL.NET je vysvětleno v rámci následující ukázky: [Active-Directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Jednoduchá serializace mezipaměti tokenů (jenom MSAL)

Níže je uveden příklad implementace Naive vlastní serializace mezipaměti tokenů pro aplikace klasické pracovní plochy. Tady je mezipaměť tokenu uživatele v souboru ve stejné složce jako aplikace.

Po sestavení aplikace povolte serializaci voláním ``TokenCacheHelper.EnableSerialization()`` předání aplikace `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Tato pomocná třída vypadá jako následující fragment kódu:

```CSharp
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

Náhled serializátoru na základě souboru mezipaměti s tokeny kvality produktu pro veřejné klientské aplikace (pro desktopové aplikace běžící v systému Windows, Mac a Linux) je k dispozici v open source knihovně [Microsoft. identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . Můžete ji zahrnout do svých aplikací z následujícího balíčku NuGet: [Microsoft. identity. Client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Právní omezení. Knihovna Microsoft. identity. Client. Extensions. Msal je rozšířením nad MSAL.NET. Třídy v těchto knihovnách mohou v budoucnu vést do MSAL.NET, jak je, nebo s nezměněnými změnami.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serializace mezipaměti s duálním tokenem (MSAL Unified cache + ADAL V3)

Pokud chcete implementovat serializaci mezipaměti tokenů současně s formátem Unified cache (společný do ADAL.NET 4. x a MSAL.NET 2. x a s jinými MSALsy stejné generace nebo staršími verzemi, na stejné platformě), můžete nechte inspirovat získat pomocí následujícího kódu. :

```CSharp
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

```CSharp
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
> [Volání webového rozhraní API z desktopové aplikace](scenario-desktop-call-api.md)
