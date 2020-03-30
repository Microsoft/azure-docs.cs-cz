---
title: Konfigurace webové aplikace, která se připisuje k uživatelům – platforma identit Microsoftu | Azure
description: Přečtěte si, jak vytvořit webovou aplikaci, která se přihlásí k uživatelům (konfigurace kódu)
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
ms.openlocfilehash: 51cd7ff97af4588139721930bd4d08ffd0f95e73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297550"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Webová aplikace, která se připisuje k uživatelům: Konfigurace kódu

Přečtěte si, jak nakonfigurovat kód webové aplikace, který připisuje uživatele.

## <a name="libraries-for-protecting-web-apps"></a>Knihovny pro ochranu webových aplikací

<!-- This section can be in an include for Web App and Web APIs -->
Knihovny, které se používají k ochraně webové aplikace (a webové rozhraní API), jsou:

| Platforma | Knihovna | Popis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Rozšíření modelu identity pro rozhraní .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Rozšíření modelu Identity Společnosti Microsoft pro rozhraní .NET, které přímo používají ASP.NET a ASP.NET core, navrhují sadu knihoven DLL spuštěných v rozhraní .NET Framework i .NET Core. Z webové aplikace ASP.NET nebo ASP.NET Core můžete řídit ověřování tokenu pomocí **třídy TokenValidationParameters** (zejména v některých partnerských scénářích). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL v Javě](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Podpora webových aplikací java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Podpora webových aplikací pythonu |

Vyberte kartu, která odpovídá platformě, která vás zajímá:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Fragmenty kódu v tomto článku a následující jsou extrahovány z [ASP.NET základní webové aplikace přírůstkové kurzu, kapitola 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Můžete chtít odkazovat na tomto kurzu pro úplné podrobnosti implementace.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Fragmenty kódu v tomto článku a následující jsou extrahovány z [ukázky ASP.NET webové aplikace](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Můžete chtít odkazovat na této ukázce podrobnosti o implementaci.

# <a name="java"></a>[Java](#tab/java)

Fragmenty kódu v tomto článku a následující jsou extrahovány z [webové aplikace Java volání microsoft graf](https://github.com/Azure-Samples/ms-identity-java-webapp) ukázku v Jazyce MSAL Java.

Můžete chtít odkazovat na této ukázce podrobnosti o implementaci.

# <a name="python"></a>[Python](#tab/python)

Fragmenty kódu v tomto článku a následující jsou extrahovány z [webové aplikace Python volání microsoft graf](https://github.com/Azure-Samples/ms-identity-python-webapp) ukázku v MSAL Python.

Můžete chtít odkazovat na této ukázce podrobnosti o implementaci.

---

## <a name="configuration-files"></a>Konfigurační soubory

Webové aplikace, které přihlašují uživatele pomocí platformy microsoft identity, jsou obvykle konfigurovány prostřednictvím konfiguračních souborů. Nastavení, která je třeba vyplnit, jsou:

- Instance cloudu`Instance`( ) pokud chcete, aby vaše aplikace běžela v národních cloudech, například
- Okruh uživatelů v ID`TenantId`klienta ( )
- ID klienta`ClientId`( ) pro vaši aplikaci, jak je zkopírováno z portálu Azure

Někdy mohou být aplikace `Authority`parametrizovány pomocí , `Instance` což `TenantId`je zřetězení a .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core, tato nastavení jsou umístěny v souboru [appsettings.json,](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) v části "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

V ASP.NET Core obsahuje jiný soubor ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) adresu URL (`applicationUrl`)`sslPort`a port TLS/SSL ( ) pro vaši aplikaci a různé profily.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Na webu Azure Portal musí odpovídat adresám URL odpovědi, které je třeba zaregistrovat na stránce **Ověřování** pro vaši aplikaci, aby odpovídaly těmto adresám URL. Pro dva předchozí konfigurační `https://localhost:44321/signin-oidc`soubory by byly . Důvodem je, `http://localhost:3110`že `sslPort` `applicationUrl` je , ale je zadán (44321). `CallbackPath`je `/signin-oidc`, jak `appsettings.json`je definováno v .

Stejným způsobem by byl identifikátor URI pro `https://localhost:44321/signout-callback-oidc`odhlášení nastaven na .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET je aplikace konfigurována prostřednictvím souboru [Web.config,](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) řádků 12 až 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Na webu Azure Portal musí odpovídat adresám URL odpovědi, které je třeba zaregistrovat na stránce **Ověřování** pro vaši aplikaci, aby odpovídaly těmto adresám URL. To znamená, že `https://localhost:44326/`by měly být .

# <a name="java"></a>[Java](#tab/java)

V jazyce Java je konfigurace umístěna v `src/main/resources`souboru [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) umístěném pod .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Na webu Azure Portal musí informace o adresách **Authentication** URL s odpovědí, které `redirectUri` je třeba zaregistrovat na stránce Ověřování pro vaši aplikaci, odpovídat instancím, které aplikace definuje. To znamená, že `http://localhost:8080/msal4jsample/secure/aad` `http://localhost:8080/msal4jsample/graph/me`by měly být a .

# <a name="python"></a>[Python](#tab/python)

Zde je konfigurační soubor Pythonu v [app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Tento rychlý start navrhuje uložit tajný klíč klienta do konfiguračního souboru pro jednoduchost. V produkční aplikaci byste chtěli použít jiné způsoby ukládání tajných klíčů, například trezor klíčů nebo proměnnou prostředí, jak je popsáno v [dokumentaci Flask](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Inicializační kód

Inicializační kód se liší v závislosti na platformě. Pro ASP.NET core a ASP.NET je přihlášení uživatelů delegováno do middlewaru OpenID Connect. Šablona ASP.NET nebo ASP.NET Core generuje webové aplikace pro koncový bod Azure Active Directory (Azure AD) v1.0. Některé konfigurace je nutné přizpůsobit platformě identit y Microsoft (v2.0) koncový bod. V případě Javy ji ve spolupráci s aplikací řeší jaro.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET základní webové aplikace (a webové api), aplikace `[Authorize]` je chráněna, protože máte atribut na řadiče nebo akce kontroleru. Tento atribut kontroluje, zda je uživatel ověřen. Kód, který inicializuje aplikaci, je v souboru Startup.cs.

Chcete-li přidat ověřování pomocí platformy identit Microsoftu (dříve Azure AD v2.0), budete muset přidat následující kód. Komentáře v kódu by měly být samozřejmé.

> [!NOTE]
> Pokud spustíte projekt s výchozí ASP.NET webový projekt Core `dotnet new mvc`v `AddAzureAD` rámci sady Visual Studio nebo pomocí aplikace , metoda je k dispozici ve výchozím nastavení. Je to proto, že související balíčky jsou automaticky načteny.
>
> Pokud vytvoříte projekt od začátku a pokoušíte se použít následující kód, doporučujeme přidat balíček NuGet **Microsoft.AspNetCore.Authentication.AzureAD.UI** do projektu, aby `AddAzureAD` byla metoda k dispozici.

Následující kód je k dispozici od [Startup.cs#L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

Metoda `AddMicrosoftIdentityPlatformAuthentication` rozšíření je definována v [souboru Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Je to:

- Přidá ověřovací službu.
- Nakonfiguruje možnosti čtení konfiguračního souboru.
- Nakonfiguruje možnosti OpenID Connect tak, aby použitá autorita byla koncový bod platformy identit y Microsoft (dříve Azure AD v2.0).
- Ověří vystavittele tokenu.
- Zajišťuje, že deklarace odpovídající názvu jsou `preferred_username` mapovány z deklarace identity v tokenu ID.

Kromě konfigurace můžete při volání `AddMicrosoftIdentityPlatformAuthentication`zadat název konfigurační ho oddílu . Ve výchozím nastavení `AzureAd`je to .

Trasování OpenId Connect middleware události vám mohou pomoci při řešení potíží s vaší webové aplikace, pokud ověřování nefunguje. Nastavení `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` `true` na vám ukáže, jak se informace zpracovává pomocí sady middleware ASP.NET Core, jak postupuje z `HttpContext.User`odpovědi HTTP na identitu uživatele v .

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

Třída `AadIssuerValidator` umožňuje vystavithotokenu, který má být ověřen v mnoha případech. Tato třída funguje s tokenem v1.0 nebo v2.0, aplikací s jedním tenantem nebo více klienty nebo aplikací, která se přihlašuje k uživatelům pomocí jejich osobních účtů Microsoft ve veřejném cloudu Azure nebo v národních cloudech. Je k dispozici na [adrese Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kód související s ověřováním ve ASP.NET webové aplikaci a webových api je umístěn v souboru [App_Start/Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Java ukázka používá rámec spring. Aplikace je chráněna, protože implementujete filtr, který zachycuje každou odpověď HTTP. V rychlém startu pro webové aplikace `AuthFilter` Java `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`je tento filtr v aplikaci .

Filtr zpracuje tok autorizačního kódu OAuth 2.0`isAuthenticated()` a zkontroluje, zda je uživatel ověřen (metoda). Pokud uživatel není ověřen, vypočítá adresu URL koncových bodů autorizace Azure AD a přesměruje prohlížeč na tento identifikátor URI.

Když přijde odpověď, obsahující autorizační kód, získá token pomocí Jazyka Java MSAL. Když konečně obdrží token z koncového bodu tokenu (na identifikátorURI přesměrování), uživatel je přihlášen.

Podrobnosti naleznete `doFilter()` v metodě [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Kód `doFilter()` je napsán v mírně jiném pořadí, ale tok je popsán.

Podrobnosti o toku autorizačního kódu, který tato metoda aktivuje, naleznete [v tématu Platformidentity Microsoft a Tok autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

Ukázka Pythonu používá Flask. Inicializace Flask a MSAL Python se provádí v [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak aktivovat přihlášení a odhlášení.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
