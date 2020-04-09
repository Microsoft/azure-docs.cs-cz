---
title: Použití identifikátorů URI přesměrování s nástrojem MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi knihovnou ověřování Microsoftu pro ObjectiveC (MSAL pro iOS a macOS) a Knihovnou ověřování Azure AD pro objectivec (ADAL. ObjC) a jak mezi nimi migrovat.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 1291563a39e3cf3acd4b343302be8b150bf794ca
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883504"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Použití identifikátorů URI přesměrování s ověřovací knihovnou Microsoftu pro iOS a macOS

Když se uživatel ověří, Azure Active Directory (Azure AD) odešle token do aplikace pomocí identifikátoru URI přesměrování registrovaného v aplikaci Azure AD.

Knihovna Microsoft Authentication (MSAL) vyžaduje, aby identifikátor URI přesměrování byl registrován v aplikaci Azure AD v určitém formátu. MSAL používá výchozí identifikátor URI přesměrování, pokud jej nezadáte. Formát je `msauth.[Your_Bundle_Id]://auth`.

Výchozí formát IDENTIFIKÁTORU URI přesměrování funguje pro většinu aplikací a scénářů, včetně zprostředkovaného ověřování a webového zobrazení systému. Pokud je to možné, použijte výchozí formát.

Však může být nutné změnit přesměrování URI pro pokročilé scénáře, jak je popsáno níže.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Scénáře, které vyžadují jiný identifikátor URI přesměrování

### <a name="cross-app-single-sign-on-sso"></a>Jednotné přihlášení mezi aplikacemi (Jednotné přihlašování)

Aby mohla platforma Microsoft Identity sdílet tokeny mezi aplikacemi, musí mít každá aplikace stejné ID klienta nebo ID aplikace. Jedná se o jedinečný identifikátor poskytnutý při registraci aplikace na portálu (nikoli ID sady aplikací, které registrujete na aplikaci u společnosti Apple).

Identifikátory URI přesměrování musí být pro každou aplikaci pro iOS odlišné. To umožňuje službě identit společnosti Microsoft jednoznačně identifikovat různé aplikace, které sdílejí ID aplikace. Každá aplikace může mít více identifikátorů URI přesměrování registrovaných na webu Azure Portal. Každá aplikace ve vaší sadě bude mít jiný identifikátor URI přesměrování. Příklad:

Vzhledem k následující registraci aplikace na webu Azure Portal:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 používá `msauth.com.contoso.app1://auth` přesměrování App2 používá `msauth.com.contoso.app2://auth` App3 používá`msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Migrace z ADAL do MSAL

Při migraci kódu, který používá Azure AD Ověřování library (ADAL) na MSAL, můžete již mít přesměrování URI nakonfigurované pro vaši aplikaci. Můžete pokračovat v používání stejného identifikátoru URI přesměrování, dokud byla aplikace ADAL nakonfigurována tak, aby podporovala zprostředkované scénáře a identifikátor URI přesměrování splňuje požadavky na formát URI přesměrování MSAL.

## <a name="msal-redirect-uri-format-requirements"></a>Požadavky na formát identifikátoru URI přesměrování služby MSAL

* Identifikátor URI přesměrování MSAL musí být ve formuláři.`<scheme>://host`

    Kde `<scheme>` je jedinečný řetězec, který identifikuje vaši aplikaci. Je primárně založen na identifikátoru sady vaší aplikace, aby byla zaručena jedinečnost. Pokud je `com.contoso.myapp`například ID sady aplikace , identifikátor URI přesměrování bude `msauth.com.contoso.myapp://auth`ve formuláři: .

    Pokud migrujete z ADAL, identifikátor URI přesměrování bude `<scheme>://[Your_Bundle_Id]`mít `scheme` pravděpodobně tento formát: , kde je jedinečný řetězec. Tento formát bude i nadále fungovat při použití MSAL.

* `<scheme>`musí být zaregistrovány v seznamu Info.plist vaší aplikace pod . `CFBundleURLTypes > CFBundleURLSchemes`  V tomto příkladu byl soubor Info.plist otevřen jako zdrojový kód:

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
    

MSAL ověří, zda se identifikátor URI přesměrování registruje správně, a vrátí chybu, pokud není.
    
* Pokud chcete použít univerzální odkazy jako identifikátor `<scheme>` URI `https` přesměrování, musí být a `CFBundleURLSchemes`nemusí být deklarován v . Místo toho nakonfigurujte aplikaci a doménu podle `handleMSALResponse:sourceApplication:` pokynů `MSALPublicClientApplication` společnosti Apple na [univerzálních odkazech pro vývojáře](https://developer.apple.com/ios/universal-links/) a zavolejte metodu, kdy je vaše aplikace otevřena prostřednictvím univerzálního odkazu.

## <a name="use-a-custom-redirect-uri"></a>Použití vlastního identifikátoru URI přesměrování

Chcete-li použít vlastní identifikátor `redirectUri` URI `MSALPublicClientApplicationConfig` přesměrování, předajte parametr a předajte mu tento objekt při `MSALPublicClientApplication` inicializaci objektu. Pokud je identifikátor URI přesměrování neplatný, `nil` inicializátor se vrátí a nastaví `redirectURIError`další informace.  Příklad:

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

Swift:

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



## <a name="handle-the-url-opened-event"></a>Zpracování události otevření adresy URL

Aplikace by měla volat MSAL, když obdrží jakoukoli odpověď prostřednictvím schémat URL nebo univerzální odkazy. Volání `handleMSALResponse:sourceApplication:` metody `MSALPublicClientApplication` při otevření aplikace. Tady je příklad vlastních schémat:

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

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)
