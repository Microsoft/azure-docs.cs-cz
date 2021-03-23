---
title: Konfigurace webové aplikace, která podepisuje uživatele | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak vytvořit webovou aplikaci, která přihlašuje uživatele (konfigurace kódu).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: cafd42653ca220670081cff102ba8be2de58f4a1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779949"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Webová aplikace, která podepisuje uživatele: Konfigurace kódu

Přečtěte si, jak nakonfigurovat kód pro webovou aplikaci, která se přihlásí uživatelům.

## <a name="microsoft-libraries-supporting-web-apps"></a>Knihovny Microsoftu podporující webové aplikace

<!-- This section can be in an include for web app and web APIs -->
K ochraně webové aplikace (a webového rozhraní API) se používají následující knihovny Microsoftu:

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

Vyberte kartu, která odpovídá platformě, na kterou zajímáte:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Fragmenty kódu v tomto článku a následující jsou extrahovány z [přírůstkového kurzu ASP.NET Core webové aplikace, kapitola 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Informace o úplné implementaci najdete v tomto kurzu.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Fragmenty kódu v tomto článku a níže jsou extrahovány z [ukázky webové aplikace ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

# <a name="java"></a>[Java](#tab/java)

Fragmenty kódu v tomto článku a níže se extrahují z [webové aplikace Java Calling Sample v Microsoft graphu](https://github.com/Azure-Samples/ms-identity-java-webapp) v MSAL Java.

Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Fragmenty kódu v tomto článku a níže jsou extrahovány z [Node.js webové aplikace Signing Users in](https://github.com/Azure-Samples/ms-identity-node) Sample v uzlu MSAL.

Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

# <a name="python"></a>[Python](#tab/python)

Fragmenty kódu v tomto článku a níže jsou extrahovány z [webové aplikace Python s voláním ukázky Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) v MSAL Pythonu.

Můžete chtít použít tuto ukázku k tomu, abyste si mohli zobrazit úplné podrobnosti implementace.

---

## <a name="configuration-files"></a>Konfigurační soubory

Webové aplikace, které přihlásí uživatele pomocí platformy Microsoft identity, jsou konfigurovány prostřednictvím konfiguračních souborů. Jedná se o hodnoty, které je nutné zadat v konfiguraci:

- Instance cloudu ( `Instance` ), pokud chcete, aby aplikace běžela v národních cloudech, například
- Cílová skupina v ID tenanta ( `TenantId` )
- ID klienta ( `ClientId` ) pro vaši aplikaci, jak je zkopírované z Azure Portal

Můžete také zobrazit odkazy na `Authority` . `Authority`Hodnota je zřetězení `Instance` `TenantId` hodnot a.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core se tato nastavení nacházejí v [appsettings.js](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) v souboru v části "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.partner.microsoftonline.cn/common" for Azure AD China operated by 21Vianet
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
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

V ASP.NET Core obsahuje jiný soubor ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) adresu URL ( `applicationUrl` ) a port TLS/SSL ( `sslPort` ) pro vaši aplikaci a různé profily.

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

V Azure Portal musí identifikátory URI pro přesměrování, které jste zaregistrovali na stránce **ověřování** pro vaši aplikaci, odpovídat těmto adresám URL. Pro dva předchozí konfigurační soubory by to bylo `https://localhost:44321/signin-oidc` . Důvodem je, že `applicationUrl` je `http://localhost:3110` , ale `sslPort` je zadaný (44321). `CallbackPath` je `/signin-oidc` , jak je definováno v `appsettings.json` .

Stejným způsobem by byl identifikátor URI pro odhlášení nastaven na hodnotu `https://localhost:44321/signout-oidc` .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET se aplikace konfiguruje pomocí souboru [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) , řádky 12 až 15.

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

V Azure Portal musí identifikátory URI odpovědi, které zaregistrujete na **ověřovací** stránce vaší aplikace, odpovídat těmto adresám URL. To znamená, že by měly být `https://localhost:44326/` .

# <a name="java"></a>[Java](#tab/java)

V jazyce Java je konfigurace umístěna v souboru [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) , který je umístěn v části `src/main/resources` .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

V Azure Portal musí identifikátory URI odpovědi, které zaregistrujete na **ověřovací** stránce vaší aplikace, odpovídat `redirectUri` instancím, které aplikace definuje. To znamená, že by měly být `http://localhost:8080/msal4jsample/secure/aad` a `http://localhost:8080/msal4jsample/graph/me` .

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Tady se nachází parametry konfigurace. `index.js`

```javascript

const REDIRECT_URI = "http://localhost:3000/redirect";

const config = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/",
        clientSecret: "Enter_the_Client_Secret_Here"
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: msal.LogLevel.Verbose,
        }
    }
};
```

V Azure Portal musí identifikátory URI odpovědi, které zaregistrujete na ověřovací stránce vaší aplikace, odpovídat instancím redirectUri, které aplikace definuje ( `http://localhost:3000/redirect` ).

> [!NOTE]
> V tomto rychlém startu se navrhuje Uložit tajný klíč klienta do konfiguračního souboru pro zjednodušení. Ve vaší produkční aplikaci byste chtěli použít jiné způsoby ukládání tajného kódu, jako je například Trezor klíčů nebo proměnná prostředí.

# <a name="python"></a>[Python](#tab/python)

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

Inicializační kód se liší v závislosti na platformě. Pro ASP.NET Core a ASP.NET se podepisování uživatelů přidělí na middleware OpenID Connect. Šablona ASP.NET nebo ASP.NET Core generuje webové aplikace pro koncový bod Azure Active Directory (Azure AD) v 1.0. Některá konfigurace je nutná k jejich přizpůsobení platformě Microsoft Identity Platform. V případě jazyka Java se v případě, že se jedná o spolupráci aplikace, zpracovává na jaře.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core Web Apps (a webová rozhraní API) je aplikace chráněná, protože máte `[Authorize]` atribut na řadičích nebo na akcích kontroleru. Tento atribut kontroluje, jestli je uživatel ověřený. Kód, který inicializuje aplikaci, je v souboru *Startup. cs* .

Pokud chcete přidat ověřování s platformou Microsoft identity (dřív Azure AD v 2.0), budete muset přidat následující kód. Komentáře v kódu by měly být vysvětlivekné.

> [!NOTE]
> Pokud chcete začít přímo s novou šablonou ASP.NET Core pro Microsoft Identity Platform, která využívá Microsoft. identity. Web, můžete si stáhnout balíček NuGet verze Preview obsahující šablony projektu pro .NET Core 3,1 a .NET 5,0. Po instalaci pak můžete přímo vytvořit instanci ASP.NET Core webových aplikací (MVC nebo Blazor). Podrobnosti najdete v tématu [šablony projektů Microsoft. identity. Web Web App](https://aka.ms/ms-id-web/webapp-project-templates) . Toto je nejjednodušší způsob, jak se provede všemi kroky uvedenými za vás.
>
> Pokud dáváte přednost spuštění projektu s aktuálním výchozím ASP.NET Corem webovým projektem v sadě Visual Studio nebo pomocí `dotnet new mvc --auth SingleAuth` nebo `dotnet new webapp --auth SingleAuth` , zobrazí se kód podobný následujícímu:
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> Tento kód používá starší verzi balíčku NuGet **Microsoft. AspNetCore. Authentication. AzureAD. UI** , která se používá k vytvoření aplikace Azure AD v 1.0. Tento článek vysvětluje, jak vytvořit aplikaci Microsoft Identity Platform (Azure AD v 2.0), která tento kód nahrazuje.
>

1. Do svého projektu přidejte balíčky NuGet [Microsoft. identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) a [Microsoft. identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) . Odeberte balíček NuGet Microsoft. AspNetCore. Authentication. AzureAD. UI, pokud je k dispozici.

2. Aktualizujte kód `ConfigureServices` tak, aby používal `AddMicrosoftIdentityWebAppAuthentication` `AddMicrosoftIdentityUI` metody a.

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. V `Configure` metodě v *Startup. cs* povolte ověřování pomocí volání `app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

Ve výše uvedeném kódu:
- `AddMicrosoftIdentityWebAppAuthentication`Metoda rozšíření je definována v **Microsoft. identity. Web**. Její
  - Přidá ověřovací službu.
  - Konfiguruje možnosti pro čtení konfiguračního souboru (zde z části "AzureAD").
  - Nakonfiguruje možnosti připojení OpenID, aby autorita byla platformou Microsoft identity.
  - Ověří vystavitele tokenu.
  - Zajistí, aby deklarace odpovídající názvu byly namapovány z `preferred_username` deklarace identity v tokenu ID.

- Kromě objektu konfigurace můžete zadat název konfiguračního oddílu při volání `AddMicrosoftIdentityWebAppAuthentication` . Ve výchozím nastavení je to `AzureAd` .

- `AddMicrosoftIdentityWebAppAuthentication` obsahuje další parametry pro pokročilé scénáře. Například trasování událostí middlewaru OpenID Connect může pomoct při odstraňování potíží s webovou aplikací, pokud ověřování nefunguje. Nastavením volitelného parametru zobrazíte `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` `true` informace o tom, jak jsou zpracovány sadou ASP.NET Core middleware v průběhu reakce http na identitu uživatele v nástroji `HttpContext.User` .

- `AddMicrosoftIdentityUI`Metoda rozšíření je definována v **Microsoft. identity. Web. UI**. Poskytuje výchozí kontroler pro zpracování přihlášení a odhlášení.

Další informace o tom, jak Microsoft. identity. Web umožňuje vytvářet webové aplikace, najdete v tématu. <https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> V současné době Microsoft. identity. Web nepodporuje scénář **individuálních uživatelských účtů** (při ukládání uživatelských účtů v aplikaci) při použití Azure AD jako a externího poskytovatele přihlášení. Podrobnosti najdete zde: [AzureAD/Microsoft-Identity-web # 133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kód, který se vztahuje k ověřování ve webové aplikaci ASP.NET a webových rozhraních API, se nachází v souboru [app_start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
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

Ukázka Java používá architekturu pružiny. Aplikace je chráněna, protože implementujete filtr, který zachycuje každou odpověď HTTP. V rychlém startu pro webové aplikace v jazyce Java je tento filtr `AuthFilter` v `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` .

Filtr zpracovává tok autorizačního kódu OAuth 2,0 a kontroluje, jestli je uživatel ověřený ( `isAuthenticated()` Metoda). Pokud uživatel není ověřený, vypočítá adresu URL koncových bodů autorizace Azure AD a přesměruje prohlížeč na tento identifikátor URI.

Když odpověď dorazí, která obsahuje autorizační kód, získá token pomocí MSAL Java. Pokud nakonec obdrží token z koncového bodu tokenu (na identifikátoru URI přesměrování), je uživatel přihlášený.

Podrobnosti naleznete v `doFilter()` metodě v [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Kód `doFilter()` je napsán v mírně jiném pořadí, ale tok je popsán.

Podrobnosti o toku autorizačního kódu, který tato metoda aktivuje, najdete v [toku autorizačního kódu platformy Microsoft a OAuth 2,0](v2-oauth2-auth-code-flow.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const msal = require('@azure/msal-node');

// Create msal application object
const cca = new msal.ConfidentialClientApplication(config);
```

# <a name="python"></a>[Python](#tab/python)

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

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Přejděte k dalšímu článku v tomto scénáři, [Přihlaste se a odhlaste](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore)se.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Přejděte k dalšímu článku v tomto scénáři, [Přihlaste se a odhlaste](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet)se.

# <a name="java"></a>[Java](#tab/java)

Přejděte k dalšímu článku v tomto scénáři, [Přihlaste se a odhlaste](./scenario-web-app-sign-user-sign-in.md?tabs=java)se.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Přejděte k dalšímu článku v tomto scénáři, [přihlaste](./scenario-web-app-sign-user-sign-in.md?tabs=nodejs)se.

# <a name="python"></a>[Python](#tab/python)

Přejděte k dalšímu článku v tomto scénáři, [Přihlaste se a odhlaste](./scenario-web-app-sign-user-sign-in.md?tabs=python)se.

---
