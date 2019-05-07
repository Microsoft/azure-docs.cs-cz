---
title: Webové aplikace, který se přihlásí uživatelé (kód konfigurace) – platforma identit Microsoft
description: Zjistěte, jak sestavit webovou aplikaci, který se přihlásí uživatelé (konfigurace kódu)
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
ms.openlocfilehash: 516d1c3440548b43bf6df21dbf5757794634fb12
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074572"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Webová aplikace, které přihlásí uživatelé - konfigurace kódu

Zjistěte, jak nakonfigurovat kód pro webové aplikace, které uživatelé přihlásí.

## <a name="libraries-used-to-protect-web-apps"></a>Knihovny používat k ochraně webových aplikací

<!-- This section can be in an include for Web App and Web APIs -->
Knihovny použitým k zamknutí webovou aplikaci (a webové rozhraní API) jsou:

| Platforma | Knihovna | Popis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Rozšíření modelu identit pro .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Rozšíření identit společnosti Microsoft pro .NET použít přímo v rámci ASP.NET a ASP.NET Core, navrhne sadu knihoven DLL systémem i rozhraní .NET Framework a .NET Core. Z ASP.NET/ASP.NET základní webové aplikace, můžete řídit pomocí ověřování tokenů **parametry tokenvalidationparameters** třídy (zejména v některých scénářích nezávislý výrobce softwaru) |

## <a name="aspnet-core-configuration"></a>Konfigurace ASP.NET Core

### <a name="application-configuration-files"></a>Konfigurační soubory aplikace

V ASP.NET Core se konfigurují webové aplikace přihlášení uživatele s platformou identity Microsoft prostřednictvím `appsettings.json` souboru. Nastavení, které je potřeba vyplnit, musí být:

- cloudu `Instance` Pokud chcete svoji aplikaci spouštět v národních cloudech
- cílové skupiny v `tenantId`
- `clientId` pro vaši aplikaci, jak zkopírovat z portálu Azure portal.

```JSon
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
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

V ASP.NET Core je jiný soubor, který obsahuje adresu URL (`applicationUrl`) a SSL Port (`sslPort`) pro aplikace, jakož i různé profily.

```JSon
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

Na webu Azure Portal, identifikátory URI, které budete muset zaregistrovat v odpovědi **ověřování** musí odpovídat tyto adresy URL stránky pro vaše aplikace; to znamená pro výše uvedené dva konfigurační soubory, byly by `https://localhost:44321/signin-oidc` jako applicationUrl je `http://localhost:3110` ale `sslPort` je zadané (44321) a `CallbackPath` je `/signin-oidc` podle `appsettings.json`.
  
Stejným způsobem, odhlaste identifikátoru URI by byl nastaven na `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Inicializační kód

Webové aplikace ASP.NET Core (a webových rozhraní API), kód způsobem inicializace aplikace nachází v `Startup.cs` souboru, a pokud chcete přidat ověřování pomocí v2.0 Microsoft Identity platform (dříve Azure AD), budete muset přidat následující kód. Komentáře v kódu by měla být zřejmých.

```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Konfigurace technologie ASP.NET

V technologii ASP.NET, aplikace je nakonfigurovaná prostřednictvím `Web.Config` souboru

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  http://go.microsoft.com/fwlink/?LinkId=301880
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

Kód související s ověřováním v rozhraní ASP.NET Web aplikace / webové rozhraní API se nachází v `App_Start/Startup.Auth.cs` souboru.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](scenario-web-app-sign-user-sign-in.md)
