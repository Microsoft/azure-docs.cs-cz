---
title: Konfigurace jednotného přihlašování v macOS a iOS
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování (SSO) na macOS a iOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "80881244"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Postupy: Konfigurace jednotného přihlašování v macOS a iOS

Knihovna Microsoft Authentication Library (MSAL) pro macOS a iOS podporuje jednotné přihlašování (SSO) mezi aplikacemi pro macOS/iOS a prohlížeči. Tento článek se zabývá následujícími scénáři jednotného přihlašování:

- [Tiché jednotné přihlašování mezi několika aplikacemi](#silent-sso-between-apps)

Tento typ jednotného přihlašování funguje mezi několika aplikacemi distribuovanými stejným vývojářem Apple. Poskytuje bezobslužné přihlášení (to znamená, že uživatel není vyzván k zadání přihlašovacích údajů) tím, že si přečte aktualizační tokeny napsané jinými aplikacemi z řetězce klíčů a vyměňuje je pro přístupové tokeny v tichém režimu.  

- [Jednotné přihlašování prostřednictvím zprostředkovatele ověřování](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Tento tok není v macOS k dispozici.

Společnost Microsoft poskytuje aplikace označované jako zprostředkovatelé, které umožňují jednotné přihlašování mezi aplikacemi od různých dodavatelů, pokud je mobilní zařízení zaregistrované ve službě Azure Active Directory (AAD). Tento typ jednotného přihlašování vyžaduje, aby se na zařízení uživatele nainstalovala aplikace zprostředkovatele.

- **Jednotné přihlašování mezi MSAL a Safari**

Jednotné přihlašování se dosahuje prostřednictvím třídy [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) . Používá existující přihlašovací stav z jiných aplikací a prohlížeče Safari. Není omezený na aplikace distribuované stejným vývojářem Apple, ale vyžaduje nějakou interakci s uživatelem.

Pokud k přihlášení uživatelů použijete výchozí webové zobrazení v aplikaci, získáte automatické jednotné přihlašování mezi MSAL aplikacemi a Safari. Další informace o webových zobrazeních, která podporuje MSAL, najdete v části [přizpůsobení prohlížečů a webových zobrazení](customize-webviews.md).

> [!IMPORTANT]
> Tento typ jednotného přihlašování není v macOS aktuálně k dispozici. MSAL on macOS podporuje jenom WKWebView, které nemají podporu jednotného přihlašování s Safari. 

- **Tiché jednotné přihlašování mezi aplikacemi ADAL a MSAL macOS/iOS**

MSAL cíl-C podporuje migraci a jednotné přihlašování pomocí aplikací založených na knihovně ADAL pro aplikace založené na jazyce C. Aplikace musí být distribuovány stejným vývojářem Apple.

Pokyny pro jednotné přihlašování mezi aplikacemi v aplikacích ADAL a MSAL najdete v tématu [jednotné přihlašování mezi aplikacemi ADAL a MSAL v MacOS a iOS](sso-between-adal-msal-apps-macos-ios.md) .

## <a name="silent-sso-between-apps"></a>Tiché jednotné přihlašování mezi aplikacemi

MSAL podporuje sdílení SSO prostřednictvím přístupových skupin řetězce pro iOS.

Pokud chcete povolit jednotné přihlašování napříč vašimi aplikacemi, musíte provést následující kroky, které jsou podrobněji vysvětleny níže:

1. Ujistěte se, že všechny vaše aplikace používají stejné ID klienta nebo ID aplikace.
1. Zajistěte, aby všechny vaše aplikace sdílely stejný podpisový certifikát od společnosti Apple, takže můžete sdílet řetězce klíčů.
1. Pro každou aplikaci si vyžádejte stejnou nárok na řetězec klíčů.
1. Řekněte sadám MSAL SDK o sdíleném řetězci klíčů, které chceme použít, pokud se liší od výchozího.

### <a name="use-the-same-client-id-and-application-id"></a>Použít stejné ID klienta a ID aplikace

Aby mohla aplikace od Microsoftu zjistit, které aplikace můžou tyto tokeny sdílet, musí tyto aplikace sdílet stejné ID klienta nebo ID aplikace. Toto je jedinečný identifikátor, který vám byl poskytnut při registraci první aplikace na portálu.

Způsob, jakým Microsoft Identity Platform oznamuje aplikacím, které používají stejné ID aplikace, je pomocí **identifikátorů URI pro přesměrování**. Každá aplikace může obsahovat více identifikátorů URI pro přesměrování v portálu pro registraci. Každá aplikace v sadě bude mít jiný identifikátor URI pro přesměrování. Například:

Identifikátor URI pro přesměrování app1: `msauth.com.contoso.mytestapp1://auth`  
Identifikátor URI pro přesměrování app2: `msauth.com.contoso.mytestapp2://auth`  
Identifikátor URI pro přesměrování APP3: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Formát identifikátorů URI pro přesměrování musí být kompatibilní s formátem, který podporuje MSAL, který je popsán v části [požadavky formátu identifikátoru URI přesměrování MSAL](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Nastavení sdílení řetězce klíčů mezi aplikacemi

Pokud chcete povolit sdílení řetězce klíčů, podívejte se do článku věnovaném [Přidání schopností](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) společnosti Apple. Důležité je, abyste se rozhodli, co chcete volat do řetězce klíčů, a přidat tuto možnost do všech aplikací, které budou součástí jednotného přihlašování.

Pokud máte oprávnění nastavené správně, zobrazí se `entitlements.plist` v adresáři projektu soubor, který obsahuje něco podobného jako v tomto příkladu:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Přidat novou skupinu řetězce klíčů

Přidejte novou skupinu řetězce klíčů do **možností** projektu. Skupina řetězce klíčů by měla být:
* `com.microsoft.adalcache` v iOS 
* `com.microsoft.identity.universalstorage` v macOS.

![příklad řetězce klíčů](media/single-sign-on-macos-ios/keychain-example.png)

Další informace najdete v tématu [skupiny řetězce klíčů](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Konfigurace objektu aplikace

Jakmile budete mít v každé aplikaci povolený nárok na řetězec řetězce klíčů a jste připraveni použít jednotné přihlašování, nakonfigurujte `MSALPublicClientApplication` pomocí přístupové skupiny pro řetězce klíčů jako v následujícím příkladu:

Cíl-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

SWIFT

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> Když ve svých aplikacích sdílíte řetězce klíčů, může kterákoli aplikace odstraňovat uživatele nebo dokonce i všechny tokeny v rámci vaší aplikace.
> To platí zejména v případě, že máte aplikace, které na tokeny spoléhají na práci na pozadí.
> Sdílení řetězce klíčů znamená, že musíte být velmi opatrní, pokud vaše aplikace používá operace odebrání sady Microsoft Identity SDK.

A to je vše! Sada Microsoft Identity SDK teď bude sdílet přihlašovací údaje napříč všemi vašimi aplikacemi. Seznam účtů se taky bude sdílet mezi instancemi aplikací.

## <a name="sso-through-authentication-broker-on-ios"></a>Jednotné přihlašování prostřednictvím zprostředkovatele ověřování v iOS

MSAL poskytuje podporu pro zprostředkované ověřování pomocí Microsoft Authenticator. Microsoft Authenticator poskytuje jednotné přihlašování pro zařízení zaregistrovaná v AAD a také pomáhá vaší aplikaci pořídit zásady podmíněného přístupu.

Následující postup slouží k povolení jednotného přihlašování pomocí zprostředkovatele ověřování pro vaši aplikaci:

1. Zaregistrujte formát identifikátoru URI přesměrování kompatibilního s zprostředkovatelem pro aplikaci v souboru info. plist vaší aplikace. Formát identifikátoru URI přesměrování kompatibilního s zprostředkovatelem je `msauth.<app.bundle.id>://auth` . Nahraďte '<app.bundle.id>' ' ID sady prostředků vaší aplikace. Například:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Do souboru info. plist vaší aplikace přidejte následující schémata `LSApplicationQueriesSchemes` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Přidejte do `AppDelegate.m` souboru následující soubor pro zpracování zpětných volání:

    Cíl-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    SWIFT
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Pokud používáte Xcode 11**, měli byste místo toho umístit MSAL zpět do `SceneDelegate` souboru.
Pokud podporujete UISceneDelegate i UIApplicationDelegate pro zajištění kompatibility se staršími systémy iOS, je nutné MSAL zpětné volání umístit do obou souborů.

Cíl-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

SWIFT

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)