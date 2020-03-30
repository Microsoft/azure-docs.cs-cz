---
title: Konfigurace automatického připojatá v macOS a iOS
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování (SSO) v macOS a iOS.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 91a55520b37c549c8f1d94ba6cf08ecd24db85b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262448"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Postup: Konfigurace přizpůsobovat služby Přikonfiguraci v macOS a iOS

Microsoft Authentication Library (MSAL) pro macOS a iOS podporuje jednotné přihlašování (SSO) mezi aplikacemi a prohlížeči macOS/iOS. Tento článek popisuje následující scénáře přispojené správě a zabezpečení:

- [Tiché přihlašovat k přihlašuji mezi více aplikacemi](#silent-sso-between-apps)

Tento typ přisychovaného zařízení funguje mezi více aplikacemi distribuovanými stejným vývojářem Apple. Poskytuje tiché sbozí (to znamená, že uživatel není vyzván k zadání přihlašovacích údajů) čtením tokenů aktualizace napsaných jinými aplikacemi z řetězce klíčů a jejich výměnou za přístupové tokeny tiše.  

- [Přizpůsobovat služby spoje prostřednictvím zprostředkovatele ověřování](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Tento tok není k dispozici v macOS.

Společnost Microsoft poskytuje aplikace, nazývané zprostředkovatelé, které umožňují přihlašování k objektům zabezpečení mezi aplikacemi od různých dodavatelů, pokud je mobilní zařízení registrované ve službě Azure Active Directory (AAD). Tento typ spřihlašovacího systému vyžaduje, aby byla na zařízení uživatele nainstalována zprostředkovatelská aplikace.

- **SSO mezi MSAL a Safari**

SSO je dosaženo prostřednictvím [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) třídy. Používá existující stav přihlášení z jiných aplikací a prohlížeče Safari. Není omezena na aplikace distribuované stejným vývojářem Apple, ale vyžaduje určitou interakci uživatele.

Pokud k přihlašování uživatelů používáte výchozí webové zobrazení v aplikaci, získáte automatické přihlašování mezi aplikacemi založenými na MSAL a Safari. Další informace o webových zobrazeních, která aplikace MSAL podporuje, naleznete na stránce [Vlastní prohlížeče a zobrazení webu](customize-webviews.md).

> [!IMPORTANT]
> Tento typ služby ASO není momentálně v macOS dostupný. MSAL na macOS podporuje pouze WKWebView, který nemá podporu s přihlašuje s Safari. 

- **Tiché přihlašuje mezi aplikacemi ADAL a MSAL macOS/iOS**

MSAL Objective-C podporuje migraci a semafory s aplikacemi založenými na ADAL Objective-C. Aplikace musí být distribuovány stejným vývojářem Apple.

Pokyny pro služby SSO mezi aplikacemi [ADAL a MSAL v systému macOS a iOS](sso-between-adal-msal-apps-macos-ios.md) najdete v tématu SSO mezi aplikacemi založenými na ADAL a MSAL.

## <a name="silent-sso-between-apps"></a>Tiché přihlašuje služby ASO mezi aplikacemi

MSAL podporuje sdílení spřimit pomocí přístupových skupin klíčenky iOS.

Chcete-li povolit přihlašování k vlastnímu přihlašování ve vašich aplikacích, budete muset provést následující kroky, které jsou vysvětleny podrobněji níže:

1. Ujistěte se, že všechny vaše aplikace používají stejné ID klienta nebo ID aplikace.
1. Ujistěte se, že všechny vaše aplikace sdílejí stejný podpisový certifikát od společnosti Apple, abyste mohli sdílet klíčenky.
1. Vyžádejte si stejný nárok na klíčenku pro každou z vašich aplikací.
1. Informujte sady MSAL SDK o sdílené montovny, kterou chcete použít, pokud se liší od výchozí.

### <a name="use-the-same-client-id-and-application-id"></a>Použití stejného ID klienta a ID aplikace

Aby platforma identit microsoftu věděla, které aplikace mohou sdílet tokeny, musí tyto aplikace sdílet stejné ID klienta nebo ID aplikace. Jedná se o jedinečný identifikátor, který vám byl poskytnut při registraci první aplikace na portálu.

Způsob, jakým platforma identit společnosti Microsoft říká aplikacím, které používají stejné ID aplikace, je podle jejich **identifikátorů URI přesměrování**. Každá aplikace může mít více redirect URI registrované v onboarding portálu. Každá aplikace ve vaší sadě bude mít jiný identifikátor URI přesměrování. Například:

Identifikátor URI přesměrování aplikace App1:`msauth.com.contoso.mytestapp1://auth`  
Identifikátor URI přesměrování aplikace 2:`msauth.com.contoso.mytestapp2://auth`  
Identifikátor URI přesměrování aplikace App3:`msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Formát uripřesměrování musí být kompatibilní s formátem MSAL podporuje, který je dokumentován v [požadavcích formátu Uri přesměrování MSAL](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Nastavení sdílení klíčenky mezi aplikacemi

Pro povolení sdílení klíčenky v článku [Přidání schopností](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) společnosti Apple. Důležité je, že se rozhodnete, co chcete, aby byl váš řetězec klíčů volán, a přidejte tuto funkci do všech aplikací, které budou zapojeny do automatického přihlašování.

Pokud máte správně nastavené nároky, zobrazí `entitlements.plist` se v adresáři projektu soubor, který obsahuje něco jako v tomto příkladu:

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

#### <a name="add-a-new-keychain-group"></a>Přidání nové skupiny klíčů

Přidejte novou skupinu klíčů do projektu **Capabilities**. Skupina klíčenky by měla být:
* `com.microsoft.adalcache`o iOS 
* `com.microsoft.identity.universalstorage`na macOS.

![příklad řetězce klíčů](media/single-sign-on-macos-ios/keychain-example.png)

Další informace naleznete v tématu [skupiny řetězce klíčů](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Konfigurace aplikačního objektu

Jakmile budete mít oprávnění řetězce klíčů povolena v každé z vašich aplikací `MSALPublicClientApplication` a jste připraveni k použití přihlašování, nakonfigurujte s vaší skupiny přístupu řetězce klíčů jako v následujícím příkladu:

Cíl-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

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
> Když sdílíte klíčenku napříč aplikacemi, každá aplikace může odstranit uživatele nebo dokonce všechny tokeny v celé vaší aplikaci.
> To je zvláště působivý, pokud máte aplikace, které spoléhají na tokeny dělat práci na pozadí.
> Sdílení klíčenky znamená, že musíte být velmi opatrní, když vaše aplikace používá operace odebrání identity Microsoftu.

A to je vše! Sada Microsoft identity SDK bude nyní sdílet přihlašovací údaje ve všech vašich aplikacích. Seznam účtů bude také sdílen mezi instancemi aplikace.

## <a name="sso-through-authentication-broker-on-ios"></a>Přizpůsobovat služby a so prostřednictvím zprostředkovatele ověřování v iOS

Služba MSAL poskytuje podporu pro zprostředkované ověřování pomocí aplikace Microsoft Authenticator. Microsoft Authenticator poskytuje služby SSO pro zařízení registrovaná AAD a také pomáhá vaší aplikaci dodržovat zásady podmíněného přístupu.

Následující kroky jsou, jak povolíte přisyťující služby Přistaň pomocí zprostředkovatele ověřování pro vaši aplikaci:

1. Zaregistrujte formát IDENTIFIKÁTOR URI kompatibilní s brokerem pro aplikaci v seznamu Info.plist vaší aplikace. Formát identifikátoru URI kompatibilní `msauth.<app.bundle.id>://auth`s zprostředkovatelem je . Nahraďte<app.bundle.id>'' id sady. Například:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Přidejte do aplikace Info.plist následující `LSApplicationQueriesSchemes`schémata pod :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Přidejte do `AppDelegate.m` souboru následující příkazy pro zpracování zpětná volání:

    Cíl-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Pokud používáte Xcode 11**, měli byste místo `SceneDelegate` toho umístit zpětné volání MSAL do souboru.
Pokud podporujete uISceneDelegate a UIApplicationDelegate pro kompatibilitu se starším iOS, bude nutné umístit zpětné volání MSAL do obou souborů.

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

Swift:

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