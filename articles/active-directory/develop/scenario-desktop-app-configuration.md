---
title: Konfigurace desktopových aplikací, které volají webová rozhraní API – Microsoft Identity Platform | Azure
description: Naučte se konfigurovat kód desktopové aplikace, která volá webová rozhraní API.
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1724c25ef2d85aa8f838811864104b49576a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423900"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Aplikace klasické pracovní plochy, která volá webové rozhraní API – konfigurace kódu

Teď, když jste vytvořili aplikaci, se dozvíte, jak nakonfigurovat kód pomocí souřadnic aplikace.

## <a name="msal-libraries"></a>Knihovny MSAL

Knihovny Microsoftu podporující desktopové aplikace jsou:

  Knihovna MSAL | Popis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podporuje vytváření desktopových aplikací na různých platformách – Linux, Windows a MacOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Podporuje vytváření desktopových aplikací na různých platformách.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL v Javě | Podporuje vytváření desktopových aplikací na různých platformách.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Podporuje jenom desktopové aplikace běžící jenom na macOS.

## <a name="public-client-application"></a>Veřejná klientská aplikace

Z hlediska kódu jsou desktopové aplikace veřejné klientské aplikace. Konfigurace se trochu liší v závislosti na tom, zda používáte interaktivní ověřování, nebo ne.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Budete muset sestavit a manipulovat s `IPublicClientApplication`MSAL.NET.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Výhradně pomocí kódu

Následující kód vytvoří instanci veřejné klientské aplikace, přihlašuje uživatele ve veřejném cloudu Microsoft Azure, pomocí pracovního a školního účtu nebo osobního účet Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Pokud máte v úmyslu používat interaktivní ověřování nebo tok kódu zařízení, jak vidíte výše, chcete použít modifikátor `.WithRedirectUri`:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Použití konfiguračních souborů

Následující kód vytvoří instanci veřejné klientské aplikace z konfiguračního objektu, který může být vyplněn programově nebo načten z konfiguračního souboru.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Podrobněji vypracované konfigurace

Sestavování aplikace můžete vymezit přidáním několika modifikátorů. Například pokud chcete, aby vaše aplikace byla víceklientské aplikace v národním cloudu (zde USA – státní správa), můžete napsat:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET obsahuje taky modifikátor pro ADFS 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Nakonec, pokud chcete získat tokeny pro klienta Azure AD B2C, můžete určit vašeho tenanta, jak je znázorněno v následujícím fragmentu kódu:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Další informace

Další informace o tom, jak nakonfigurovat desktopovou aplikaci MSAL.NET:

- Seznam všech modifikátorů dostupných v `PublicClientApplicationBuilder`najdete v referenční dokumentaci [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) .
- Popis všech možností vystavených v `PublicClientApplicationOptions` naleznete v tématu [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)v referenční dokumentaci.

### <a name="complete-example-with-configuration-options"></a>Kompletní příklad s možnostmi konfigurace

Představte si konzolovou aplikaci .NET Core, která má následující konfigurační soubor `appsettings.json`:

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
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Nyní budete muset vytvořit aplikaci, stačí napsat následující kód:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

a před voláním metody `.Build()` můžete přepsat vaši konfiguraci voláním `.WithXXX` metod, jak se zobrazily dříve.

# <a name="javatabjava"></a>[Java](#tab/java)

Tady je třída, která se používá v MSAL ukázek Java ke konfiguraci ukázek: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

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
---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získání tokenu pro desktopovou aplikaci](scenario-desktop-acquire-token.md)
