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
ms.collection: M365-identity-device-management
ms.openlocfilehash: e551159ad2d41af37b1f400e91680c49117498d6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423630"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Webová aplikace, která volá konfiguraci rozhraní Web API – Code

Jak je vidět ve [scénáři přihlášení k webové aplikaci](scenario-web-app-sign-user-overview.md), používá webová aplikace [tok autorizačního kódu](v2-oauth2-auth-code-flow.md) OAuth 2.0 k přihlášení uživatele. Tento tok je ve dvou částech:

1. Vyžádejte si autorizační kód. Tato část deleguje k uživateli privátní dialog Microsoft Identity Platform. Uživatel se přihlásí a souhlasí s používáním webových rozhraní API. Po úspěšném dokončení tohoto privátního dialogu obdrží aplikace na svém identifikátoru URI přesměrování autorizační kód.
1. Vyžádání přístupového tokenu pro rozhraní API uplatněním autorizačního kódu.

[Přihlášení k webové aplikaci ve scénáři uživatelů](scenario-web-app-sign-user-overview.md) provádělo pouze první nožku. Tady se dozvíte, jak upravit vaše webové rozhraní API, které přihlašuje uživatele, aby teď volala webová rozhraní API.

## <a name="libraries-supporting-web-app-scenarios"></a>Knihovny podporující scénáře webových aplikací

Knihovny podporující tok autorizačního kódu pro webové aplikace jsou:

| Knihovna MSAL | Popis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podporované platformy jsou .NET Framework a .NET Core Platforms (ne UWP, Xamarin. iOS a Xamarin. Android, protože tyto platformy slouží k vytváření veřejných klientských aplikací). |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Podpora webových aplikací v Pythonu |
| ![MSAL v Javě](media/sample-v2-code/logo_java.png) <br/> MSAL v Javě | Podpora webových aplikací v jazyce Java |

Vyberte kartu odpovídající platformě, které vás zajímá:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Vzhledem k tomu, že je umožněno přihlášení uživatele, je delegováno na middleware Open ID Connect (OIDC), chcete se zapojit do procesu OIDC. Způsob, jak to udělat, se liší v závislosti na rozhraní, které používáte.
V případě ASP.NET Core se přihlásíte k odběru událostí middlewaru OIDC. Princip je následující:

- ASP.NET Core požádá o autorizační kód prostřednictvím middleware Open ID Connect. Tímto ASP.NET/ASP.NET Core umožní uživateli přihlásit se a vyjádřit souhlas,
- Přihlásíte se k odběru žádosti o autorizační kód webovou aplikací. Toto předplatné se provádí prostřednictvím C# delegáta.
- Po přijetí kódu ověřování použijete knihovny MSAL k uplatnění kódu a výsledné přístupové tokeny a aktualizační tokeny jsou uloženy v mezipaměti tokenů. Odtud lze mezipaměť použít v jiných částech aplikace, například v řadičích, aby bylo možné získat další tokeny v tichém režimu.

