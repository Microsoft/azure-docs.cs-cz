---
title: Konfigurace webové aplikace, která podepisuje uživatele – Microsoft Identity Platform | Azure
description: Naučte se, jak vytvořit webovou aplikaci, která přihlašuje uživatele (konfigurace kódu).
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
ms.openlocfilehash: b077a71a541d29c9b93778babc096ea40c3b43cb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964867"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Webová aplikace, která podepisuje uživatele: Konfigurace kódu

Přečtěte si, jak nakonfigurovat kód pro webovou aplikaci, která se přihlásí uživatelům.

## <a name="libraries-for-protecting-web-apps"></a>Knihovny pro ochranu webových aplikací

<!-- This section can be in an include for Web App and Web APIs -->
Knihovny, které slouží k ochraně webové aplikace (a webového rozhraní API):

| Platforma | Knihovna | Popis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Rozšíření modelu identity pro .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Rozšíření Microsoft Identity Model pro .NET, které používá přímo ASP.NET a ASP.NET Core, navrhuje sadu knihoven DLL spouštěných v .NET Framework i .NET Core. Z webové aplikace ASP.NET nebo ASP.NET Core můžete ověřování tokenu řídit pomocí třídy **TokenValidationParameters** (konkrétně v některých partnerských scénářích). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Knihovna Microsoft Authentication Library (MSAL) pro jazyk Java. Aktuálně ve verzi Public Preview. |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL pro Python. Aktuálně ve verzi Public Preview. |

