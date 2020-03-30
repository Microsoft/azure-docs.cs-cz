---
title: Vytvoření instance veřejné klientské aplikace (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vytvořit instanci veřejné klientské aplikace s možnostmi konfigurace pomocí knihovny Microsoft Authentication Library for .NET (MSAL.NET).
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
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083594"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Vytvoření instance aplikace veřejného klienta s možnostmi konfigurace pomocí MSAL.NET

Tento článek popisuje, jak vytvořit instanci [veřejné klientské aplikace](msal-client-applications.md) pomocí Knihovny ověřování Microsoft pro .NET (MSAL.NET).  Aplikace je vytvořena s možnostmi konfigurace definovanými v souboru nastavení.

Před inicializací aplikace je třeba ji [nejprve zaregistrovat,](quickstart-register-app.md) aby bylo možné aplikaci integrovat s platformou identit Microsoftu. Po registraci budete možná potřebovat následující informace (které najdete na webu Azure Portal):

- ID klienta (řetězec představující identifikátor GUID)
- Adresa URL poskytovatele identity (s názvem instance) a přihlašovací cílovou skupinu pro vaši aplikaci. Tyto dva parametry jsou souhrnně označovány jako autorita.
- ID klienta, pokud píšete řadu obchodních aplikací výhradně pro vaši organizaci (také s názvem aplikace s jedním tenantem).
- Pro webové aplikace a někdy i pro aplikace veřejných klientů (zejména když vaše aplikace potřebuje používat zprostředkovatele), budete mít také nastavit redirectUri, kde poskytovatel identity bude kontaktovat zpět vaši aplikaci s tokeny zabezpečení.


Konzolová aplikace .NET Core může mít následující konfigurační soubor *appsettings.json:*

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Následující kód přečte tento soubor pomocí konfiguračního rozhraní .NET:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Následující kód vytvoří aplikaci pomocí konfigurace ze souboru nastavení:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

