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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a96d17ae7fbe94877032e7b4b2aacb63f6e070ca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562253"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Webová aplikace, která přihlásí uživatele – konfigurace kódu

Přečtěte si, jak nakonfigurovat kód pro vaši webovou aplikaci, která se přihlásí uživatelům.

## <a name="libraries-used-to-protect-web-apps"></a>Knihovny používané k ochraně Web Apps

<!-- This section can be in an include for Web App and Web APIs -->
Knihovny používané k ochraně webové aplikace (a webového rozhraní API) jsou:

| Platforma | Knihovna | Popis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Rozšíření modelu identity pro .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Microsoft Identity Extensions for .NET, který používá přímo ASP.NET a ASP.NET Core, navrhuje sadu knihoven DLL běžících jak v .NET Framework a .NET Core. Z webové aplikace ASP.NET/ASP.NET Core můžete řídit ověření tokenu pomocí třídy **TokenValidationParameters** (zejména v některých scénářích ISV). |

## <a name="aspnet-core-configuration"></a>Konfigurace ASP.NET Core

Fragmenty kódu v tomto článku a následující jsou extrahovány z [přírůstkového kurzu ASP.NET Core webové aplikace, kapitola 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg). Můžete chtít v tomto kurzu vyhledat úplné podrobnosti o implementaci.

### <a name="application-configuration-files"></a>Konfigurační soubory aplikace

V ASP.NET Core je pomocí tohoto `appsettings.json` souboru nakonfigurovaná webová aplikace přihlašování uživatelů pomocí platformy Microsoft Identity Platform. Nastavení, která je třeba vyplnit, jsou následující:

- Cloud `Instance` , pokud chcete, aby se vaše aplikace spouštěla v národních cloudech
- Cílová skupina v`tenantId`
- `clientId` pro aplikaci, jak je zkopírováno z Azure Portal.

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

V ASP.NET Core existuje další soubor, který obsahuje adresu URL (`applicationUrl`) a port SSL (`sslPort`) pro vaši aplikaci i pro různé profily.

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

V Azure Portal musí být identifikátory URI odpovědi, které je třeba registrovat na **ověřovací** stránce vaší aplikace, odpovídat těmto adresám URL; To znamená, že pro dva konfigurační soubory `https://localhost:44321/signin-oidc` výše by to bylo, že ApplicationUrl nebyla je `http://localhost:3110` , `sslPort` ale `CallbackPath` je zadána (44321) a je `/signin-oidc` definován v `appsettings.json`.
  
Stejným způsobem by byl identifikátor URI pro odhlášení nastaven na `https://localhost:44321/signout-callback-oidc`hodnotu.

### <a name="initialization-code"></a>Inicializační kód

V ASP.NET Core Web Apps (a webových rozhraních API) se kód, který provádí inicializaci aplikace, nachází `Startup.cs` v souboru, a pokud chcete přidat ověřování s platformou Microsoft identity (dříve Azure AD v 2.0), budete muset přidat následující kód. Komentáře v kódu by měly být vysvětlivekné.

  > [!NOTE]
  > Pokud spustíte projekt s výchozím webovým projektem ASP.NET Core v sadě Visual Studio nebo pokud `dotnet new mvc` použijete `AddAzureAD` metodu, je ve výchozím nastavení dostupná, protože související balíčky jsou automaticky načteny. Pokud však sestavíte projekt od začátku a pokoušíte se použít níže uvedený kód, doporučujeme vám přidat balíček NuGet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** do svého projektu, aby byla `AddAzureAD` metoda k dispozici.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft identity platform endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Microsoft identity platform endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Konfigurace ASP.NET

V ASP.NET je aplikace nakonfigurovaná prostřednictvím `Web.Config` souboru.

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

Kód související s ověřováním ve webové aplikaci ASP.NET nebo webové rozhraní API je umístěný v `App_Start/Startup.Auth.cs` souboru.

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](scenario-web-app-sign-user-sign-in.md)
