---
title: Konfigurace webové aplikace, která volá webová rozhraní API – platforma identit Microsoftu | Azure
description: Přečtěte si, jak nakonfigurovat kód webové aplikace, která volá webová rozhraní API
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
ms.openlocfilehash: b0f60e13ca4bc5115f9a49885c3c659ad1147fcc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881889"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Webová aplikace, která volá webová api: konfigurace kódu

Jak je znázorněno ve [webové aplikaci, která se přihlašuje ve scénáři uživatelů,](scenario-web-app-sign-user-overview.md) webová aplikace používá [tok autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md) k přihlášení uživatele. Tento tok má dva kroky:

1. Vyžádejte si autorizační kód. Tato část deleguje soukromý dialog s uživatelem na platformě identit microsoftu. Během tohoto dialogu se uživatel přihlásí a souhlasí s používáním webových rozhraní API. Po úspěšném ukončení soukromého dialogu webová aplikace obdrží autorizační kód na identifikátoru URI přesměrování.
1. Požádejte o přístupový token pro rozhraní API uplatněním autorizačního kódu.

[Webová aplikace, která se přizpůsobuje ve](scenario-web-app-sign-user-overview.md) scénářích uživatelů, se týkala pouze prvního kroku. Zde se dozvíte, jak upravit webovou aplikaci tak, aby nejen přiřazovala uživatele, ale také nyní volá webová api.

## <a name="libraries-that-support-web-app-scenarios"></a>Knihovny, které podporují scénáře webových aplikací

Následující knihovny v Knihovně ověřování Microsoft (MSAL) podporují tok autorizačního kódu pro webové aplikace:

| Knihovna MSAL | Popis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podpora pro rozhraní .NET Framework a .NET Core platformy. Není podporována univerzální platforma Windows (UPW), Xamarin.iOS a Xamarin.Android, protože tyto platformy se používají k vytváření veřejných klientských aplikací. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL pro Python | Podpora webových aplikací pythonu. |
| ![MSAL v Javě](media/sample-v2-code/logo_java.png) <br/> MSAL pro Javu | Podpora webových aplikací Java. |

Vyberte kartu platformy, která vás zajímá:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Vzhledem k tomu, že přihlášení uživatele je delegováno na middleware Open ID connect (OIDC), musíte pracovat s procesem OIDC. Způsob interakce závisí na rámci, který používáte.

Pro ASP.NET Core se přihlásíte k odběru událostí MIDDLEWare OIDC:

- Prostředkem Open ID Connect si ASP.NET Core vyžádá autorizační kód. ASP.NET nebo ASP.NET Core umožní uživateli přihlásit se a souhlasit.
- Budete se přihlásit k odběru webové aplikace pro příjem autorizačního kódu. Toto předplatné se provádí pomocí delegáta jazyka C#.
- Po přijetí autorizačního kódu jej použijete pomocí knihoven MSAL. Výsledné přístupové tokeny a obnovovací tokeny jsou uloženy v mezipaměti tokenů. Mezipaměť lze použít v jiných částech aplikace, jako jsou řadiče, k získání jiných tokenů tiše.

