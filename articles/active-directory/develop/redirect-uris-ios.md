---
title: Použití identifikátorů URI pro přesměrování s MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi knihovnou Microsoft Authentication Library for ObjectiveC (MSAL for iOS and macOS) a s knihovnou ověřování Azure AD pro ObjectiveC (ADAL. ObjC) a postup migrace mezi nimi.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 2c728854846a3add1f5b41cb318d7dc5fd86e742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064194"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Použití identifikátorů URI pro přesměrování s knihovnou Microsoft Authentication Library pro iOS a macOS

Když se uživatel ověří, Azure Active Directory (Azure AD) pošle token do aplikace pomocí identifikátoru URI přesměrování zaregistrovaného v aplikaci Azure AD.

Knihovna Microsoft Authentication Library (MSAL) vyžaduje, aby identifikátor URI přesměrování byl zaregistrován v aplikaci Azure AD v konkrétním formátu. MSAL používá výchozí identifikátor URI přesměrování, pokud ho nezadáte. Formát je `msauth.[Your_Bundle_Id]://auth` .

Výchozí formát identifikátoru URI pro přesměrování funguje pro většinu aplikací a scénářů, jako je například zprostředkované ověřování a systémové webové zobrazení. Pokud je to možné, použijte výchozí formát.

V případě pokročilých scénářů ale možná budete muset změnit identifikátor URI přesměrování, jak je popsáno níže.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Scénáře, které vyžadují jiný identifikátor URI pro přesměrování

### <a name="cross-app-single-sign-on-sso"></a>Jednotné přihlašování mezi aplikacemi (SSO)

Aby mohla platforma Microsoft Identity sdílet tokeny napříč aplikacemi, musí mít každá aplikace stejné ID klienta nebo ID aplikace. Toto je jedinečný identifikátor, který jste zadali při registraci aplikace na portálu (ne ID sady prostředků aplikace, které zaregistrujete na aplikaci pomocí Applu).

Identifikátory URI pro přesměrování musí být pro každou aplikaci pro iOS odlišné. To umožňuje službě Microsoft Identity jednoznačně identifikovat různé aplikace, které sdílejí ID aplikace. Každá aplikace může mít v Azure Portal zaregistrováno více identifikátorů URI pro přesměrování. Každá aplikace v sadě bude mít jiný identifikátor URI pro přesměrování. Například:

Při následující registraci aplikace v Azure Portal:

* ID klienta: `ABCDE-12345` (Toto je jediné ID klienta)
* RedirectUris: `msauth.com.contoso.app1://auth` , `msauth.com.contoso.app2://auth` , `msauth.com.contoso.app3://auth`

App1 používá přesměrování `msauth.com.contoso.app1://auth` . \
App2 používá `msauth.com.contoso.app2://auth` . \
APP3 používá `msauth.com.contoso.app1://auth` .

### <a name="migrating-from-adal-to-msal"></a>Migrace z knihovny ADAL na MSAL

Při migraci kódu, který používal knihovnu ověřování Azure AD (ADAL) na MSAL, už možná máte pro vaši aplikaci nakonfigurovanou identifikátor URI pro přesměrování. Stejný identifikátor URI pro přesměrování můžete dál používat, pokud byla aplikace ADAL nakonfigurovaná tak, aby podporovala zprostředkované scénáře, a váš identifikátor URI přesměrování splňuje požadavky formátu URI MSAL přesměrování.

## <a name="msal-redirect-uri-format-requirements"></a>Požadavky formátu identifikátoru URI pro přesměrování MSAL

* Identifikátor URI pro přesměrování MSAL musí být ve formátu. `<scheme>://host`

    Kde `<scheme>` je jedinečný řetězec, který identifikuje vaši aplikaci. Primárně se vychází z identifikátoru sady prostředků vaší aplikace, aby se zaručila jedinečnost. Pokud má vaše aplikace například ID sady `com.contoso.myapp` , váš identifikátor URI přesměrování by měl být ve formátu: `msauth.com.contoso.myapp://auth` .

    Pokud migrujete z knihovny ADAL, váš identifikátor URI pro přesměrování bude pravděpodobně mít tento formát: `<scheme>://[Your_Bundle_Id]` , kde `scheme` je jedinečný řetězec. Tento formát bude fungovat i v případě, že použijete MSAL.

* `<scheme>` musí se zaregistrovat v souboru info. plist vaší aplikace v části `CFBundleURLTypes > CFBundleURLSchemes` .  V tomto příkladu byl otevřen info. plist jako zdrojový kód:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```

MSAL ověří, zda identifikátor URI přesměrování správně zaregistruje, a vrátí chybu, pokud není.
    
* Pokud chcete použít univerzální odkazy jako identifikátor URI přesměrování, `<scheme>` musí být `https` a nemusí být deklarován v `CFBundleURLSchemes` . Místo toho nakonfigurujte informace o aplikaci a doméně na společnosti Apple na stránce [Universal Links pro vývojáře](https://developer.apple.com/ios/universal-links/) a zavolejte metodu pro, `handleMSALResponse:sourceApplication:` `MSALPublicClientApplication` když je vaše aplikace otevírána prostřednictvím univerzálního odkazu.

## <a name="use-a-custom-redirect-uri"></a>Použít vlastní identifikátor URI pro přesměrování

Chcete-li použít vlastní identifikátor URI přesměrování, předejte `redirectUri` parametr do `MSALPublicClientApplicationConfig` a předejte tomuto objektu `MSALPublicClientApplication` při inicializaci objektu. Pokud je identifikátor URI přesměrování neplatný, inicializátor vrátí `nil` a nastaví `redirectURIError` Další informace.  Například:

Cíl-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

SWIFT

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Zpracovat událost otevření adresy URL

Vaše aplikace by měla volat MSAL, když přijme jakoukoli odpověď prostřednictvím schémat adres URL nebo univerzálních odkazů. `handleMSALResponse:sourceApplication:` `MSALPublicClientApplication` Při otevření aplikace zavolejte metodu. Tady je příklad pro vlastní schémata:

Cíl-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

SWIFT

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)