Fragmenty kódu v tomto článku a následující jsou extrahovány z [přírůstkového kurzu ASP.NET Core webové aplikace, kapitola 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Informace o úplné implementaci najdete v tomto kurzu.

> [!NOTE]
> Chcete-li plně pochopit fragmenty kódu níže, je třeba znát [ASP.NET Core základy](https://docs.microsoft.com/aspnet/core/fundamentals)a zejména vkládání a [Možnosti](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) [závislostí](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) .

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Vzhledem k tomu, že je umožněno přihlášení uživatele, je delegováno na middleware Open ID Connect (OIDC), chcete se zapojit do procesu OIDC. Způsob, jak to udělat, se liší v závislosti na rozhraní, které používáte.
V případě ASP.NET se přihlásíte k odběru událostí middleware OIDC. Princip je následující:

- ASP.NET Core požádá o autorizační kód prostřednictvím middleware Open ID Connect. Tímto ASP.NET/ASP.NET Core umožní uživateli přihlásit se a vyjádřit souhlas,
- Přihlásíte se k odběru žádosti o autorizační kód webovou aplikací. Toto je C# delegát.
- Po přijetí kódu ověřování použijete knihovny MSAL k uplatnění kódu. Výsledné tokeny přístupu a tokeny aktualizace jsou pak uloženy v mezipaměti tokenů. Odtud lze mezipaměť použít v jiných částech aplikace, například v řadičích, aby bylo možné získat další tokeny v tichém režimu.

Fragmenty kódu v tomto článku a níže jsou extrahovány z [ukázky webové aplikace ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

# <a name="javatabjava"></a>[Java](#tab/java)

Fragmenty kódu v tomto článku a níže jsou extrahovány z [webové aplikace Java s voláním](https://github.com/Azure-Samples/ms-identity-java-webapp) ukázka webové aplikace Microsoft Graph MSAL Java.
Ukázka v současné době umožňuje, aby MSAL Java vytvořila adresu URL autorizačního kódu a zpracovává navigaci na koncový bod autorizace platformy Microsoft Identity Platform. K přihlášení uživatele je také možné použít zabezpečení sprintu. Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

# <a name="pythontabpython"></a>[Python](#tab/python)

Fragmenty kódu v tomto článku a níže se extrahují z [webové aplikace v Pythonu, která volá Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Ukázka webové aplikace v Pythonu
Ukázka v současné době umožňuje MSAL. Python vytváří adresu URL autorizačního kódu a zpracovává navigaci do koncového bodu autorizace platformy Microsoft Identity Platform. Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kód, který uplatňuje autorizační kód

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

V ASP.NET Core je princip v souboru `Startup.cs`. Budete chtít přihlašovat se k odběru události `OnAuthorizationCodeReceived` otevřít ID připojení a z této události volat MSAL. Metoda netto `AcquireTokenFromAuthorizationCode`, která má vliv na ukládání do mezipaměti tokenů, přístupový token pro požadovaný `scopes`a obnovovací token, který se použije k aktualizaci přístupového tokenu, když se blíží jeho vypršení platnosti, nebo pokud chcete získat token jménem stejného uživatele, ale pro jiný prostředek.

V praxi se v [kurzu ASP.NET Core Web App](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) pokusí o poskytnutí opakovaně použitelného kódu pro vaše webové aplikace.

Tady je kód [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) , který nabízí volání metody `AddMicrosoftIdentityPlatformAuthentication`, která do webové aplikace přidává ověřování a `AddMsal`, která přidává schopnost volat webová rozhraní API. Volání `AddInMemoryTokenCaches` se týká výběru implementace mezipaměti tokenů mezi těmi, které jsou možné:

```csharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead` je definována v [konstantách. cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Již jste prostudovali obsah `AddMicrosoftIdentityPlatformAuthentication` ve [webové aplikaci, který přihlašuje uživatele – konfigurace kódu](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)

### <a name="the-addmsal-method"></a>Metoda AddMsal

Kód pro `AddMsal` je umístěný v [Microsoft. identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
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

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
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

- Webová aplikace ASP.NET Core pro uživatele požaduje IDToken a ověřovací kód (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- je přidán obor `offline_access`. Je potřeba, aby uživatel souhlasil s tím, že aplikaci získá obnovovací token.
- aplikace se přihlásí k odběru události OIDC `OnAuthorizationCodeReceived` a uplatní volání pomocí MSAL.NET, které je zde zapouzdřeno do opakovaně použitelné komponenty implementující `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Metoda TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync

Metoda `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` se nachází v [Microsoft. identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Zajišťuje:

- ASP.NET se nepokusí o uplatnění ověřovacího kódu paralelně na MSAL.NET (`context.HandleCodeRedemption();`).
- Deklarace identity v IDToken jsou k dispozici pro MSAL k výpočtu klíče mezipaměti tokenu pro účet uživatele.
- v případě potřeby je vytvořena instance aplikace MSAL.NET
- kód je uplatněn aplikací MSAL.NET.
- Nový token ID se sdílí s ASP.NET Core (během volání `context.HandleCodeRedemption(null, result.IdToken);`). Přístupový token není sdílen s ASP.NET Core. Zůstane v mezipaměti tokenů MSAL.NET přidružené k uživateli, kde je připravený k použití v ASP.NET Corech řadičích.

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
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
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

V ASP.NET Core vytváření důvěrných klientských aplikací používá informace, které jsou v objektu HttpContext. Dostupné prostřednictvím vlastnosti `CurrentHttpContext`, HttpContext přidružená k žádosti, ví o adrese URL webové aplikace a přihlášeném uživateli (v `ClaimsPrincipal`). `BuildConfidentialClientApplication` používá taky konfiguraci ASP.NET Core, která má oddíl "AzureAD" a který je vázaný na:

- struktura `_applicationOptions` dat typu [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)
- instance `azureAdOptions` typu [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) definovaná v ASP.NET Core `Authentication.AzureAD.UI`. Nakonec aplikace potřebuje udržovat mezipaměti tokenů. V další části se dozvíte víc.

Kód pro metodu `GetOrBuildConfidentialClientApplication()` je v [Microsoft. identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Používá členy, které byly vloženy pomocí injektáže závislostí (předáno v konstruktoru třídy TokenAcquisition v [Microsoft. identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59))

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
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

Pro Shrnutí `AcquireTokenByAuthorizationCode` skutečným uplatněním autorizačního kódu, který požaduje ASP.NET, a získá tokeny přidané do mezipaměti tokenů uživatele MSAL.NET. Odtud jsou pak použiti v ASP.NET Core řadičích.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Způsob, jakým ASP.NET zpracovává věci, je podobný ASP.NET Core, s tím rozdílem, že konfigurace OpenIdConnect a předplatného události `OnAuthorizationCodeReceived` se nachází v souboru [app_start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Podobné koncepty najdete v ASP.NET Core s tím rozdílem, že v ASP.NET budete muset zadat RedirectUri v [souboru Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Tato konfigurace je trochu méně robustní než to, co se děje v ASP.NET Core, jak je budete muset při nasazení aplikace změnit.

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
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
      // Upon successful sign in, get the access token & cache it using MSAL
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

Informace o tom, jak ukázka Java získá autorizační kód, najdete v části [Webová aplikace, která přihlašuje uživatele – konfigurace kódu](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) . Po přijetí aplikací [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) deleguje do metody `AuthHelper.processAuthenticationCodeRedirect` v [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)a potom zavolá `getAuthResultByAuthCode`:

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

Metoda `getAuthResultByAuthCode` je definována v [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Vytvoří `ConfidentialClientApplication` MSAL a zavolá `acquireToken()` s `AuthorizationCodeParameters` vytvořenými z autorizačního kódu.

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

Po vyžádání toku autorizačního kódu, jak je vidět ve [webové aplikaci, která přihlašuje uživatele konfigurace kódu](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code), se autorizační kód obdrží ve funkci `authorized`e, která směřuje do baňky od adresy URL/getAToken. Viz [App. py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
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
Použití kontrolních výrazů klienta je pokročilý scénář, podrobně popisuje [kontrolní výrazy klienta](msal-net-client-assertions.md) .

## <a name="token-cache"></a>Mezipaměť tokenů

> [!IMPORTANT]
> V části Web Apps (nebo webová rozhraní API ve skutečnosti) se implementace mezipaměti tokenů liší od implementace mezipaměti tokenů desktopových aplikací (které jsou často [založené na souborech](scenario-desktop-acquire-token.md#file-based-token-cache)).
> Kvůli zabezpečení a výkonu je důležité, abyste měli jistotu, že pro webové aplikace a webová rozhraní API by měla existovat jedna mezipaměť tokenů na uživatele (za každý účet). Je nutné serializovat mezipaměť tokenů pro každý účet.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Kurz ASP.NET Core používá vkládání závislostí a umožňuje určit implementaci mezipaměti tokenů v souboru Startup.cs pro vaši aplikaci. Microsoft. identity. Web se dodává s několika předem sestavenými serializátory mezipaměti tokenů popsanými v tématu [serializace mezipaměti tokenů](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Zajímavou možností je zvolit ASP.NET Core [distribuované mezipaměti paměti](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Podrobnosti o poskytovatelích mezipaměti tokenů najdete v tématu [ASP.NET Core výukových kurzů pro webové aplikace | Fáze mezipamětí tokenů](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) v kurzu

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

V části Web Apps (nebo webová rozhraní API ve skutečnosti) se implementace mezipaměti tokenů liší od implementace mezipaměti tokenů desktopových aplikací (které jsou často [založené na souborech](scenario-desktop-acquire-token.md#file-based-token-cache)). Může použít relaci ASP.NET nebo paměť serveru. V části jsou uvedeny informace o tom, jak je implementace mezipaměti zapojena po vytvoření aplikace MSAL.NET v [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

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

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java poskytuje metody pro serializaci a deserializaci mezipaměti tokenů. Ukázka jazyka Java zpracovává serializaci z relace, jak je znázorněno v metodě `getAuthResultBySilentFlow` v [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

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

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Podrobnosti `SessionManagementHelper` třídy jsou k dispozici v[](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

V ukázce v Pythonu je při ukládání do mezipaměti podle účtu zajištěna opětovné vytvoření důvěrné klientské aplikace pro každý požadavek a její serializace v mezipaměti relace v baňce:

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

V tomto okamžiku, když se uživatel přihlásí k tokenu, je uložen v mezipaměti tokenů. Pojďme se podívat, jak se pak používá v jiných částech webové aplikace.

> [!div class="nextstepaction"]
> [Přihlaste se k webové aplikaci.](scenario-web-app-call-api-sign-in.md)
