---
title: Aplikace klasické pracovní plochy, která volá webové rozhraní API (získání tokenu aplikace) – platforma identit Microsoft
description: Zjistěte, jak sestavit aplikaci klasické pracovní plochy, která volá webové rozhraní API (získání tokenu pro aplikaci |)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d18c92cccac6bfb0bd359767ecdb51951268735
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962532"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Aplikace klasické pracovní plochy, která volá webové rozhraní API – získání tokenu

Jakmile jste sestavili jste `IPublicClientApplication`, použijete ho k získání tokenu, který pak použijete k volání webového rozhraní API.

## <a name="recommended-pattern"></a>Doporučený model

Webové rozhraní API je definován jeho `scopes`. Bez ohledu prostředí je zadat v aplikaci, je vzor, který budete chtít použít:

- Systematicky pokusu o získání tokenu z mezipaměti tokenů voláním `AcquireTokenSilent`
- Pokud toto volání se nezdaří, použijte `AcquireToken` tok, který chcete použít (zde reprezentovaný `AcquireTokenXX`)

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

Tady je nyní podrobností z různých způsobů, jak získat tokeny v desktopové aplikaci

## <a name="acquiring-a-token-interactively"></a>Získávání tokenu interaktivně

Následující příklad ukazuje minimální kód pro získání tokenu interaktivně pro čtení profilu uživatele s Microsoft Graphem.

```CSharp
string[] scopes = new string["user.read"];
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

`AcquireTokenInteractive` má pouze jeden povinný parametr ``scopes``, který obsahuje výčet řetězců, které definují obory, u kterých se vyžaduje token. Pokud je token pro Microsoft Graph, požadované obory najdete v referenci rozhraní api z každého rozhraní Microsoft graph API v části s názvem "Oprávnění". Například k [seznamu kontaktů uživatele](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), bude nutné použít obor "User.Read", "Contacts.Read". Viz také [referenční dokumentace k Microsoft Graphu oprávněním](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

V systému Android, budete muset zadat také nadřazená aktivita (pomocí `.WithParentActivityOrWindow`, viz níže) tak, že token, který získá zpět do nadřazené aktivity po interakce. Pokud ho nezadáte, bude vyvolána výjimka, při volání metody `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Konkrétní volitelné parametry

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Interaktivní, uživatelské rozhraní je důležité. `AcquireTokenInteractive` má jeden konkrétní volitelný parametr umožňující zadat pro platformy, podpora nadřazené uživatelského rozhraní. Při použití v případě aplikace klasické pracovní plochy `.WithParentActivityOrWindow` má jiný typ, v závislosti na platformě:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Poznámky:

- V .NET Standard, očekávané `object` je `Activity` na Androidu, `UIViewController` v Iosu `NSWindow` na počítači MAC a `IWin32Window` nebo `IntPr` na Windows.
- Na Windows, je nutné volat `AcquireTokenInteractive` v uživatelském rozhraní vlákna tak, aby embedded prohlížeče získá příslušný kontext synchronizace uživatelského rozhraní.  Není volání z vlákna uživatelského rozhraní může způsobit zpráv není správně pump a/nebo zablokování scénáře pomocí uživatelského rozhraní. Jeden ze způsobů, jak voláním MSAL z vlákna uživatelského rozhraní, pokud nejste na vlákně UI při již je použít `Dispatcher` na WPF.
- Pokud používáte WPF, chcete-li získat okna z ovládacího prvku WPF, můžete použít `WindowInteropHelper.Handle` třídy. Volání je pak z ovládacího prvku WPF (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` slouží k řízení interakce s uživatelem zadáním výzva

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Třída definuje následující konstanty:

- ``SelectAccount``: Vynutí služby STS pro zobrazení dialogu Výběr účtu, která obsahuje správcovské účty, pro které má uživatel relace. Tato možnost je užitečná, pokud chcete, aby mohl uživatel zvolit jiné identity vývojáři aplikací. Tato možnost řídí MSAL k odesílání ``prompt=select_account`` ke zprostředkovateli identity. Tato možnost je výchozí a provádí úlohy vhodné poskytnout optimální prostředí na základě dostupných informací (účtu, přítomnost relaci pro uživatele a tak dále. ...). Nemusíte ho měnit, pokud máte dobrý důvod, proč to.
- ``Consent``: umožňuje aplikaci pro vývojáře přinutit uživatele vyzváni k zadání souhlasu i v případě, že před udělení souhlasu. V takovém případě MSAL odešle `prompt=consent` ke zprostředkovateli identity. Tuto možnost lze použít v některých aplikacích zabezpečení, zaměřuje, kde se požadavky zásad správného řízení organizace, uživateli se zobrazí dialogové okno souhlasu pokaždé, když se aplikace používá.
- ``ForceLogin``: umožňuje vývojáři aplikace mít uživateli zobrazí výzva k zadání přihlašovacích údajů službou i v případě této výzvy uživatel nebude potřeba. Tato možnost může být užitečné, pokud získávání tokenu selže, aby mohl uživatel znovu-přihlášení se změnami. V takovém případě MSAL odešle `prompt=login` ke zprostředkovateli identity. Znovu zaznamenali jsme ji použít v některých aplikacích zabezpečení, zaměřuje kde zásad správného řízení organizace požadavků, že uživatel relogs – v pokaždé, když přistupují k konkrétní části aplikace.
- ``Never`` (pro .NET 4.5 a službu pouze WinRT) nezobrazí uživateli výzvu, ale místo toho se pokusí použít souboru cookie uloženého v zobrazení skrytá integrovaného webového (viz níže: Webové zobrazení v MSAL.NET). Pomocí této možnosti může selhat a v takovém případě `AcquireTokenInteractive` vyvolá výjimku upozornit, že je potřeba interakce s uživatelským rozhraním a budete muset použít jiné `Prompt` parametru.
- ``NoPrompt``: Nebudou odesílat žádné řádku ke zprostředkovateli identity. Tato možnost je užitečná pro Azure AD B2C upravit profil zásady (viz [B2C specifika](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Tento modifikátor se používá v pokročilém scénáři, kde má uživatel předem souhlas s předem několik prostředků (a nechcete použít přírůstkové souhlasu, která se běžně používá s MSAL.NET / Microsoft identity platform v2.0). Podrobnosti najdete v tématu [s návody: Požádejte uživatele, předem souhlas pro několik prostředků](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi je bod rozšiřitelnosti

`WithCustomWebUi` je k bodu rozšiřitelnosti, který umožňuje že zadat vlastní uživatelské rozhraní ve veřejné klientské aplikace a umožňují uživateli přejít přes koncový bod/authorize zprostředkovatele identity a umožňují jejich přihlášení a vyjádření souhlasu. MSAL.NET pak můžete uplatnit kód ověřování a získání tokenu. Například se používá v sadě Visual Studio mít elektronů poskytnout interakce webové aplikace (třeba zpětná vazba VS), ale ponechte MSAL.NET provádět většinu práce. Můžete také ho Pokud byste chtěli poskytnout automatizace uživatelského rozhraní. Aplikace veřejným klientem MSAL.NET používá standardní PKCE ([RFC 7636 – testování klíč pro výměnu kódu ve veřejných klientů OAuth](https://tools.ietf.org/html/rfc7636)) zajišťující dodržování zabezpečení: Pouze MSAL.NET může uplatnit kód.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Jak používat WithCustomWebUi

Chcete-li použít `.WithCustomWebUI`, budete muset:
  
  1. Implementace `ICustomWebUi` rozhraní (viz [tady](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). V podstatě budete muset implementovat jednu metodu `AcquireAuthorizationCodeAsync` přijímá adresa URL autorizační kód (počítají tak, že MSAL.NET), takže uživatel projít interakci s zprostředkovatele identity a vrácení zpět adresu URL, pomocí kterého by zprostředkovatele identity mít volá vaši implementaci zpět (včetně autorizační kód). Pokud máte problémy s vaší implementace, by měly vyvolat `MsalExtensionException` výjimka krásně spolupracovat s MSAL.
  2. Ve vaší `AcquireTokenInteractive` volání, můžete použít `.WithCustomUI()` modifikátor předejte instanci vlastního webového uživatelského rozhraní

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Příklady implementace ICustomWebUi ve službě automation test - SeleniumWebUI

Tým MSAL.NET přepsali jsme naše testy uživatelského rozhraní využívat tento mechanismus rozšíření. V případě, že vás zajímá, budete moci podívat, [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) třídy ve zdrojovém kódu MSAL.NET

#### <a name="other-optional-parameters"></a>Další volitelné parametry

Další informace o všechny ostatní volitelné parametry pro `AcquireTokenInteractive` z referenční dokumentaci pro [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Integrované ověřování Windows

Pokud chcete k přihlášení uživatele domény, počítač připojený k doméně nebo Azure AD, budete muset použít:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Omezení

- AcquireTokenByIntegratedWindowsAuth (IWA) lze použít pro pouze **federativní** pouze, uživatelů, tzn., uživatelé vytvořili ve službě Active Directory a podporovaný službou Azure Active Directory. Uživatelé přímo v ní vytvářet bez zálohování AD - **spravované** nemůžou uživatelé - pomocí tohoto toku ověřování. Toto omezení nemá vliv na tok uživatelského jména a hesla.
- IWA je pro aplikace napsané pro rozhraní .NET Framework, .NET Core a UPW platformy
- IWA není obejít MFA (vícefaktorové ověřování). Pokud je nakonfigurované vícefaktorové ověřování, IWA může selhat, pokud ověřovacím testem MFA se vyžaduje, protože MFA vyžaduje zásah uživatele.
  > [!NOTE]
  > Tento příklad je velmi obtížné. IWA je jako neinteraktivní, ale vyžaduje 2FA s uživatelem. Můžete neovládají když poskytovatel identity požádá o 2FA, která se má provést, provede správce tenanta. Z našich pozorování se vyžaduje 2FA, při přihlášení z jiné země, když nejsou připojené prostřednictvím sítě VPN k podnikové síti a to někdy i v případě připojení prostřednictvím VPN. Neočekáváme, že deterministické sadu pravidel, Azure Active Directory používá průběžně další, pokud se vyžaduje 2FA AI. Byste měli náhradu za výzvy uživatele (interaktivní ověřování nebo zařízení toku kódu), pokud selže IWA.

- Předaný oprávnění `PublicClientApplicationBuilder` musí být:
  - ed tenanta (ve formátu `https://login.microsoftonline.com/{tenant}/` kde `tenant` je buď identifikátor guid představující ID tenanta nebo domény přidružené k tenantovi.
  - pro všechny pracovních a školních účtů (`https://login.microsoftonline.com/organizations/`)

  > Osobní účty Microsoft se nepodporují (nelze použít/Common nebo /consumers tenantů)

- Protože integrované ověřování Windows je pasivní tok:
  - uživatele vaší aplikace musí mít dříve odsouhlasený. k používání aplikace
  - nebo správce tenanta musí mít dříve souhlas pro všechny uživatele v tenantovi k používání aplikace.
  - Jinými slovy:
    - buď jako vývojář stisknutí **udělení** tlačítko na webu Azure portal pro sebe,
    - nebo správce tenanta stiskla **udělení nebo odvolání souhlasu správce pro {doména tenanta}** tlačítko **oprávnění k rozhraní API** kartu registrace aplikace (naleznete v tématu [přidat oprávnění pro přístup k webovým rozhraním API](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - nebo které jste zadali způsob, jak uživatelům udělit souhlas s aplikace (viz [vyžádání souhlasu jednotlivé uživatele](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - nebo jste zadali způsob, jak správce tenanta o souhlasu pro aplikaci (viz [souhlas správce](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Tento tok je povolený pro .net desktop, .net core a Windows Universal (UPW) aplikace. V rozhraní .NET core je k dispozici, pouze přetížení přijímající uživatelské jméno, podle platformy .NET Core nemůžete se ptát uživatelské jméno pro operační systém.
  
Další informace o souhlas, naleznete v tématu [v2.0 oprávnění a souhlas](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Jak ji použít

Obvykle potřebujete jenom jeden parametr (`scopes`). Ale v závislosti na způsobu, jakým správce Windows má nastavení zásad, může být možné, že aplikace na počítači s windows nemůžou k vyhledání přihlášeného uživatele. V takovém případě použít druhou metodu `.WithUsername()` a předejte mu uživatelské jméno přihlášeného uživatele jako hlavní název uživatele formát - `joe@contoso.com`.

Následující příklad představuje aktuální případ s vysvětlením typ výjimky, které můžete získat a jejich možných zmírnění

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

Seznam možných modifikátorů AcquireTokenByIntegratedWindowsAuthentication najdete v tématu [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Uživatelské jméno / heslo

Můžete také získat token zadáním uživatelského jména a hesla. Tento tok je omezené, ale nedoporučený krok, ale existují i nadále používat případech, kdy je nezbytné.

### <a name="this-flow-isnt-recommended"></a>Tento tok se nedoporučuje.

Tento tok je **ale nedoporučený krok** vzhledem k tomu, že vaše aplikace s žádostí uživatele k zadání hesla není zabezpečený. Další informace o tomto problému najdete v tématu [v tomto článku](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Upřednostňované tok pro získání tokenu tiše na počítačích připojených k doméně Windows je [integrované ověřování Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Jinak můžete použít také [toku kódu zařízení](https://aka.ms/msal-net-device-code-flow)

> I když to je užitečné v některých případech (scénáře DevOps), pokud chcete použít uživatelského jména a hesla v interaktivních scénářů, kde poskytuje vaše onw uživatelského rozhraní, byste ve skutečnosti uvažovat o tom, jak ho přestat používat. Pomocí uživatelského jména a hesla můžete jsou poskytuje up několik věcí:

> - základní tenantů moderního identity: získá podléhá heslo, znovu přehrát. Když máme tento koncept tajného kódu sdílené složky, do které může.
> To není kompatibilní s passwordless.
> - Uživatelé, kteří vyžadují vícefaktorové ověřování, nebude možné přihlašovat (protože není žádná interakce s)
> - Uživatelé nebudou moct jednotného přihlašování

### <a name="constraints"></a>Omezení

Také platí následující omezení:

- Tok uživatelského jména a hesla není kompatibilní s podmíněného přístupu a ověřování službou Multi-Factor Authentication: Pokud vaše aplikace běží v tenantovi Azure AD, kde správce tenanta požaduje ověření službou Multi-Factor Authentication, v důsledku toho nelze pomocí tohoto toku. Mnoho organizací to udělat.
- Funguje pouze pro pracovní a školní účty (ne MSA).
- Tok je k dispozici na .net desktop a .net core, ale nikoli na UPW

### <a name="b2c-specifics"></a>Specifika B2C

[Další informace o používání se službou B2C ROPC](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Jak ji používat?

`IPublicClientApplication`obsahuje metodu `AcquireTokenByUsernamePassword`

Následující příklad představuje zjednodušenou případ

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

Následující příklad představuje aktuální případ s vysvětlením typ výjimky, které můžete získat a jejich možných zmírnění

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

Podrobnosti o všech parametrů, které mohou být použity `AcquireTokenByUsernamePassword`, naleznete v tématu [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Nástroj příkazového řádku (bez webový prohlížeč)

### <a name="device-code-flow-why-and-how"></a>Kód zařízení tok důvod, proč? a jak?

Pokud píšete nástroj příkazového řádku (nemá webové ovládací prvky) a nemůžete nebo nechcete použít předchozí toků, budete muset použít `AcquireTokenWithDeviceCode`.

Interaktivní ověřování pomocí Azure AD vyžaduje webový prohlížeč (podrobnosti najdete v tématu [využití webových prohlížečů](https://aka.ms/msal-net-uses-web-browser)). Však k ověřování uživatelů na zařízení nebo operační systémy, které neposkytují webový prohlížeč, tok kódu při zařízení vám umožní používat jiné zařízení (například jiného počítače nebo mobilní telefon) pro přihlášení interaktivně. Pomocí toku kódu zařízení, aplikace získá tokeny krocích zvláště určené pro zařízení nebo operačního systému. Příkladem takové aplikace jsou aplikace běžící na iOT nebo nástroje příkazového řádku (CLI). Cílem je, že:

1. Pokaždé, když se vyžaduje ověření uživatele, poskytuje kód a žádá uživatele, aby pomocí jiného zařízení (jako jsou připojené k Internetu smartphone) přejděte na adresu URL aplikace (například `https://microsoft.com/devicelogin`), ve kterém uživateli zobrazí výzva k zadání kódu. Že budete hotovi, webové stránky přejde uživatele prostřednictvím normální ověřování prostředí, a v případě potřeby včetně výzev k udělení souhlasu a ověřování službou Multi-Factor Authentication.

2. Po úspěšném ověření aplikace příkazového řádku bude přijímat požadované tokeny prostřednictvím používající back channel a použije ho k provádění volání webové rozhraní API, které potřebuje.

### <a name="code"></a>Kód

`IPublicClientApplication`obsahuje metodu s názvem `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Tato metoda přijímá jako parametry:

- `scopes` Požádat o token přístupu
- Zpětné volání, která bude dostávat `DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Následující ukázkový kód představuje aktuální případ s vysvětlením typ výjimky, které můžete získat a jejich zmírnění.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
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
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>Souborové mezipaměti tokenů

V MSAL.NET ve výchozím nastavení poskytuje mezipaměť tokenu v paměti.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Serializace je přizpůsobitelný v aplikacích klasické pracovní plochy Windows a webové aplikace/webové rozhraní API

V případě rozhraní .NET Framework a .NET core Pokud se vám nic nedělají navíc, token mezipaměť v paměti trvá po dobu trvání aplikace. Vysvětlení, proč není ihned k dispozici serializace pamatovat MSAL .NET desktop/jádro aplikace mohou být konzoly a aplikace Windows (které bude mít přístup k systému souborů) **, ale také** webové aplikace nebo webové rozhraní API. Tyto webové aplikace a webová rozhraní API může použít některé mechanismy mezipaměti, jako jsou databáze, distribuované mezipaměti, a tak dále mezipaměti redis. Pokud chcete, aby trvalou mezipaměť tokenu aplikace v .NET Desktop nebo jádra, budete muset přizpůsobit serializace.

Třídy a rozhraní, které jsou součástí mezipaměť tokenu serializace jsou následující typy:

- ``ITokenCache``, který definuje události k odběru mezipaměť tokenu serializaci požadavků, jakož i metody pro serializovat nebo deserializovat do mezipaměti v různých formátech (ADAL v3.0, MSAL 2.x a MSAL 3.x = ADAL v5.0)
- ``TokenCacheCallback`` zpětné volání předána události tak, aby bylo možné zpracovat serializace. budete se volá s argumenty typu ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` poskytuje pouze ``ClientId`` aplikace a odkaz na uživatele, pro který je k dispozici token

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET pro vás vytvoří token mezipaměti a nabízí `IToken` ukládat do mezipaměti při volání aplikace `GetUserTokenCache` a `GetAppTokenCache` metody. Nejsou by měl implementovat rozhraní. Vaše odpovědnosti, Pokud implementujete vlastní mezipaměť tokenu serializace, je:
>
> - Reakce na ně `BeforeAccess` a `AfterAccess` "události". `BeforeAccess` Delegáta odpovídá k deserializaci do mezipaměti, že `AfterAccess` jeden je zodpovědná za serializaci do mezipaměti.
> - Součástí těchto událostí uložit nebo načíst objekty BLOB, které se předává argument události do jakékoli úložiště, které chcete.

Strategie se liší v závislosti na tom, pokud píšete mezipaměť tokenu serializace pro aplikace veřejným klientem (Desktop) nebo aplikace důvěrnému klientovi (webové aplikace/webové rozhraní API, proces démon aplikace).

Od verze MSAL V2.x máte několik možností, podle toho, pokud chcete serializovat mezipaměti pouze na formát MSAL.NET (sjednoceném formátu, mezipaměť, která je společná s MSAL, ale i napříč platformami), nebo pokud chcete také podporovat [starší verze](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Serializace mezipaměť tokenů ADAL V3.

Přizpůsobení tokenu mezipaměti serializaci sdílení stavu jednotné přihlašování mezi ADAL.NET 3.x ADAL.NET 5.x a MSAL.NET je vysvětleno v části o následující ukázka: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Serializace jednoduchou mezipaměť tokenu (pouze MSAL)

Níže je příklad naivní implementace vlastní serializace mezipaměť tokenu pro desktopové aplikace. Tady mezipaměť tokenu uživatele do souboru ve stejné složce jako aplikace.

Po sestavení aplikace, povolíte serializace voláním ``TokenCacheHelper.EnableSerialization()`` předávání aplikace `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Tato pomocná třída bude vypadat jako následující fragment kódu:

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

Mezipaměť tokenu kvalitu produktu na základě souboru serializátor pro aplikace veřejným klientem (pro desktopové aplikace běžící na Windows, Mac a linux) je k dispozici ve verzi preview [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) knihovny Open source. Můžete ho zahrnout do své aplikace z následující balíček nuget: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> Právní omezení. Knihovna Microsoft.Identity.Client.Extensions.Msal je rozšířením přes MSAL.NET. Třídy v těchto knihoven může dostanou do MSAL.NET v budoucnu, jak jsou, nebo s rozbíjející změny.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Duální mezipaměť tokenu serializace (MSAL unified mezipaměti + modul ADAL V3)

Pokud chcete implementovat mezipaměť tokenu serializace i pomocí sjednocené mezipaměti formátu (běžné ADAL.NET 4.x a MSAL.NET 2.x a s další MSALs ke stejné generaci nebo starší, v rámci téže platformy), je možné nechte se inspirovat následující kód :

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

Tentokrát pomocná třída bude vypadat jako v následujícím kódu:

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
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volání webového rozhraní API z desktopové aplikace](scenario-desktop-call-api.md)
