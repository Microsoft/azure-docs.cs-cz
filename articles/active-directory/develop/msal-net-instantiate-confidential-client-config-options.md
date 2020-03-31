---
title: Vytvoření instance důvěrné klientské aplikace (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vytvořit instanci důvěrné klientské aplikace s možnostmi konfigurace pomocí knihovny Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084731"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Vytvořte instanci důvěrné klientské aplikace s možnostmi konfigurace pomocí MSAL.NET

Tento článek popisuje, jak vytvořit instanci [důvěrné klientské aplikace](msal-client-applications.md) pomocí Knihovny ověřování Microsoft pro .NET (MSAL.NET).  Aplikace je vytvořena s možnostmi konfigurace definovanými v souboru nastavení.

Před inicializací aplikace je třeba ji [nejprve zaregistrovat,](quickstart-register-app.md) aby bylo možné aplikaci integrovat s platformou identit Microsoftu. Po registraci budete možná potřebovat následující informace (které najdete na webu Azure Portal):

- ID klienta (řetězec představující identifikátor GUID)
- Adresa URL poskytovatele identity (s názvem instance) a přihlašovací cílovou skupinu pro vaši aplikaci. Tyto dva parametry jsou souhrnně označovány jako autorita.
- ID klienta, pokud píšete řadu obchodních aplikací výhradně pro vaši organizaci (také s názvem aplikace s jedním tenantem).
- Tajný klíč aplikace (tajný řetězec klienta) nebo certifikát (typu X509Certificate2), pokud se jedná o důvěrnou klientskou aplikaci.
- Pro webové aplikace a někdy i pro aplikace veřejných klientů (zejména když vaše aplikace potřebuje používat zprostředkovatele), budete mít také nastavit redirectUri, kde poskytovatel identity bude kontaktovat zpět vaši aplikaci s tokeny zabezpečení.

## <a name="configure-the-application-from-the-config-file"></a>Konfigurace aplikace z konfiguračního souboru
Název vlastností možností v MSAL.NET se shoduje s názvem `AzureADOptions` vlastností v ASP.NET jádra, takže nemusíte psát žádný kód lepidla.

Konfigurace aplikace ASP.NET Core je popsána v souboru *appsettings.json:*

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Počínaje MSAL.NET v3.x, můžete nakonfigurovat důvěrné klientské aplikace z konfiguračního souboru.

Ve třídě, kde chcete konfigurovat a konstanci aplikace, `ConfidentialClientApplicationOptions` je třeba deklarovat objekt.  Svázat konfiguraci přečtenou ze zdroje (včetně souboru appconfig.json) s `IConfigurationRoot.Bind()` instancí možností aplikace pomocí metody z [balíčku Microsoft.Extensions.Configuration.Binder nuget](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

To umožňuje obsah "AzureAD" části *appsettings.json* souboru vázána na odpovídající `ConfidentialClientApplicationOptions` vlastnosti objektu.  Dále vytvořte `ConfidentialClientApplication` objekt:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Přidat konfiguraci runtime
V důvěrné klientské aplikaci obvykle máte mezipaměť na uživatele. Proto budete muset získat mezipaměti spojené s uživatelem a informovat tvůrce aplikací, který chcete použít. Stejným způsobem může mít dynamicky vypočítané přesměrování URI. V tomto případě je kód následující:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