Vyberte kartu, která odpovídá platformě, na kterou zajímáte:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Fragmenty kódu v tomto článku a následující jsou extrahovány z [přírůstkového kurzu ASP.NET Core webové aplikace, kapitola 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Informace o úplné implementaci najdete v tomto kurzu.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Fragmenty kódu v tomto článku a níže jsou extrahovány z [ukázky webové aplikace ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

# <a name="javatabjava"></a>[Java](#tab/java)

Fragmenty kódu v tomto článku a níže se extrahují z [webové aplikace Java Calling Sample v Microsoft graphu](https://github.com/Azure-Samples/ms-identity-java-webapp) v MSAL Java.

Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

# <a name="pythontabpython"></a>[Python](#tab/python)

Fragmenty kódu v tomto článku a níže jsou extrahovány z [webové aplikace Python s voláním ukázky Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) v MSAL Pythonu.

Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

---

## <a name="configuration-files"></a>Konfigurační soubory

Webové aplikace, které přihlásí uživatele pomocí platformy Microsoft identity, jsou obvykle konfigurovány prostřednictvím konfiguračních souborů. Nastavení, která je třeba vyplnit, jsou následující:

- Instance cloudu (`Instance`), pokud chcete, aby aplikace běžela v národních cloudech, například
- Cílová skupina v ID tenanta (`TenantId`)
- ID klienta (`ClientId`) pro vaši aplikaci, jak je zkopírované z Azure Portal

V některých případech je možné aplikace určit podle `Authority`, což je zřetězení `Instance` a `TenantId`.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core se tato nastavení nacházejí v souboru [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) v části "AzureAd".

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
    "TenantId": "[Enter the tenantId here]]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

V ASP.NET Core obsahuje jiný soubor ([properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) adresu URL (`applicationUrl`) a port SSL (`sslPort`) pro vaši aplikaci a různé profily.

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

V Azure Portal musí být identifikátory URI odpovědi, které je třeba registrovat na **ověřovací** stránce vaší aplikace, odpovídat těmto adresám URL. Pro dva předchozí konfigurační soubory by byly `https://localhost:44321/signin-oidc`. Důvodem je, že `applicationUrl` je `http://localhost:3110`, ale je zadána `sslPort` (44321). `CallbackPath` je `/signin-oidc`, jak je definováno v `appsettings.json`.

Stejným způsobem by byl identifikátor URI pro odhlášení nastaven na `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET je aplikace nakonfigurována pomocí souboru [Web. config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) , řádky 12 až 15.

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

V Azure Portal musí být identifikátory URI odpovědi, které je třeba registrovat na **ověřovací** stránce vaší aplikace, odpovídat těmto adresám URL. To znamená, že by měly být `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

V jazyce Java je konfigurace umístěna v souboru [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) , který je umístěn v části `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

V Azure Portal musí být identifikátory URI odpovědi, které je třeba registrovat na **ověřovací** stránce vaší aplikace, odpovídat `redirectUri` instancím, které aplikace definuje. To znamená, že by měly být `http://localhost:8080/msal4jsample/secure/aad` a `http://localhost:8080/msal4jsample/graph/me`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Tady je konfigurační soubor Pythonu v [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> V tomto rychlém startu se navrhuje Uložit tajný klíč klienta do konfiguračního souboru pro zjednodušení. Ve vaší produkční aplikaci byste chtěli použít jiné způsoby ukládání tajného klíče, jako je například Trezor klíčů nebo proměnná prostředí, jak je popsáno v [dokumentaci k baňce](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Inicializační kód

Inicializační kód se liší v závislosti na platformě. Pro ASP.NET Core a ASP.NET se podepisování uživatelů přidělí na middleware OpenID Connect. Šablona ASP.NET nebo ASP.NET Core generuje webové aplikace pro koncový bod Azure Active Directory (Azure AD) v 1.0. Některá konfigurace je nutná k jejich přizpůsobení koncovému bodu Microsoft Identity Platform (v 2.0). V případě jazyka Java se v případě, že se jedná o spolupráci aplikace, zpracovává na jaře.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core Web Apps (a webová rozhraní API) je aplikace chráněná, protože máte atribut `[Authorize]` na řadičích nebo akcích kontroleru. Tento atribut kontroluje, jestli je uživatel ověřený. Kód, který inicializuje aplikaci, je v souboru Startup.cs. 

Pokud chcete přidat ověřování s platformou Microsoft identity (dřív Azure AD v 2.0), budete muset přidat následující kód. Komentáře v kódu by měly být vysvětlivekné.

> [!NOTE]
> Pokud spustíte projekt s výchozím ASP.NET Core webový projekt v sadě Visual Studio nebo pomocí `dotnet new mvc`, je metoda `AddAzureAD` ve výchozím nastavení dostupná. To je proto, že související balíčky jsou automaticky načteny.
>
> Pokud vytvoříte projekt od začátku a pokoušíte se použít následující kód, doporučujeme přidat do projektu balíček NuGet **Microsoft. AspNetCore. Authentication. AzureAD. UI** , aby byla dostupná metoda `AddAzureAD`.

Následující kód je k dispozici z [Startup. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```CSharp
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

Metoda rozšíření `AddMicrosoftIdentityPlatformAuthentication` je definována v [Microsoft. identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Její

- Přidá ověřovací službu.
- Nakonfiguruje možnosti pro čtení konfiguračního souboru.
- Nakonfiguruje možnosti připojení OpenID, aby použitá autorita byla koncovým bodem Microsoft Identity Platform (dříve Azure AD v 2.0).
- Ověří vystavitele tokenu.
- Zajišťuje, aby deklarace odpovídající názvu byly namapovány z deklarace identity `preferred_username` v tokenu ID.

Kromě konfigurace můžete zadat název konfiguračního oddílu při volání `AddMicrosoftIdentityPlatformAuthentication`. Ve výchozím nastavení je to `AzureAd`.

Trasování událostí middlewaru OpenId Connect vám může pomoct při řešení potíží s webovou aplikací, pokud ověřování nefunguje. Nastavením `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` na `true` se dozvíte, jak budou informace vypracované sadou ASP.NET Core middleware v průběhu reakce protokolu HTTP na identitu uživatele v `HttpContext.User`.

```CSharp
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

Třída `AadIssuerValidator` umožňuje vystaviteli tokenu ověřit v mnoha případech. Tato třída spolupracuje s tokenem v 1.0 nebo v 2.0, s jedním klientem nebo víceklientské aplikací nebo s aplikací, která podepisuje uživatele se svými osobními účty Microsoft ve veřejném cloudu Azure nebo v národních cloudech. Je k dispozici z [Microsoft. identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Kód, který se vztahuje k ověřování ve webové aplikaci ASP.NET a webových rozhraních API, se nachází v souboru [app_start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```CSharp
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

# <a name="javatabjava"></a>[Java](#tab/java)

Ukázka Java používá architekturu pružiny. Aplikace je chráněna, protože implementujete filtr, který zachycuje každou odpověď HTTP. V rychlém startu pro webové aplikace v jazyce Java je tento filtr `AuthFilter` v `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`. 

Filtr zpracovává tok autorizačního kódu OAuth 2,0 a kontroluje, jestli je uživatel ověřený (`isAuthenticated()` metoda). Pokud uživatel není ověřený, vypočítá adresu URL koncových bodů autorizace Azure AD a přesměruje prohlížeč na tento identifikátor URI.

Když odpověď dorazí, která obsahuje autorizační kód, získá token pomocí MSAL Java. Pokud nakonec obdrží token z koncového bodu tokenu (na identifikátoru URI přesměrování), je uživatel přihlášený.

Podrobnosti najdete v tématu metoda `doFilter()` v [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Kód `doFilter()` je napsán v trochu jiném pořadí, ale tok je popsán.

Podrobnosti o toku autorizačního kódu, který tato metoda aktivuje, najdete v tématu [tok autorizačního kódu Microsoft Identity Platform a OAuth 2,0](v2-oauth2-auth-code-flow.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

Ukázka Pythonu používá baňky. Inicializace baněk a MSAL Pythonu se provádí v [App. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
