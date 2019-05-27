---
title: Aplikace klasické pracovní plochy, že volání webových rozhraní API (kód konfigurace) – platforma identit Microsoft
description: Zjistěte, jak vytvářet aplikace klasické pracovní plochy, že volání webových rozhraní API (konfigurace kódu aplikace)
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
ms.openlocfilehash: bc0042d6392891e8282c563afea2212031a0f49a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121886"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Aplikace klasické pracovní plochy, že volání webových rozhraní API – konfigurace kódu

Teď, když jste vytvořili aplikaci, budete se dozvíte, jak nakonfigurovat kód se souřadnicemi vaší aplikace.

## <a name="msal-libraries"></a>Knihovna MSAL knihovny

Pouze knihovna MSAL dnes podpora aplikací klasické pracovní plochy je MSAL.NET

## <a name="public-client-application"></a>Veřejné klientské aplikace

Z kódu hlediska, aplikací klasické pracovní plochy jsou veřejné klientské aplikace, a proto budete moct sestavit a manipulaci s MSAL.NET `IPublicClientApplication`. Znovu věci bude trochu liší, zda používáte interaktivní ověřování nebo ne.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Výhradně pomocí kódu

Následující kód vytvoří instanci aplikace veřejným klientem přihlašování uživatelů ve veřejném cloudu Microsoft Azure, s pracovní a školní účet nebo osobní účet Microsoft.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Pokud máte v úmyslu používat interaktivní ověřování, jak je znázorněno výše, kterou chcete použít `.WithRedirectUri` modifikátor:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .Build();
```

### <a name="using-configuration-files"></a>Pomocí konfiguračních souborů

Následující kód vytvoří instanci aplikace veřejným klientem z objekt konfigurace, které by mohly být vyplněné prostřednictvím kódu programu nebo číst z konfiguračního souboru

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .Build();
```

### <a name="more-elaborated-configuration"></a>Více elaborované konfigurace

Můžete efektivitě aplikace sestavení tak, že přidáte počet parametrů. Například pokud chcete, aby vaše aplikace bude aplikace s více tenanty v národních cloudů (zde pro státní správu USA), můžete napsat:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET také obsahuje Modifikátor pro AD FS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Nakonec pokud chcete získat tokeny pro tenanta služby Azure AD B2C, můžete zadat vašeho tenanta, jak je znázorněno v následujícím fragmentu kódu:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Další informace

Další informace o tom, jak nakonfigurovat aplikaci klasické pracovní plochy MSAL.NET:

- Seznam všech parametrů k dispozici na `PublicClientApplicationBuilder`, naleznete v referenční dokumentaci [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Popis všech možností v `PublicClientApplicationOptions` naleznete v tématu [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), v referenční dokumentaci

## <a name="complete-example-with-configuration-options"></a>Kompletní příklad s možností konfigurace

Představte si konzolovou aplikaci .NET Core, který má následující `appsettings.json` konfiguračního souboru:

```JSon
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

Máte malý kód ke čtení tohoto souboru pomocí rozhraní .NET framework konfigurace; k dispozici

```CSharp
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
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Nyní jak vytvořit aplikaci, stejně musíte napsat následující kód:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
           .Build();
```

a před volání `.Build()` metodu, můžete přepsat konfiguraci s voláními `.WithXXX` metod, jak je vidět dříve.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získání tokenu pro desktopové aplikace](scenario-desktop-acquire-token.md)
