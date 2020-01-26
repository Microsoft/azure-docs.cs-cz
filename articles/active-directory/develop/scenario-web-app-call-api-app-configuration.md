---
title: Konfigurace webové aplikace, která volá webová rozhraní API – Microsoft Identity Platform | Azure
description: Naučte se konfigurovat kód webové aplikace, která volá webová rozhraní API.
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
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759156"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Webová aplikace, která volá webová rozhraní API: Konfigurace kódu

Jak je znázorněno ve [webové aplikaci, která se podepisuje ve scénáři uživatelé](scenario-web-app-sign-user-overview.md) , používá webová aplikace [tok autorizačního kódu OAuth 2,0](v2-oauth2-auth-code-flow.md) k podepsání uživatele v. Tento tok má dva kroky:

1. Vyžádejte si autorizační kód. Tato část deleguje soukromý dialog s uživatelem na platformě Microsoft identity. Během tohoto dialogu se uživatel přihlásí a souhlasí s používáním webových rozhraní API. Po úspěšném dokončení privátního dialogu obdrží webová aplikace autorizační kód na svém identifikátoru URI přesměrování.
1. Vyžádání přístupového tokenu pro rozhraní API uplatněním autorizačního kódu.

[Webová aplikace, která se podepisuje do scénářů uživatelů,](scenario-web-app-sign-user-overview.md) je popsaná pouze v prvním kroku. Tady se dozvíte, jak upravit webovou aplikaci tak, aby nepodepisuje jenom uživatele v aplikaci, ale také teď volá webová rozhraní API.

## <a name="libraries-that-support-web-app-scenarios"></a>Knihovny, které podporují scénáře webové aplikace

Následující knihovny v knihovně Microsoft Authentication Library (MSAL) podporují tok autorizačního kódu pro webové aplikace:

| Knihovna MSAL | Popis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podpora .NET Framework a platforem .NET Core. Nepodporováno jsou Univerzální platforma Windows (UWP), Xamarin. iOS a Xamarin. Android, protože tyto platformy slouží k vytváření veřejných klientských aplikací. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL pro Python | Podpora webových aplikací v Pythonu |
| ![MSAL v Javě](media/sample-v2-code/logo_java.png) <br/> MSAL pro Javu | Podpora webových aplikací v jazyce Java. |

Vyberte kartu pro platformu, které vás zajímá:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Vzhledem k tomu, že je přihlášení uživatele delegované na middleware Open ID Connect (OIDC), musíte pracovat s procesem OIDC. Způsob práce závisí na rozhraní, které používáte.

V případě ASP.NET Core se přihlásíte k odběru událostí middlewaru OIDC:

- Umožní vám ASP.NET Core požádat o autorizační kód prostřednictvím middlewaru Open ID Connect. ASP.NET nebo ASP.NET Core umožní uživateli přihlašovat se a odsouhlasit.
- Přihlásíte se k odběru webové aplikace, abyste získali autorizační kód. Toto předplatné se provádí pomocí C# delegáta.
- Po přijetí autorizačního kódu použijete MSAL knihovny k uplatnění. Výsledné přístupové tokeny a aktualizační tokeny jsou uloženy v mezipaměti tokenů. Mezipaměť lze použít v jiných částech aplikace, jako jsou například řadiče, a získat tak další tokeny v tichém režimu.

