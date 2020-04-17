---
title: Microsoft identity platformy iOS a macOS rychlý start | Azure
description: Přečtěte si, jak přihlašovat uživatele a dotazovat se microsoft graphu v aplikaci pro iOS nebo macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 47485d8d9007a6cf6432b7bf401c7c1c34a9863a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536127"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Úvodní příručka: Přihlášení uživatelů a volání rozhraní Microsoft Graph API z aplikace pro iOS nebo macOS

Tento rychlý start obsahuje ukázku kódu, která ukazuje, jak nativní aplikace pro iOS nebo macOS může používat platformu identit Microsoftu k přihlášení osobních, pracovních a školních účtů, získání přístupového tokenu a volání rozhraní Microsoft Graph API.

Tento rychlý start se vztahuje na aplikace pro iOS i macOS. Některé kroky jsou potřeba pouze pro aplikace pro iOS. Tyto kroky volají, že jsou pouze pro iOS.

![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Požadavky**
> * XCode 10+
> * iOS 10+
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Expresní] [Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Ruční] [Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a stažení ukázky kódu
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Chcete-li aplikaci zaregistrovat,
> 1. Přejděte na nové [podokno Registrace aplikací azure.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs)
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přejděte na platformu identit Microsoftpro vývojáře [Registrace aplikací](https://aka.ms/MobileAppReg) stránky.
> 1. Vyberte **možnost Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>      - V části **Název** zadejte smysluplný název aplikace, který se uživatelům aplikace zobrazí při přihlášení nebo souhlasu s vaší aplikací.
>      - Přeskočte ostatní konfigurace na této stránce.
>      - Vyberte `Register`.
> 1. V části **Spravovat** `Authentication`  >  `Add Platform`  >  `iOS`vyberte .
>      - Zadejte ***identifikátor sady pro*** vaši aplikaci. Identifikátor svazku je pouze jedinečný řetězec, který jednoznačně `com.<yourname>.identitysample.MSALMacOS`identifikuje vaši aplikaci, například . Poznamenejte si hodnotu, kterou používáte.
>      - Všimněte si, že konfigurace iOS je také použitelná pro aplikace macOS.
> 1. V `Configure` tomto rychlém startu vyberte a uložte podrobnosti ***o konfiguraci služby MSAL*** na později.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurace aplikace
> Pro ukázku kódu pro tento rychlý start do práce, je třeba přidat přesměrování URI kompatibilní s zprostředkovatele ověřování.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-ios/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-sample-project"></a>Krok 2: Stažení ukázkového projektu

- [Stažení ukázky kódu pro iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Stažení ukázky kódu pro macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Krok 3: Instalace závislostí

V okně terminálu přejděte do složky s `pod install` ukázkou staženého kódu a spusťte instalaci nejnovější knihovny MSAL.

#### <a name="step-4-configure-your-project"></a>Krok 4: Konfigurace projektu

> [!div renderon="docs"]
> Pokud jste vybrali možnost 1 výše, můžete tyto kroky přeskočit.

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahujte soubor zip a otevřete projekt v XCode.
> 1. Upravte **ViewController.swift** a nahraďte řádek začínající 'let clientID' s následujícím fragmentem kódu. Nezapomeňte aktualizovat hodnotu `kClientID` pomocí ID klienta, které jste uložili při registraci aplikace na portálu dříve v rychlém startu:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Upravte **ViewController.swift** a nahraďte řádek začínající příkazem "let kAuthority" následujícím fragmentem kódu:
>    ```swift
>    let kAuthority = "Enter_the_Authority_Endpoint_Host_HereEnter_the_Tenant_Info_Here"
>    ```
> 1. Upravte **ViewController.swift** a nahraďte řádek začínající "let KGraphEndpoint" následujícím fragmentem kódu:
>    ```swift
>    let kGraphEndpoint = "Enter_the_MS_Graph_Endpoint_Host_Here"
>    ```
> 1. Otevřete nastavení projektu. V části **Identita** zadejte **identifikátor sady,** který jste zadali na portálu.
> 1. Pro jenom pro iOS klikněte pravým tlačítkem myši na **Info.plist** a vyberte **Otevřít jako** > **zdrojový kód**.
> 1. Pouze pro iOS, pod kořenovým uzlem dict, nahraďte `CFBundleURLSchemes` ***ID sady,*** které jste zadali na portálu.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 1. Sestavte & spusťte aplikaci!
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
>
> 1. Extrahujte soubor zip a otevřete projekt v XCode.
> 1. Upravte **ViewController.swift** a nahraďte řádek začínající 'let clientID' s následujícím fragmentem kódu. Nezapomeňte aktualizovat hodnotu `kClientID` pro pomocí id klienta, které jste uložili při registraci aplikace na portálu dříve v tomto rychlém startu:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Pokud vytváříte aplikaci pro [národní cloudy Azure AD](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints), nahraďte řádek začínající "let kGraphEndpoint" a "let kAuthority" se správnými koncovými body. Pro globální přístup použijte výchozí hodnoty:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Ostatní koncové body jsou dokumentovány [zde](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints). Chcete-li například spustit rychlý start s Azure AD Germany, použijte následující:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Otevřete nastavení projektu. V části **Identita** zadejte **identifikátor sady,** který jste zadali na portálu.
> 1. Pro jenom pro iOS klikněte pravým tlačítkem myši na **Info.plist** a vyberte **Otevřít jako** > **zdrojový kód**.
> 1. Pro jenom pro iOS, pod `Enter_the_bundle_Id_Here` kořenovým uzlem dict, nahraďte ***id sady,*** které jste použili na portálu.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Sestavte & spusťte aplikaci!

## <a name="more-information"></a>Další informace

Další informace o tomto rychlém startu najdete v následujících částech.

### <a name="get-msal"></a>Získat MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) je knihovna používaná k přihlášení uživatelů a vyžádání tokenů používaných pro přístup k rozhraní API chráněnému platformou identit microsoftu. Knihovnu MSAL můžete do své aplikace přidat následujícím způsobem:

```
$ vi Podfile

```
Přidejte následující do tohoto podfile (s cílem projektu):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Spustit příkaz k instalaci KakaoPods:

```pod install```

### <a name="initialize-msal"></a>Inicializovat MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```swift
import MSAL
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Kde: ||
> |---------|---------|
> | `clientId` | ID aplikace z aplikace zaregistrované na webu *portal.azure.com* |
> | `authority` | Koncový bod platformy identity Microsoftu. Ve většině případů to bude *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Identifikátor URI přesměrování aplikace. Můžete předat 'nula' použít výchozí hodnotu, nebo vlastní přesměrování URI. |

### <a name="for-ios-only-additional-app-requirements"></a>Jenom pro iOS, další požadavky na aplikace

Vaše aplikace musí mít také `AppDelegate`následující v aplikaci . To umožňuje msal sdk zpracovat odpověď tokenu z aplikace zprostředkovatele ověření při ověřování.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> V iOS 13+, `UISceneDelegate` pokud `UIApplicationDelegate`přijmete místo , `scene:openURLContexts:` umístěte tento kód do zpětného volání místo (Viz [dokumentace Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Pokud podporujete uISceneDelegate a UIApplicationDelegate pro kompatibilitu se starším iOS, musí být zpětné volání MSAL umístěno na obou místech.

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

Nakonec musí mít vaše `LSApplicationQueriesSchemes` aplikace položku ve vašem `CFBundleURLTypes` ***info.plist*** vedle . Vzorek je dodáván s tímto součástí.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Přihlášení uživatelů & tokeny požadavků

Knihovna MSAL používá k získání tokenů dvě metody: `acquireToken` a `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: Získejte token interaktivně

Některé situace vyžadují, aby uživatelé komunikovali s platformou identit microsoftu. V těchto případech může být koncový uživatel požádán, aby si vybral svůj účet, zadali své přihlašovací údaje nebo souhlasili s oprávněními vaší aplikace. Například:

* Při prvním přihlášení uživatele k aplikaci
* Pokud uživatel resetuje své heslo, bude muset zadat své přihlašovací údaje.
* Když vaše aplikace poprvé požaduje přístup k prostředku
* Pokud jsou vyžadovány vícefaktorové povolení nebo jiné zásady podmíněného přístupu

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Kde:||
> |---------|---------|
> | `scopes` | Obsahuje požadované obory (to `[ "user.read" ]` znamená pro `[ "<Application ID URL>/scope" ]` Microsoft Graph nebo`api://<Application ID>/access_as_user`pro vlastní webová rozhraní API ( ) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Získejte přístupový token tiše

Aplikace by neměly vyžadovat, aby se uživatelé přihlašovali pokaždé, když požadují token. Pokud se uživatel již přihlásil, tato metoda umožňuje aplikacím bezobslužně požadovat tokeny.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Kde: ||
> |---------|---------|
> | `scopes` | Obsahuje požadované obory (to `[ "user.read" ]` znamená pro `[ "<Application ID URL>/scope" ]` Microsoft Graph nebo`api://<Application ID>/access_as_user`pro vlastní webová rozhraní API ( ) |
> | `account` | Účet token je požadováno pro. Tento rychlý start je o aplikaci jednoho účtu. Pokud chcete vytvořit aplikaci s více s kládami, budete muset definovat logiku, abyste určili, který účet se má použít pro žádosti o tokeny, které používají `accountsFromDeviceForParameters:completionBlock:` a předávají správné`accountIdentifier` |

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si kurz pro iOS a macOS pro kompletní podrobný návod na vytváření aplikací, včetně úplného vysvětlení tohoto rychlého startu.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Naučte se, jak vytvořit aplikaci použitou v tomto rychlém startu

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro volání grafů pro iOS a macOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
