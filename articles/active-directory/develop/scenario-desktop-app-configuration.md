---
title: Konfigurace aplikací klasické pracovní plochy, které volají webová rozhraní API – platforma identit Microsoftu | Azure
description: Přečtěte si, jak nakonfigurovat kód desktopové aplikace, která volá webová rozhraní API.
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
ms.openlocfilehash: 2ba69e6447c686230412c33e74196c4bb594e0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611827"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Desktopová aplikace, která volá webová api: konfigurace kódu

Teď, když jste vytvořili aplikaci, se dozvíte, jak nakonfigurovat kód s souřadnicemi aplikace.

## <a name="microsoft-authentication-libraries"></a>Knihovny ověřování společnosti Microsoft

Následující knihovny ověřování (MSALs) společnosti Microsoft podporují desktopové aplikace.

  Identity a ověřování Microsoftu | Popis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podporuje vytváření desktopové aplikace na více platformách, jako je Linux, Windows a macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Podporuje vytváření desktopové aplikace na více platformách.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL v Javě | Podporuje vytváření desktopové aplikace na více platformách.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Podporuje desktopové aplikace, které běží pouze na macOS.

## <a name="public-client-application"></a>Veřejná klientská aplikace

Z hlediska kódu jsou desktopové aplikace veřejnými klientskými aplikacemi. Konfigurace bude trochu odlišná v závislosti na tom, zda používáte interaktivní ověřování nebo ne.

# <a name="net"></a>[.NET](#tab/dotnet)

Budete muset stavět a manipulovat `IPublicClientApplication`s MSAL.NET .

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Výhradně podle kódu

Následující kód inkonkaluje veřejnou klientskou aplikaci a přihlašuje uživatele ve veřejném cloudu Microsoft Azure pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Pokud máte v úmyslu použít interaktivní ověřování nebo tok `.WithRedirectUri` kódu zařízení, jak je vidět dříve, použijte modifikátor.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Použití konfiguračních souborů

Následující kód inkonaluje veřejnou klientskou aplikaci z konfiguračního objektu, který může být vyplněn programově nebo číst z konfiguračního souboru.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Propracovanější konfigurace

Sestavení aplikace můžete zpracovat přidáním několika modifikátorů. Například pokud chcete, aby vaše aplikace byla víceklientskou aplikací v národním cloudu, jako je například vláda USA zobrazená zde, můžete napsat:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET obsahuje také modifikátor služby AD FS 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Nakonec pokud chcete získat tokeny pro klienta B2C služby Azure Active Directory (Azure AD), zadejte svého klienta, jak je znázorněno v následujícím fragmentu kódu:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Další informace

Další informace o konfiguraci MSAL.NET desktopové aplikace:

- Seznam všech modifikátorů `PublicClientApplicationBuilder`dostupných na aplikaci naleznete v referenční dokumentaci [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Popis všech možností vystavených `PublicClientApplicationOptions`v aplikaci naleznete v tématu [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) v referenční dokumentaci.

### <a name="complete-example-with-configuration-options"></a>Kompletní příklad s možnostmi konfigurace

Představte si aplikaci konzoly `appsettings.json` .NET Core, která má následující konfigurační soubor:

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

Máte malý kód ke čtení v tomto souboru pomocí . Konfigurační rámec s čistým a čistým a čistým a finančním zajištěním:

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

Nyní, chcete-li vytvořit aplikaci, napište následující kód:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Před voláním `.Build()` metody můžete přepsat konfiguraci `.WithXXX` voláním metod, jak je vidět dříve.

# <a name="java"></a>[Java](#tab/java)

Zde je třída použitá ve vzorcích vývoje MSAL Java ke konfiguraci vzorků: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

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

# <a name="macos"></a>[Macos](#tab/macOS)

Následující kód inkonkaluje veřejnou klientskou aplikaci a přihlašuje uživatele ve veřejném cloudu Microsoft Azure pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.

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

### <a name="more-elaborated-configuration"></a>Propracovanější konfigurace

Sestavení aplikace můžete zpracovat přidáním několika modifikátorů. Například pokud chcete, aby vaše aplikace byla víceklientskou aplikací v národním cloudu, jako je například vláda USA zobrazená zde, můžete napsat:

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
