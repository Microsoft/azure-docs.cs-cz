---
title: Vytvoření instance aplikace důvěrného klienta (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet instance důvěrných klientských aplikací s možnostmi konfigurace pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084731"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Vytvoření instance aplikace důvěrného klienta s možnostmi konfigurace pomocí MSAL.NET

Tento článek popisuje, jak vytvořit instanci [důvěrné klientské aplikace](msal-client-applications.md) pomocí knihovny Microsoft Authentication Library pro .net (MSAL.NET).  Instance aplikace je vytvořena s možnostmi konfigurace definovanými v souboru nastavení.

Před inicializací aplikace je nejprve nutné ji [zaregistrovat](quickstart-register-app.md) , aby bylo možné aplikaci integrovat s platformou Microsoft identity. Po registraci možná budete potřebovat následující informace (které najdete v Azure Portal):

- ID klienta (řetězec představující GUID)
- Adresa URL zprostředkovatele identity (pojmenovaná instance) a cílová skupina pro přihlášení k vaší aplikaci. Tyto dva parametry jsou souhrnně známé jako autorita.
- ID tenanta, pokud píšete obchodní aplikaci výhradně pro vaši organizaci (nazývá se jenom jediná aplikace tenanta).
- Tajný klíč aplikace (tajný řetězec klienta) nebo certifikát (typu X509Certificate2), pokud se jedná o důvěrnou klientskou aplikaci.
- Pro webové aplikace a někdy pro veřejné klientské aplikace (zejména v případě, že vaše aplikace potřebuje použít zprostředkovatele) nastavíte také redirectUri, kde bude poskytovatel identity kontaktovat zpět vaší aplikaci pomocí tokenů zabezpečení.

## <a name="configure-the-application-from-the-config-file"></a>Konfigurace aplikace z konfiguračního souboru
Název vlastností možností v MSAL.NET se shoduje s názvem vlastností `AzureADOptions` v ASP.NET Core, takže nemusíte psát žádný spojovací kód.

Konfigurace aplikace ASP.NET Core je popsána v souboru *appSettings. JSON* :

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

Od MSAL.NET v3. x můžete svoji důvěrnou klientskou aplikaci nakonfigurovat z konfiguračního souboru.

Ve třídě, kde chcete konfigurovat a vytvářet instance aplikace, je nutné deklarovat objekt `ConfidentialClientApplicationOptions`.  Navažte konfiguraci načtenou ze zdroje (včetně souboru appconfig. JSON) do instance možností aplikace pomocí metody `IConfigurationRoot.Bind()` z [balíčku NuGet Microsoft. Extensions. Configuration. Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

To umožňuje svázat obsah oddílu "AzureAD" souboru *appSettings. JSON* s odpovídajícími vlastnostmi objektu `ConfidentialClientApplicationOptions`.  Dále Sestavte objekt `ConfidentialClientApplication`:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Přidat konfiguraci modulu runtime
V důvěrné klientské aplikaci máte obvykle mezipaměť na uživatele. Proto budete muset získat mezipaměť přidruženou k uživateli a informovat tvůrce aplikace, že ho chcete použít. Stejným způsobem může být k dispozici dynamicky vypočítaný identifikátor URI přesměrování. V tomto případě kód je následující:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

