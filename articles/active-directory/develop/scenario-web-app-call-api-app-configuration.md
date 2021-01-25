---
title: Konfigurace webové aplikace, která volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se konfigurovat kód webové aplikace, která volá webová rozhraní API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: bcbff131706854c079a5af2f38cad98f97018167
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753321"
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
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podpora .NET Framework a platforem .NET Core. Nepodporováno jsou Univerzální platforma Windows (UWP), Xamarin. iOS a Xamarin. Android, protože tyto platformy slouží k vytváření veřejných klientských aplikací. <br/><br/>Pro ASP.NET Core Web Apps a webová rozhraní API je MSAL.NET zapouzdřený v knihovně vyšší úrovně s názvem [Microsoft. identity. Web](https://aka.ms/ms-identity-web). |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL pro Python | Podpora webových aplikací v Pythonu |
| ![MSAL v Javě](media/sample-v2-code/logo_java.png) <br/> MSAL pro Javu | Podpora webových aplikací v jazyce Java. |

Vyberte kartu pro platformu, které vás zajímá:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Klientské tajné klíče nebo klientské certifikáty

Vzhledem k tomu, že vaše webová aplikace nyní volá webové rozhraní API pro příjem dat, je nutné zadat tajný klíč klienta nebo klientský certifikát v *appsettings.js* souboru. Můžete také přidat oddíl, který určuje:

- Adresa URL webového rozhraní API pro příjem dat
- Rozsahy vyžadované pro volání rozhraní API

V následujícím příkladu obsahuje `GraphBeta` oddíl tato nastavení.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Místo tajného kódu klienta můžete zadat klientský certifikát. Následující fragment kódu ukazuje použití certifikátu uloženého v Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

*Microsoft. identity. Web* nabízí několik způsobů, jak popsat certifikáty, jak pomocí konfigurace, tak pomocí kódu. Podrobnosti najdete v tématu [Microsoft. identity. Web – používání certifikátů](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) na GitHubu.

## <a name="startupcs"></a>Startup.cs

Vaše webová aplikace bude potřebovat získat token pro rozhraní API pro příjem dat. Zadejte ho přidáním `.EnableTokenAcquisitionToCallDownstreamApi()` řádku po `.AddMicrosoftIdentityWebApi(Configuration)` . Tento řádek zpřístupňuje `ITokenAcquisition` službu, kterou můžete použít v rámci kontroleru a akcí stránky. Jak se ale zobrazí v následujících dvou možnostech, dá se to udělat víckrát. Budete také muset zvolit implementaci mezipaměti tokenů, například `.AddInMemoryTokenCaches()` v *Startup.cs*:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

Předané obory `EnableTokenAcquisitionToCallDownstreamApi` jsou volitelné a umožňují webové aplikaci požádat o obory a jejich souhlas uživatele v případě, že se přihlásí. Pokud nezadáte rozsahy, *Microsoft. identity. Web* umožní postupné souhlasu.

Pokud nechcete získat token sami, *Microsoft. identity. Web* poskytuje dva mechanismy pro volání webového rozhraní API z webové aplikace. Možnost, kterou zvolíte, závisí na tom, jestli chcete volat Microsoft Graph nebo jiné rozhraní API.

### <a name="option-1-call-microsoft-graph"></a>Možnost 1: volání Microsoft Graph

Pokud chcete volat Microsoft Graph, *Microsoft. identity. Web* vám umožní přímo použít `GraphServiceClient` (zveřejněné v sadě Microsoft Graph SDK) ve svých akcích rozhraní API. Postup vystavení Microsoft Graph:

1. Do projektu přidejte balíček NuGet [Microsoft. identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) .
1. Přidejte `.AddMicrosoftGraph()` po `.EnableTokenAcquisitionToCallDownstreamApi()` v souboru *Startup.cs* . `.AddMicrosoftGraph()` má několik přepsání. Pomocí přepsání, které přebírá konfigurační oddíl jako parametr, se kód změní na:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Možnost 2: volání podřízeného webového rozhraní API kromě Microsoft Graph

Chcete-li volat webové rozhraní API jiné než Microsoft Graph, *Microsoft. identity. Web* poskytuje `.AddDownstreamWebApi()` , které vyžádá tokeny a zavolá webové rozhraní API pro příjem dat.

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>Souhrn

Stejně jako u webových rozhraní API můžete zvolit různé implementace mezipaměti tokenů. Podrobnosti najdete v tématu věnovaném [serializaci mezipaměti Microsoft. identity. Web-token](https://aka.ms/ms-id-web/token-cache-serialization) na GitHubu.

Následující obrázek znázorňuje různé možnosti *Microsoft. identity. Web* a jejich dopad na soubor *Startup.cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Blokový diagram znázorňující možnosti konfigurace služby ve spouštěcí tečkě C S pro volání webového rozhraní API a určení implementace mezipaměti tokenů":::

> [!NOTE]
> Pokud chcete plně pochopit příklady kódu, musíte být obeznámeni s [ASP.NET Core základy](/aspnet/core/fundamentals)a zejména pomocí injektáže a [možností](/aspnet/core/fundamentals/configuration/options) [závislosti](/aspnet/core/fundamentals/dependency-injection) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Vzhledem k tomu, že je přihlášení uživatele delegované pro middleware OpenID Connect (OIDC), musíte pracovat s procesem OIDC. Způsob práce závisí na rozhraní, které používáte.

V případě ASP.NET se přihlásíte k odběru událostí middleware OIDC:

- Umožní vám ASP.NET Core požádat o autorizační kód prostřednictvím middlewaru Open ID Connect. ASP.NET nebo ASP.NET Core umožní uživateli přihlašovat se a odsouhlasit.
- Přihlásíte se k odběru webové aplikace, abyste získali autorizační kód. Toto předplatné se provádí pomocí delegáta jazyka C#.
- Po přijetí autorizačního kódu použijete MSAL knihovny k uplatnění. Výsledné přístupové tokeny a aktualizační tokeny jsou uloženy v mezipaměti tokenů. Mezipaměť lze použít v jiných částech aplikace, jako jsou například řadiče, a získat tak další tokeny v tichém režimu.

Příklady kódu v tomto článku a následující jsou extrahovány z [ukázky webové aplikace ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Můžete chtít, aby se v této ukázce naodkazovala na podrobnosti o plné implementaci.

# <a name="java"></a>[Java](#tab/java)

Příklady kódu v tomto článku a následující jsou extrahovány z [webové aplikace Java, která volá Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), ukázku webové aplikace, která používá MSAL pro jazyk Java.
Tato ukázka v současné době umožňuje MSAL pro jazyk Java generovat adresu URL autorizačního kódu a zpracovává navigaci na koncový bod autorizace pro platformu Microsoft identity. Je také možné použít zabezpečení sprintu k podepsání uživatele v. Můžete chtít, abyste si v ukázce naodkazovali na podrobnosti o plné implementaci.

# <a name="python"></a>[Python](#tab/python)

Příklady kódu v tomto článku a následující jsou extrahovány z [webové aplikace v Pythonu, která volá Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), ukázku webové aplikace, která používá MSAL. Python.
Ukázka v současné době umožňuje MSAL. Python vytváří adresu URL autorizačního kódu a zpracovává navigaci na koncový bod autorizace pro platformu Microsoft Identity Platform. Můžete chtít, abyste si v ukázce naodkazovali na podrobnosti o plné implementaci.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kód, který uplatňuje autorizační kód

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. identity. Web zjednodušuje váš kód nastavením správných nastavení připojení OpenID, přihlášení k odběru události přijatého kódu a uplatnění kódu. Pro uplatnění autorizačního kódu není nutný žádný další kód. Podrobnosti o tom, jak to funguje, najdete v tématu [Microsoft. identity. Web Source Code](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET zpracovává podobně ASP.NET Core, s tím rozdílem, že konfigurace OpenID připojení a odběr k `OnAuthorizationCodeReceived` události dochází v souboru [app_start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Koncepty jsou také podobné těm v ASP.NET Core, s výjimkou toho, že v ASP.NET je nutné zadat `RedirectUri` v [Web.config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Tato konfigurace je trochu méně robustní než ta v ASP.NET Core, protože ji budete muset při nasazení aplikace změnit.

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

Viz [Webová aplikace, která přihlašuje uživatele: Konfigurace kódu](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) pro pochopení, jak ukázka Java získá autorizační kód. Poté, co aplikace obdrží kód, [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Deleguje delegáty `AuthHelper.processAuthenticationCodeRedirect` metody v [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Volání `getAuthResultByAuthCode` .

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

`getAuthResultByAuthCode`Metoda je definována v [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Vytvoří MSAL `ConfidentialClientApplication` a pak zavolá metodu `acquireToken()` `AuthorizationCodeParameters` vytvořenou z autorizačního kódu.

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

Tok autorizačního kódu se požaduje, jak je znázorněno ve [webové aplikaci, která přihlašuje uživatele: Konfigurace kódu](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Kód se pak obdrží ve `authorized` funkci, která prochází trasy z `/getAToken` adresy URL. Úplný kontext tohoto kódu naleznete v tématu [App. py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) :

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

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Kurz ASP.NET Core používá vkládání závislostí a umožňuje určit implementaci mezipaměti tokenů v souboru Startup.cs pro vaši aplikaci. Microsoft. identity. Web přichází s předem vytvořenými serializátory mezipaměti tokenů, které jsou popsané v tématu [serializace mezipaměti tokenů](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application). Zajímavou možností je zvolit ASP.NET Core [distribuované mezipaměti paměti](/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

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

Podrobnosti o poskytovatelích mezipaměti tokenů najdete v článku také o aplikacích Microsoft. identity. Web pro [serializaci mezipaměti tokenu](https://aka.ms/ms-id-web/token-cache-serialization) a také o [ASP.NET Core výukových kurzů pro webové aplikace | Fáze mezipamětí tokenů](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) v kurzu Web Apps.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

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

# <a name="java"></a>[Java](#tab/java)

MSAL Java poskytuje metody pro serializaci a deserializaci mezipaměti tokenů. Ukázka Java zpracovává serializaci z relace, jak je znázorněno v `getAuthResultBySilentFlow` metodě v [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

# <a name="python"></a>[Python](#tab/python)

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

[Odebrání účtů z mezipaměti při globálním odhlašování](scenario-web-app-call-api-sign-in.md)
