---
title: Webová aplikace, která přihlašuje uživatele (konfigurace kódu) – Microsoft Identity Platform
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1453821561ab7bb361fbb3e5d57634cf23a7be2c
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310063"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Webová aplikace, která přihlásí uživatele – konfigurace kódu

Přečtěte si, jak nakonfigurovat kód pro vaši webovou aplikaci, která se přihlásí uživatelům.

## <a name="libraries-used-to-protect-web-apps"></a>Knihovny používané k ochraně Web Apps

<!-- This section can be in an include for Web App and Web APIs -->
Knihovny používané k ochraně webové aplikace (a webového rozhraní API) jsou:

| Platforma | Knihovna | Popis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Rozšíření modelu identity pro .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Microsoft Identity Extensions for .NET, který používá přímo ASP.NET a ASP.NET Core, navrhuje sadu knihoven DLL běžících jak v .NET Framework a .NET Core. Z webové aplikace ASP.NET/ASP.NET Core můžete řídit ověření tokenu pomocí třídy **TokenValidationParameters** (zejména v některých scénářích ISV). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL pro jazyk Java v současnosti ve verzi Public Preview |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL pro Python – aktuálně ve verzi Public Preview |

Vyberte kartu odpovídající platformě, které vás zajímá:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Fragmenty kódu v tomto článku a následující jsou extrahovány z [přírůstkového kurzu ASP.NET Core webové aplikace, kapitola 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Informace o úplné implementaci najdete v tomto kurzu.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Fragmenty kódu v tomto článku a níže se extrahují z [ukázky webové aplikace ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) .

Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

# <a name="javatabjava"></a>[Java](#tab/java)

Fragmenty kódu v tomto článku a níže jsou extrahovány z [webové aplikace Java volání Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) msal4j Web App Sample

Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

# <a name="pythontabpython"></a>[Python](#tab/python)

Fragmenty kódu v tomto článku a níže se extrahují z [webové aplikace v Pythonu, která volá Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Ukázka webové aplikace v Pythonu

Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

---

## <a name="configuration-files"></a>Konfigurační soubory

Webové aplikace, které se přihlašují uživatelům pomocí platformy Microsoft Identity Platform, se obvykle konfigurují prostřednictvím konfiguračních souborů. Nastavení, která je třeba vyplnit, jsou následující:

- Cloud `Instance` , pokud chcete, aby se vaše aplikace spouštěla (například v národních cloudech)
- Cílová skupina v`tenantId`
- `clientId` pro aplikaci, jak je zkopírováno z Azure Portal.

V některých případech mohou být aplikace parametry `authority`typu, což je zřetězení a `instance``tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core se tato nastavení nacházejí v souboru [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) v části "AzureAD".

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

V ASP.NET Core existuje další [properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) souboru, který obsahuje adresu URL (`applicationUrl`) a port SSL (`sslPort`) pro vaši aplikaci a různé profily.

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

V Azure Portal musí být identifikátory URI odpovědi, které je třeba registrovat na **ověřovací** stránce vaší aplikace, odpovídat těmto adresám URL; To znamená, že pro dva konfigurační soubory `https://localhost:44321/signin-oidc` výše by to bylo, že ApplicationUrl nebyla je `http://localhost:3110` , `sslPort` ale `CallbackPath` je zadána (44321) a je `/signin-oidc` definován v `appsettings.json`.
  
Stejným způsobem by byl identifikátor URI pro odhlášení nastaven na `https://localhost:44321/signout-callback-oidc`hodnotu.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET je aplikace nakonfigurována pomocí řádků souboru [Web. Config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) 12-15

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
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

V Azure Portal musí být identifikátory URI odpovědi, které je třeba registrovat na **ověřovací** stránce vaší aplikace, odpovídat těmto adresám URL; To znamená `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

V jazyce Java se konfigurace nachází v souboru [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) , který se nachází v části`src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

V Azure Portal musí být identifikátory URI odpovědi, které je třeba registrovat na stránce **ověřování** pro vaši aplikaci, stejné jako redirectUris definované aplikací, tj `http://localhost:8080/msal4jsample/secure/aad` .`http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

Tady je konfigurační soubor Pythonu v [app_config. py.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> V tomto rychlém startu se navrhuje Uložit tajný klíč klienta do konfiguračního souboru pro zjednodušení. V produkční aplikaci byste chtěli použít jiné způsoby ukládání tajného klíče, jako je třeba Trezor klíčů nebo proměnná prostředí, jak je popsáno v dokumentaci k baňce: https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Inicializační kód

Inicializační kód se liší v závislosti na platformě. Pro ASP.NET Core a ASP.NET se podepisování uživatelů přidělí na middleware OpenIDConnect. V současné době šablona ASP.NET/ASP.NET Core vygeneruje webové aplikace pro koncový bod Azure AD v 1.0. Proto je třeba nakonfigurovat bitovou kopii, aby byla přizpůsobena na koncový bod Microsoft Identity Platform (v 2.0). V případě jazyka Java se v případě, že se jedná o spolupráci aplikace, zpracovává na jaře.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core Web Apps (a webových rozhraních API) je aplikace chráněná, protože máte `[Authorize]` atribut na řadičích nebo na akcích kontrol. Tento atribut kontroluje, jestli je uživatel ověřený. Kód, který provádí inicializaci aplikace, je umístěn v `Startup.cs` souboru, a pokud chcete přidat ověřování s platformou Microsoft identity (dříve Azure AD v 2.0), budete muset přidat následující kód. Komentáře v kódu by měly být vysvětlivekné.

  > [!NOTE]
  > Pokud spustíte projekt s výchozím webovým projektem ASP.NET Core v sadě Visual Studio nebo pokud `dotnet new mvc` použijete `AddAzureAD` metodu, je ve výchozím nastavení dostupná, protože související balíčky jsou automaticky načteny.
  > Pokud však sestavíte projekt od začátku a pokoušíte se použít níže uvedený kód, doporučujeme vám přidat balíček NuGet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** do svého projektu, aby byla `AddAzureAD` metoda k dispozici.
  
Následující kód je k dispozici z [Startup. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
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

Je metoda rozšíření definovaná v [Microsoft. identity. Web/WebAppServiceCollectionExtensions. cs # L23.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23) `AddMicrosoftIdentityPlatformAuthentication` Její

- Přidá ověřovací službu.
- konfigurovat možnosti pro čtení konfiguračního souboru
- nakonfiguruje možnosti připojení OpenID, aby použitá autorita byla koncovým bodem Microsoft Identity Platform (dřív Azure AD v 2.0).
- Vystavitel tokenu je ověřený.
- deklarace identity odpovídající názvu se mapují z deklarace identity preferred_username v tokenu ID. 

Kromě konfigurace můžete zadat při volání `AddMicrosoftIdentityPlatformAuthentication`:

- název konfiguračního oddílu (ve výchozím nastavení AzureAD)
- Pokud chcete trasovat události middlewaru OpenIdConnect, což vám může pomáhat při řešení potíží s webovou aplikací, pokud ověřování nefunguje: `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` nastavení `true` na vám ukáže, jak budou informace vypracované sadou ASP.NET Core middleware během zpracování z odpovědi HTTP na identitu uživatele v `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
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
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
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

`AadIssuerValidator` Třída umožňuje Vystavitel tokenu se ověří v mnoha případech (v tokenech v 1.0 nebo v 2.0, v jediném tenantovi nebo v aplikaci víceklientské aplikace nebo aplikaci, která přihlašuje uživatele se svými osobními účty Microsoft, ve veřejném cloudu Azure nebo Národní cloudy). Je k dispozici z [Microsoft. identity. Web/Resource/AadIssuerValidator. cs.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Kód, který se vztahuje k ověřování ve webové aplikaci ASP.NET nebo webové rozhraní API, se nachází v souboru [app_start/Startup. cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ukázka Java používá architekturu pružiny. Aplikace je chráněna `Filter`, protože implementujete, který zachycuje každou odpověď HTTP. V rychlém startu webové aplikace Java je `AuthFilter` tento filtr v. `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` Filtr zpracuje tok autorizačního kódu OAuth 2,0 a proto:

- ověří, jestli je uživatel ověřený (`isAuthenticated()` metoda).
- Pokud uživatel není ověřený, vypočítá adresu URL koncových bodů autorizace Azure AD a přesměruje prohlížeč na tento identifikátor URI.
- když odpověď dorazí, která obsahuje tok kódu ověřování, umožňuje msal4j získat token.
- Pokud nakonec obdrží token z koncového bodu tokenu (na identifikátoru URI přesměrování), je uživatel přihlášený.

Podrobnosti najdete v tématu `doFilter()` metoda v [AuthFilter. Java.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> Kód `doFilter()` je napsán v mírně jiném pořadí, ale tok je popsán.

Podrobnosti o toku autorizačního kódu, který aktivovala Tato metoda, najdete v tématu [Microsoft Identity Platform a flow 2,0 – tok autorizačního kódu OAuth](v2-oauth2-auth-code-flow.md) .

# <a name="pythontabpython"></a>[Python](#tab/python)

Ukázka Pythonu používá baňky. Inicializace baňky a MSAL. Python se provádí v [App. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)

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

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](scenario-web-app-sign-user-sign-in.md)