Příklady kódu v tomto článku a následující jsou extrahovány z [přírůstkového kurzu ASP.NET Core webové aplikace, kapitola 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Můžete chtít v tomto kurzu vyhledat úplné podrobnosti o implementaci.

> [!NOTE]
> Pokud chcete plně pochopit příklady kódu, musíte být obeznámeni s [ASP.NET Core základy](https://docs.microsoft.com/aspnet/core/fundamentals)a zejména pomocí injektáže a [možností](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) [závislosti](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) .

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Vzhledem k tomu, že je přihlášení uživatele delegované na middleware Open ID Connect (OIDC), musíte pracovat s procesem OIDC. Způsob práce závisí na rozhraní, které používáte.

V případě ASP.NET se přihlásíte k odběru událostí middleware OIDC:

- Umožní vám ASP.NET Core požádat o autorizační kód prostřednictvím middlewaru Open ID Connect. ASP.NET nebo ASP.NET Core umožní uživateli přihlašovat se a odsouhlasit.
- Přihlásíte se k odběru webové aplikace, abyste získali autorizační kód. Toto předplatné se provádí pomocí C# delegáta.
- Po přijetí autorizačního kódu použijete MSAL knihovny k uplatnění. Výsledné přístupové tokeny a aktualizační tokeny jsou uloženy v mezipaměti tokenů. Mezipaměť lze použít v jiných částech aplikace, jako jsou například řadiče, a získat tak další tokeny v tichém režimu.

Příklady kódu v tomto článku a následující jsou extrahovány z [ukázky webové aplikace ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Můžete chtít, aby se v této ukázce naodkazovala na podrobnosti o plné implementaci.

# <a name="javatabjava"></a>[Java](#tab/java)

Příklady kódu v tomto článku a následující jsou extrahovány z [webové aplikace Java, která volá Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), ukázku webové aplikace, která používá MSAL pro jazyk Java.
Tato ukázka v současné době umožňuje MSAL pro jazyk Java generovat adresu URL autorizačního kódu a zpracovává navigaci na koncový bod autorizace pro platformu Microsoft identity. Je také možné použít zabezpečení sprintu k podepsání uživatele v. Můžete chtít, abyste si v ukázce naodkazovali na podrobnosti o plné implementaci.

# <a name="pythontabpython"></a>[Python](#tab/python)

Příklady kódu v tomto článku a následující jsou extrahovány z [webové aplikace v Pythonu, která volá Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), ukázku webové aplikace, která používá MSAL. Python.
Ukázka v současné době umožňuje MSAL. Python vytváří adresu URL autorizačního kódu a zpracovává navigaci na koncový bod autorizace pro platformu Microsoft Identity Platform. Můžete chtít, abyste si v ukázce naodkazovali na podrobnosti o plné implementaci.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kód, který uplatňuje autorizační kód

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

V ASP.NET Core v souboru `Startup.cs` jste se přihlásili k odběru události `OnAuthorizationCodeReceived` OpenID Connect. Z této události zavolejte metodu MSAL.NET `AcquireTokenFromAuthorizationCode`. Tato metoda ukládá do mezipaměti tokenů následující tokeny:

- *Přístupový token* pro požadovanou `scopes`
- *Obnovovací token* Tento token se použije k aktualizaci přístupového tokenu, když se blíží vypršení jeho platnosti, nebo získá další token jménem stejného uživatele, ale pro jiný prostředek.

[Kurz ASP.NET Core webové aplikace](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) poskytuje opakovaně použitelný kód pro vaše webové aplikace.

Následuje kód z [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Funkce volá:

- Metoda `AddMicrosoftIdentityPlatformAuthentication`, která do webové aplikace přidá ověřování.
- Metoda `AddMsal`, která přidává schopnost volat webová rozhraní API.
- `AddInMemoryTokenCaches` metoda, která se týká výběru implementace mezipaměti tokenu.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead` je definována v [konstantách. cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Již jste prostudovali obsah `AddMicrosoftIdentityPlatformAuthentication` ve [webové aplikaci, který podepisuje konfiguraci uživatele](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>Metoda AddMsal

Kód pro `AddMsal` je umístěný v [Microsoft. identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

Metoda `AddMsal` zajišťuje:

- Webová aplikace ASP.NET Core požaduje token ID pro uživatele a ověřovací kód (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- Je přidán obor `offline_access`. Tento obor získá souhlas uživatele, aby aplikace získala obnovovací token.
- Aplikace se přihlásí k odběru události OIDC `OnAuthorizationCodeReceived` a uplatní volání pomocí MSAL.NET, které se sem zapouzdřuje do opakovaně použitelné komponenty implementující `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Metoda TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync

Metoda `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` se nachází v [Microsoft. identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Zajišťuje:

- ASP.NET se nepokusí o uplatnění ověřovacího kódu paralelně na MSAL.NET (`context.HandleCodeRedemption();`).
- Deklarace identity v tokenu ID jsou k dispozici pro MSAL k výpočtu klíče mezipaměti tokenu pro účet uživatele.
- V případě potřeby se vytvoří instance aplikace MSAL.NET.
- Kód je uplatněn aplikací MSAL.NET.
- Nový token ID se sdílí s ASP.NET Core během volání `context.HandleCodeRedemption(null, result.IdToken);`. Přístupový token není sdílen s ASP.NET Core. Zůstane v mezipaměti tokenů MSAL.NET přidružené k uživateli, kde je připravený k použití v ASP.NET Corech řadičích.

Zde je uveden příslušný kód pro `TokenAcquisition`:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>Metoda TokenAcquisition. BuildConfidentialClientApplication

V ASP.NET Core vytváření důvěrných klientských aplikací používá informace, které jsou v `HttpContext`. `HttpContext` přidružený k žádosti se přistupuje pomocí vlastnosti `CurrentHttpContext`. `HttpContext` obsahuje informace o adrese URL webové aplikace a o přihlášeném uživateli (v `ClaimsPrincipal`). 

Metoda `BuildConfidentialClientApplication` používá také konfiguraci ASP.NET Core. V konfiguraci je oddíl "AzureAD" a také je vázán na oba následující prvky:

- `_applicationOptions` struktura dat typu [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- Instance `azureAdOptions` typu [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)definovaná v ASP.NET Core `Authentication.AzureAD.UI`.

Nakonec aplikace potřebuje udržovat mezipaměti tokenů. V další části se dozvíte víc.

Kód pro metodu `GetOrBuildConfidentialClientApplication()` je v [Microsoft. identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Používá členy, které byly vloženy pomocí injektáže závislostí (předáno v konstruktoru `TokenAcquisition` v [Microsoft. identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Zde je kód pro `GetOrBuildConfidentialClientApplication`:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Souhrn

`AcquireTokenByAuthorizationCode` je ve skutečnosti metoda, která uplatňuje autorizační kód, který ASP.NET požadavky, a který získá tokeny přidané do mezipaměti uživatelských tokenů MSAL.NET. Z mezipaměti se pak tokeny použijí v ASP.NET Corech řadičích.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET zpracovává něco podobně jako ASP.NET Core, s tím rozdílem, že konfigurace OpenID připojení a odběr k události `OnAuthorizationCodeReceived` dochází v souboru [app_start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Koncepty jsou také podobné těm v ASP.NET Core, s výjimkou toho, že v ASP.NET je nutné zadat `RedirectUri` v [souboru Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Tato konfigurace je trochu méně robustní než ta v ASP.NET Core, protože ji budete muset při nasazení aplikace změnit.

Zde je kód pro Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Viz [Webová aplikace, která přihlašuje uživatele: Konfigurace kódu](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) pro pochopení, jak ukázka Java získá autorizační kód. Poté, co aplikace obdrží kód, [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Deleguje delegáty metody `AuthHelper.processAuthenticationCodeRedirect` v [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Volá `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

Metoda `getAuthResultByAuthCode` je definována v [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Vytvoří `ConfidentialClientApplication`MSAL a potom zavolá `acquireToken()` s `AuthorizationCodeParameters` vytvořenými z autorizačního kódu.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Tok autorizačního kódu se požaduje, jak je znázorněno ve [webové aplikaci, která přihlašuje uživatele: Konfigurace kódu](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Kód se pak obdrží ve funkci `authorized`, která prochází trasy z adresy URL `/getAToken`. Úplný kontext tohoto kódu naleznete v tématu [App. py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) :

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Místo tajného klíče klienta může důvěrná klientská aplikace také prokázat svoji identitu pomocí klientského certifikátu nebo kontrolního výrazu klienta.
Použití kontrolních výrazů klienta je pokročilý scénář, podrobně popisuje [kontrolní výrazy klienta](msal-net-client-assertions.md).

## <a name="token-cache"></a>Mezipaměť tokenů

> [!IMPORTANT]
> Implementace mezipaměti tokenu pro webové aplikace nebo webová rozhraní API se liší od implementace aplikací klasické pracovní plochy, které jsou často [založené na souborech](scenario-desktop-acquire-token.md#file-based-token-cache).
> Z důvodu zabezpečení a výkonu je důležité zajistit, že pro webové aplikace a webová rozhraní API je pro každý uživatelský účet k dispozici jedna mezipaměť tokenů. Je nutné serializovat mezipaměť tokenů pro každý účet.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Kurz ASP.NET Core používá vkládání závislostí a umožňuje určit implementaci mezipaměti tokenů v souboru Startup.cs pro vaši aplikaci. Microsoft. identity. Web přichází s předem vytvořenými serializátory mezipaměti tokenů, které jsou popsané v tématu [serializace mezipaměti tokenů](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Zajímavou možností je zvolit ASP.NET Core [distribuované mezipaměti paměti](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Podrobnosti o poskytovatelích mezipaměti tokenů najdete v tématu [ASP.NET Core výukových kurzů pro webové aplikace | Fáze mezipamětí tokenů](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) v kurzu.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Implementace mezipaměti tokenu pro webové aplikace nebo webová rozhraní API se liší od implementace aplikací klasické pracovní plochy, které jsou často [založené na souborech](scenario-desktop-acquire-token.md#file-based-token-cache).

Implementace webové aplikace může používat relaci ASP.NET nebo paměť serveru. Například můžete zjistit, jak je implementace mezipaměti zapojena po vytvoření aplikace MSAL.NET v [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java poskytuje metody pro serializaci a deserializaci mezipaměti tokenů. Ukázka jazyka Java zpracovává serializaci z relace, jak je znázorněno v metodě `getAuthResultBySilentFlow` v [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Podrobnosti `SessionManagementHelper` třídy jsou k dispozici v [ukázce MSAL pro jazyk Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="pythontabpython"></a>[Python](#tab/python)

V ukázce v Pythonu je jedna mezipaměť na účet zajištěná tak, že se znovu vytvoří důvěrná klientská aplikace pro každý požadavek a pak se v mezipaměti relace v baňce zaregistruje jako serializace:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Další kroky

V tomto okamžiku, když se uživatel přihlásí, je token uložen v mezipaměti tokenů. Pojďme se podívat, jak se pak používá v jiných částech webové aplikace.

> [!div class="nextstepaction"]
> [Webová aplikace, která volá webová rozhraní API: Odebrání účtů z mezipaměti při globálním odhlašování](scenario-web-app-call-api-sign-in.md)