Příklady kódu v tomto článku a následující jsou extrahovány z [ASP.NET základní webové aplikace přírůstkové kurzu, kapitola 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Můžete chtít odkazovat na tento kurz pro úplné podrobnosti implementace.

> [!NOTE]
> Chcete-li plně pochopit příklady kódu zde, musíte být obeznámeni s [ASP.NET základní základní](https://docs.microsoft.com/aspnet/core/fundamentals), a zejména s vkládání [závislostí](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) a [možnosti](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Vzhledem k tomu, že přihlášení uživatele je delegováno na middleware Open ID connect (OIDC), musíte pracovat s procesem OIDC. Způsob interakce závisí na rámci, který používáte.

Pro ASP.NET se přihlásíte k odběru událostí MIDDLEWare OIDC:

- Prostředkem Open ID Connect si ASP.NET Core vyžádá autorizační kód. ASP.NET nebo ASP.NET Core umožní uživateli přihlásit se a souhlasit.
- Budete se přihlásit k odběru webové aplikace pro příjem autorizačního kódu. Toto předplatné se provádí pomocí delegáta jazyka C#.
- Po přijetí autorizačního kódu jej použijete pomocí knihoven MSAL. Výsledné přístupové tokeny a obnovovací tokeny jsou uloženy v mezipaměti tokenů. Mezipaměť lze použít v jiných částech aplikace, jako jsou řadiče, k získání jiných tokenů tiše.

Příklady kódu v tomto článku a následující jsou extrahovány z [ukázky webové aplikace ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Můžete chtít odkazovat na tuto ukázku pro podrobnosti o implementaci.

# <a name="java"></a>[Java](#tab/java)

Příklady kódu v tomto článku a následující jsou extrahovány z [webové aplikace Java, která volá Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), ukázku webové aplikace, která používá MSAL pro Jazyk Java.
Ukázka aktuálně umožňuje MSAL pro Java vytvořit adresu URL autorizačního kódu a zpracovává navigaci do koncového bodu autorizace pro platformu identit microsoftu. K přihlášení uživatele je také možné použít zabezpečení sprintu. Můžete chtít odkazovat na ukázku pro podrobnosti o implementaci.

# <a name="python"></a>[Python](#tab/python)

Příklady kódu v tomto článku a následující jsou extrahovány z [webové aplikace Python volání Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), ukázka webové aplikace, která používá MSAL. Python.
Ukázka v současné době umožňuje MSAL. Python vytvoří adresu URL autorizačního kódu a zpracuje navigaci ke koncovému bodu autorizace pro platformu identit Microsoftu. Můžete chtít odkazovat na ukázku pro podrobnosti o implementaci.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kód, který uplatní autorizační kód

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

V ASP.NET Core, `Startup.cs` v souboru `OnAuthorizationCodeReceived` se přihlásíte k odběru události OpenID Connect. Z této události volejte `AcquireTokenFromAuthorizationCode` metodu MSAL.NET. Tato metoda ukládá následující tokeny v mezipaměti tokenů:

- *Přístupový token* pro `scopes`požadovanou .
- Obnovovací *token*. Tento token se použije k aktualizaci přístupového tokenu, když je blízko vypršení platnosti, nebo získat jiný token jménem stejného uživatele, ale pro jiný prostředek.

[Kurz ASP.NET základní webové aplikace](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) poskytuje opakovaně použitelný kód pro vaše webové aplikace.

Následuje kód z [Startup.cs#L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Je vybaven volání:

- Metoda, `AddMicrosoftIdentityPlatformAuthentication` která přidá ověřování do webové aplikace.
- Metoda, `AddMsal` která přidává možnost volání webových api.
- Metoda, `AddInMemoryTokenCaches` která je o výběru implementace mezipaměti tokenu.

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

`Constants.ScopeUserRead`je definována v [Constants.cs#L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Již jste studovali obsah `AddMicrosoftIdentityPlatformAuthentication` [ve webové aplikaci, která se připisuje uživatelům - konfigurace kódu](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>Metoda AddMsal

Kód pro `AddMsal` je umístěn v [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

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

Tato `AddMsal` metoda zajišťuje, že:

- Webová aplikace ASP.NET Core požaduje token ID pro uživatele`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`i ověřovací kód ( ).
- Je `offline_access` přidán obor. Tento obor získá souhlas uživatele pro aplikaci získat obnovovací token.
- Aplikace se přihlásí k `OnAuthorizationCodeReceived` odběru události OIDC a uplatní volání pomocí MSAL.NET, který je zde `ITokenAcquisition`zapouzdřen do opakovaně použitelné součásti implementující .

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Metoda TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync

Metoda `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` je umístěna v [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Zajišťuje, že:

- ASP.NET se nepokouší uplatnit ověřovací kód souběžně s MSAL.NET (`context.HandleCodeRedemption();`).
- Deklarace identity v tokenu ID jsou k dispozici pro MSAL vypočítat klíč mezipaměti tokenu pro účet uživatele.
- V případě potřeby je vytvořena instance aplikace MSAL.NET.
- Kód je uplatněn MSAL.NET aplikací.
- Nový token ID je sdílen s ASP.NET `context.HandleCodeRedemption(null, result.IdToken);`Core během volání do . Přístupový token není sdílen s ASP.NET Core. Zůstane v mezipaměti MSAL.NET tokenu přidružené k uživateli, kde je připraven k použití v ASP.NET řadiče Core.

Zde je příslušný kód `TokenAcquisition`pro:

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

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>Metoda TokenAcquisition.BuildConfidentialClientApplication

V ASP.NET Core, vytváření důvěrné klientské aplikace `HttpContext`používá informace, které jsou v . Přidružené `HttpContext` s požadavkem je přístupný `CurrentHttpContext` pomocí vlastnosti. `HttpContext`má informace o adrese URL webové aplikace a o přihlášeném uživateli (v ). `ClaimsPrincipal` 

Metoda `BuildConfidentialClientApplication` také používá konfiguraci ASP.NET Core. Konfigurace má část "AzureAD" a je také vázána na oba následující prvky:

- Datová `_applicationOptions` struktura typu [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- Instance `azureAdOptions` typu [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs), definované `Authentication.AzureAD.UI`v ASP.NET Core .

Nakonec aplikace potřebuje udržovat mezipaměti tokenů. Více se o tom dozvíte v další části.

Kód `GetOrBuildConfidentialClientApplication()` metody je v [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Používá členy, které byly vloženy vkládání závislostí (předánv konstruktoru `TokenAcquisition` v [Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59).](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)

Zde je kód `GetOrBuildConfidentialClientApplication`pro:

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

`AcquireTokenByAuthorizationCode`je skutečně metoda, která uplatní autorizační kód, který ASP.NET požadavky a který získá tokeny, které jsou přidány do MSAL.NET mezipaměti uživatelských tokenů. Z mezipaměti tokeny se pak používají v řadičích ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET zpracovává podobně jako ASP.NET Core, s tím rozdílem, že konfigurace `OnAuthorizationCodeReceived` OpenID Connect a předplatné události se dějí v souboru [App_Start\Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) Koncepty jsou také podobné těm v ASP.NET Core, s `RedirectUri` tím rozdílem, že v ASP.NET musíte zadat v [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Tato konfigurace je o něco méně robustní než v ASP.NET Core, protože budete muset změnit při nasazení aplikace.

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

# <a name="java"></a>[Java](#tab/java)

Viz [Webová aplikace, která se připojuje k uživatelům: Konfigurace kódu,](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) abyste pochopili, jak ukázka Javy získá autorizační kód. Poté, co aplikace obdrží kód, [AuthFilter.java #L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegáti `AuthHelper.processAuthenticationCodeRedirect` metody v [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Volání `getAuthResultByAuthCode`.

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

Metoda `getAuthResultByAuthCode` je definována v [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Vytvoří MSAL `ConfidentialClientApplication`a pak `acquireToken()` volání `AuthorizationCodeParameters` s vytvořené z autorizačního kódu.

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

# <a name="python"></a>[Python](#tab/python)

Tok autorizačního kódu je požadován tak, jak je znázorněno ve [webové aplikaci, která se přiřazuje k uživatelům: Konfigurace kódu](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Kód je pak přijat `authorized` na funkci, která Flask směruje z `/getAToken` URL. Viz [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) pro úplný kontext tohoto kódu:

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

Namísto tajného klíče klienta může důvěrná klientská aplikace také prokázat svou identitu pomocí klientského certifikátu nebo kontrolního výrazu klienta.
Použití kontrolnívýrazy klienta je rozšířený scénář, podrobně popsané v [kontrolní výrazy klienta](msal-net-client-assertions.md).

## <a name="token-cache"></a>Mezipaměť tokenů

> [!IMPORTANT]
> Implementace mezipaměti tokenů pro webové aplikace nebo webová řešení API se liší od implementace pro desktopové aplikace, která je často [založena na souboru](scenario-desktop-acquire-token.md#file-based-token-cache).
> Z důvodů zabezpečení a výkonu je důležité zajistit, aby pro webové aplikace a webová rozhraní API existovala jedna mezipaměť tokenů na uživatelský účet. Je nutné serializovat mezipaměť tokenů pro každý účet.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Základní kurz ASP.NET používá vkládání závislostí, které vám umožní rozhodnout o implementaci mezipaměti tokenů v souboru Startup.cs pro vaši aplikaci. Soubor Microsoft.Identity.Web je dodáván s předem vytvořenými serializátory mezipaměti tokenů popsanými v [serializaci mezipaměti tokenů](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Zajímavou možností je vybrat si ASP.NET cache [distribuované paměti](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)Core :

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

Podrobnosti o poskytovatelích mezipaměti tokenů najdete také v [ASP.NET kurzy k webové aplikaci Core | Fáze mezipaměti tokenu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) v kurzu.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Implementace mezipaměti tokenů pro webové aplikace nebo webová řešení API se liší od implementace pro desktopové aplikace, která je často [založena na souboru](scenario-desktop-acquire-token.md#file-based-token-cache).

Implementace webové aplikace můžete použít ASP.NET relace nebo paměti serveru. Například podívejte se, jak je implementace mezipaměti připojena po vytvoření aplikace MSAL.NET v [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

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

# <a name="java"></a>[Java](#tab/java)

Jazyk MSAL Java poskytuje metody serializace a deserializace mezipaměti tokenů. Ukázka jazyka Java zpracovává serializaci z relace, `getAuthResultBySilentFlow` jak je znázorněno v metodě [v AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

Podrobnosti třídy `SessionManagementHelper` jsou uvedeny ve [vzorku MSAL pro Javu](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

V ukázce Pythonu je jedna mezipaměť na účet zajištěna opětovným vytvořením důvěrné klientské aplikace pro každý požadavek a jeho serializací v mezipaměti relace Flask:

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

V tomto okamžiku při přihlášení uživatele token uložených v mezipaměti tokenu. Podívejme se, jak se pak používá v jiných částech webové aplikace.

> [!div class="nextstepaction"]
> [Webová aplikace, která volá webová api: Odebrání účtů z mezipaměti při globálním odhlašování](scenario-web-app-call-api-sign-in.md)
