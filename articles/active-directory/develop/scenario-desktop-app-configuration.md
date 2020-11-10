---
title: Konfigurace desktopových aplikací, které volají webová rozhraní API – Microsoft Identity Platform | Azure
description: Naučte se konfigurovat kód desktopové aplikace, která volá webová rozhraní API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 27c9f2ab8b02c96d016fea66acd2b3840d79d09d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444117"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Aplikace klasické pracovní plochy, která volá webová rozhraní API: Konfigurace kódu

Teď, když jste vytvořili aplikaci, se dozvíte, jak nakonfigurovat kód pomocí souřadnic aplikace.

## <a name="microsoft-authentication-libraries"></a>Knihovny Microsoft Authentication Library

Následující knihovny Microsoft Authentication Library (MSALs) podporují desktopové aplikace.

  Identity a ověřování Microsoftu | Popis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podporuje vytváření desktopových aplikací na různých platformách, jako jsou Linux, Windows a macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Podporuje vytváření desktopových aplikací na různých platformách.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL v Javě | Podporuje vytváření desktopových aplikací na různých platformách.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Podporuje aplikace klasické pracovní plochy, které běží pouze na macOS.

## <a name="public-client-application"></a>Veřejná klientská aplikace

Z hlediska kódu jsou desktopové aplikace veřejné klientské aplikace. Konfigurace se trochu liší v závislosti na tom, zda používáte interaktivní ověřování, nebo ne.

# <a name="net"></a>[.NET](#tab/dotnet)

Budete muset sestavit a manipulovat s MSAL.NET `IPublicClientApplication` .

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Výhradně pomocí kódu

Následující kód vytvoří instanci veřejné klientské aplikace a přihlásí uživatele ve veřejném cloudu Microsoft Azure pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Pokud máte v úmyslu používat interaktivní ověřování nebo tok kódu zařízení, jak bylo vidět dříve, použijte `.WithRedirectUri` modifikátor.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Použití konfiguračních souborů

Následující kód vytvoří instanci veřejné klientské aplikace z konfiguračního objektu, který může být vyplněn programově nebo načten z konfiguračního souboru.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Podrobněji vypracované konfigurace

Sestavování aplikace můžete vymezit přidáním několika modifikátorů. Například pokud chcete, aby vaše aplikace byla víceklientské aplikace v národním cloudu, jako je zde uvedená státní správa USA, mohli byste napsat:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET také obsahuje modifikátor pro Active Directory Federation Services (AD FS) 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Nakonec, pokud chcete získat tokeny pro klienta Azure Active Directory (Azure AD) B2C, zadejte svého tenanta, jak je znázorněno v následujícím fragmentu kódu:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Další informace

Další informace o tom, jak nakonfigurovat desktopovou aplikaci MSAL.NET:

- Seznam všech modifikátorů, které jsou k dispozici v systému `PublicClientApplicationBuilder` , naleznete v referenční dokumentaci [PublicClientApplicationBuilder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Popis všech možností zveřejněných v nástroji naleznete v `PublicClientApplicationOptions` tématu [PublicClientApplicationOptions](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) v referenční dokumentaci.

### <a name="complete-example-with-configuration-options"></a>Kompletní příklad s možnostmi konfigurace

Představte si konzolovou aplikaci .NET Core, která má následující `appsettings.json` konfigurační soubor:

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

Máte malý kód pro čtení v tomto souboru pomocí. Rozhraní pro konfiguraci zadané příkazem NET:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
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

  // Read the auth and graph endpoint configuration
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

Nyní vytvořte aplikaci zadáním následujícího kódu:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Před voláním `.Build()` metody můžete svou konfiguraci přepsat voláním `.WithXXX` metod, jak je vidět dříve.

# <a name="java"></a>[Java](#tab/java)

Tady je třída, která se používá v ukázkách vývoje MSAL Java ke konfiguraci ukázek: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[MacOS](#tab/macOS)

Následující kód vytvoří instanci veřejné klientské aplikace a přihlásí uživatele ve veřejném cloudu Microsoft Azure pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.

### <a name="quick-configuration"></a>Rychlá konfigurace

Cíl-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

SWIFT
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Podrobněji vypracované konfigurace

Sestavování aplikace můžete vymezit přidáním několika modifikátorů. Například pokud chcete, aby vaše aplikace byla víceklientské aplikace v národním cloudu, jako je zde uvedená státní správa USA, mohli byste napsat:

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

SWIFT

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři a [Získejte token pro desktopovou aplikaci](scenario-desktop-acquire-token.md).