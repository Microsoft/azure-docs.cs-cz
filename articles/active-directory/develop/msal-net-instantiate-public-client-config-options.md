---
title: Vytvoření instance aplikace veřejným klientem s možnostmi (knihovna Microsoft Authentication Library pro .NET) | Azure
description: Zjistěte, jak vytvořit instanci aplikace veřejným klientem s možnostmi konfigurace pomocí knihovna Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 3a483cd6e22ce3c9fafb9b2b4d839e22c248f020
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158697"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Vytvoření instance aplikace veřejným klientem s možnostmi konfigurace pomocí MSAL.NET

Tento článek popisuje, jak vytvořit instanci [aplikace veřejným klientem](msal-client-applications.md) pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).  Aplikace je vytvořena instance s možnostmi konfigurace definované v souboru nastavení.

Před inicializací aplikace, musíte nejprve [zaregistrovat](quickstart-register-app.md) ho tak, aby vaše aplikace je možné integrovat s platformou identity Microsoft. Po registraci může potřebovat následující informace (který se nachází na webu Azure Portal):

- ID klienta (řetězec představující GUID)
- Adresa URL zprostředkovatele identity (s názvem instance) a skupinou přihlášení pro aplikaci. Tyto dva parametry jsou souhrnně označovány jako autorita.
- ID tenanta, pokud píšete řádek obchodní aplikace pouze pro vaši organizaci (také pojmenované jednoho tenanta aplikaci).
- Pro webové aplikace a někdy pro aplikace veřejným klientem (zejména když vaše aplikace potřebuje pomocí zprostředkovatele) budete mít také nastavíte redirectUri kde kontaktuje zprostředkovatele identity back aplikace s použitím tokenů zabezpečení.


Aplikace konzoly .NET Core může mít následující *appsettings.json* konfiguračního souboru:

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

Následující kód načte tento soubor pomocí rozhraní .NET framework konfigurace:

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

Následující kód vytvoří aplikace pomocí konfigurace ze souboru nastavení:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

