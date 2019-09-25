---
title: Aplikace klasické pracovní plochy, která volá webová rozhraní API (konfigurace kódu) – platforma Microsoftu identity
description: Zjistěte, jak vytvořit desktopovou aplikaci, která volá webová rozhraní API (konfigurace kódu aplikace).
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
ms.openlocfilehash: 0926e6800dbcd81d2e542e27afe3afb1240cff22
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268420"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Aplikace klasické pracovní plochy, která volá webové rozhraní API – konfigurace kódu

Teď, když jste vytvořili aplikaci, se dozvíte, jak nakonfigurovat kód pomocí souřadnic aplikace.

## <a name="msal-libraries"></a>Knihovny MSAL

Jediná knihovna MSAL podporující desktopové aplikace na více platformách je dnes MSAL.NET.

MSAL pro iOS a macOS podporuje desktopové aplikace běžící jenom na macOS. 

## <a name="public-client-application-with-msalnet"></a>Veřejná klientská aplikace s MSAL.NET

Z hlediska kódu jsou desktopové aplikace veřejné klientské aplikace a proto je vhodné sestavovat a manipulovat s MSAL.NET `IPublicClientApplication`. Nové věci budou trochu odlišné, ať už používáte interaktivní ověřování, nebo ne.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Výhradně pomocí kódu

Následující kód vytvoří instanci veřejné klientské aplikace, přihlašuje uživatele ve veřejném cloudu Microsoft Azure, pomocí pracovního a školního účtu nebo osobního účet Microsoft.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Pokud máte v úmyslu používat interaktivní ověřování nebo tok kódu zařízení, jak vidíte výše, chcete použít `.WithRedirectUri` modifikátor:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Použití konfiguračních souborů

Následující kód vytvoří instanci veřejné klientské aplikace z konfiguračního objektu, který může být vyplněn programově nebo načten z konfiguračního souboru.

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Podrobněji vypracované konfigurace

Sestavování aplikace můžete vymezit přidáním několika modifikátorů. Například pokud chcete, aby vaše aplikace byla víceklientské aplikace v národním cloudu (zde USA – státní správa), můžete napsat:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET obsahuje taky modifikátor pro ADFS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Nakonec, pokud chcete získat tokeny pro klienta Azure AD B2C, můžete určit vašeho tenanta, jak je znázorněno v následujícím fragmentu kódu:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Víc se uč

Další informace o tom, jak nakonfigurovat desktopovou aplikaci MSAL.NET:

- Seznam všech modifikátorů dostupných na `PublicClientApplicationBuilder`najdete v referenční dokumentaci [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) .
- Popis všech možností vystavených v `PublicClientApplicationOptions` tématu [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)najdete v referenční dokumentaci.

## <a name="complete-example-with-configuration-options"></a>Kompletní příklad s možnostmi konfigurace

Představte si konzolovou aplikaci .NET Core, `appsettings.json` která má následující konfigurační soubor:

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

Máte malý kód pro čtení tohoto souboru pomocí rozhraní .NET Framework pro konfiguraci, které je k dispozici.

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

Nyní budete muset vytvořit aplikaci, stačí napsat následující kód:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

a před voláním `.Build()` metody můžete přepsat vaši konfiguraci `.WithXXX` voláním metod, jak se zobrazily dříve.

## <a name="public-client-application-with-msal-for-ios-and-macos"></a>Veřejná klientská aplikace s MSAL pro iOS a macOS

Následující kód vytvoří instanci veřejné klientské aplikace, přihlašuje uživatele ve veřejném cloudu Microsoft Azure, pomocí pracovního a školního účtu nebo osobního účet Microsoft.

### <a name="quick-configuration"></a>Rychlá konfigurace

Cíl-C:

```objc
NSError *msalError = nil;
    
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Podrobněji vypracované konfigurace

Sestavování aplikace můžete vymezit přidáním několika modifikátorů. Například pokud chcete, aby vaše aplikace byla víceklientské aplikace v národním cloudu (zde USA – státní správa), můžete napsat:

Cíl-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získání tokenu pro desktopovou aplikaci](scenario-desktop-acquire-token.md)
