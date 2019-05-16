---
title: Vytvoření instance důvěrnému klientovi aplikace s možnostmi (knihovna Microsoft Authentication Library pro .NET) | Azure
description: Zjistěte, jak vytvořit instanci důvěrné klientské aplikace s možnostmi konfigurace pomocí knihovna Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f935b1b2815501710444e3f921a157ba02e3215
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544085"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Vytvoření instance důvěrné klientské aplikace s možnostmi konfigurace pomocí MSAL.NET

Tento článek popisuje, jak vytvořit instanci [důvěrnému klientovi aplikace](msal-client-applications.md) pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).  Aplikace je vytvořena instance s možnostmi konfigurace definované v souboru nastavení.

Před inicializací aplikace, musíte nejprve [zaregistrovat](quickstart-register-app.md) ho tak, aby vaše aplikace je možné integrovat s platformou identity Microsoft. Po registraci může potřebovat následující informace (který se nachází na webu Azure Portal):

- ID klienta (řetězec představující GUID)
- Adresa URL zprostředkovatele identity (s názvem instance) a skupinou přihlášení pro aplikaci. Tyto dva parametry jsou souhrnně označovány jako autorita.
- ID tenanta, pokud píšete řádek obchodní aplikace pouze pro vaši organizaci (také pojmenované jednoho tenanta aplikaci).
- Tajný klíč aplikace (tajného kódu klienta string) nebo certifikátu (typu X509Certificate2) Pokud je aplikace důvěrnému klientovi.
- Pro webové aplikace a někdy pro aplikace veřejným klientem (zejména když vaše aplikace potřebuje pomocí zprostředkovatele) budete mít také nastavíte redirectUri kde kontaktuje zprostředkovatele identity back aplikace s použitím tokenů zabezpečení.

## <a name="configure-the-application-from-the-config-file"></a>Konfigurace aplikace z konfiguračního souboru
Název vlastnosti z možností v MSAL.NET shodovat s názvem vlastnosti `AzureADOptions` v ASP.NET Core, takže není nutné psaní spojovacího kódu.

Konfigurace aplikace ASP.NET Core je popsaná v *appsettings.json* souboru:

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

Počínaje MSAL.NET v3.x, můžete nakonfigurovat důvěrné klientské aplikace ze souboru config. Třídy související s konfigurací aplikace jsou umístěné v `Microsoft.Identity.Client.AppConfig` oboru názvů.

Ve třídě, ve které chcete nakonfigurovat a vytvoření instance aplikace, je třeba deklarovat `ConfidentialClientApplicationOptions` objektu.  Vázání konfigurace čtení ze zdrojové (včetně souboru appconfig.json) na instanci aplikace možnosti:

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

To umožňuje obsah v oddílu "Azure AD" *appsettings.json* soubor vázaný na odpovídající vlastnosti `ConfidentialClientApplicationOptions` objektu.  V dalším kroku sestavení `ConfidentialClientApplication` objektu:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Přidání konfigurace modulu runtime
V aplikaci důvěrnému klientovi mají obvykle mezipaměť na uživatele. Proto je potřeba získat mezipaměti přidružené k uživateli a informovat Tvůrce aplikací, že chcete použít. Stejným způsobem pravděpodobně na dynamicky vypočtená identifikátor URI přesměrování. V tomto případě je následující kód:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

